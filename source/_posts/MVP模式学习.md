---
title: MVP模式学习
date: 2017-03-17 09:31:11
tags: [Android,设计模式]
categories: Android
---

MVP模式是基于MVC模式在Android上面的一个变种，可以参考谷歌的官方架构项目[Android-Architecture](https://github.com/googlesamples/android-architecture)。在 MVC 模式中，Activity 应该是属于 View 这一层。而实质上，它既承担了 View，同时也包含一些 Controller 的东西在里面。这对于开发与维护来说不太友好，耦合度大高了。把 Activity 的 View 和 Controller 抽离出来就变成了 View 和 Presenter，这就是 MVP 模式。


View只负责处理视图状态
Presenter处理逻辑
Contract 连接View和Presenter

针对各个界面：




	
	public interface BasePresenter {
	
	    void subscribe();
	
	    void unsubscribe();
	
	}
	
	public interface BaseView<T> {

    	void setPresenter(T presenter);

	}




	public interface AddEditTaskContract {

	    interface View extends BaseView<Presenter> {
	
	        void showEmptyTaskError();
	
	        void showTasksList();
	
	        void setTitle(String title);
	
	        void setDescription(String description);
	
	        boolean isActive();
	    }
	
	    interface Presenter extends BasePresenter {
	
	        void saveTask(String title, String description);
	
	        void populateTask();
	
	        boolean isDataMissing();
	    }
	}
	
	
		public class AddEditTaskPresenter implements AddEditTaskContract.Presenter {
	
	    @NonNull
	    private final TasksDataSource mTasksRepository;
	
	    @NonNull
	    private final AddEditTaskContract.View mAddTaskView;
	
	    @NonNull
	    private final BaseSchedulerProvider mSchedulerProvider;
	
	    @Nullable
	    private String mTaskId;
	
	    private boolean mIsDataMissing;
	
	    @NonNull
	    private CompositeSubscription mSubscriptions;
	
	    /**
	     * Creates a presenter for the add/edit view.
	     *
	     * @param taskId                 ID of the task to edit or null for a new task
	     * @param tasksRepository        a repository of data for tasks
	     * @param addTaskView            the add/edit view
	     * @param shouldLoadDataFromRepo whether data needs to be loaded or not (for config changes)
	     */
	    public AddEditTaskPresenter(@Nullable String taskId, @NonNull TasksDataSource tasksRepository,
	                                @NonNull AddEditTaskContract.View addTaskView, boolean shouldLoadDataFromRepo,
	                                @NonNull BaseSchedulerProvider schedulerProvider) {
	        mTaskId = taskId;
	        mTasksRepository = checkNotNull(tasksRepository);
	        mAddTaskView = checkNotNull(addTaskView);
	        mIsDataMissing = shouldLoadDataFromRepo;
	
	        mSchedulerProvider = checkNotNull(schedulerProvider, "schedulerProvider cannot be null!");
	
	        mSubscriptions = new CompositeSubscription();
	        mAddTaskView.setPresenter(this);
	    }
	
	    @Override
	    public void subscribe() {
	        if (!isNewTask() && mIsDataMissing) {
	            populateTask();
	        }
	    }
	
	    @Override
	    public void unsubscribe() {
	        mSubscriptions.clear();
	    }
	
	    @Override
	    public void saveTask(String title, String description) {
	        if (isNewTask()) {
	            createTask(title, description);
	        } else {
	            updateTask(title, description);
	        }
	    }
	
	    @Override
	    public void populateTask() {
	        if (isNewTask()) {
	            throw new RuntimeException("populateTask() was called but task is new.");
	        }
	        mSubscriptions.add(mTasksRepository
	                .getTask(mTaskId)
	                .subscribeOn(mSchedulerProvider.computation())
	                .observeOn(mSchedulerProvider.ui())
	                .subscribe(
	                        // onNext
	                        task -> {
	                            if (mAddTaskView.isActive()) {
	                                mAddTaskView.setTitle(task.getTitle());
	                                mAddTaskView.setDescription(task.getDescription());
	
	                                mIsDataMissing = false;
	                            }
	                        }, // onError
	                        __ -> {
	                            if (mAddTaskView.isActive()) {
	                                mAddTaskView.showEmptyTaskError();
	                            }
	                        }));
	    }
	
	    @Override
	    public boolean isDataMissing() {
	        return mIsDataMissing;
	    }
	
	    private boolean isNewTask() {
	        return mTaskId == null;
	    }
	
	    private void createTask(String title, String description) {
	        Task newTask = new Task(title, description);
	        if (newTask.isEmpty()) {
	            mAddTaskView.showEmptyTaskError();
	        } else {
	            mTasksRepository.saveTask(newTask);
	            mAddTaskView.showTasksList();
	        }
	    }
	
	    private void updateTask(String title, String description) {
	        if (isNewTask()) {
	            throw new RuntimeException("updateTask() was called but task is new.");
	        }
	        mTasksRepository.saveTask(new Task(title, description, mTaskId));
	        mAddTaskView.showTasksList(); // After an edit, go back to the list.
	    }
	}