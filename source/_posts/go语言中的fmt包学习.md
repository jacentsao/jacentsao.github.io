---
title: go语言中的fmt包学习
date: 2017-01-29 10:55:38
tags: Golang
categories: Golang 
---
### go doc fmt list all the data below 
#### print

The verbs:

General:

    %v	the value in a default format
    	when printing structs, the plus flag (%+v) adds field names
    %#v	a Go-syntax representation of the value
    %T	a Go-syntax representation of the type of the value
    %%	a literal percent sign; consumes no value

Boolean:

    %t	the word true or false

Integer:

    %b	base 2
    %c	the character represented by the corresponding Unicode code point
    %d	base 10
    %o	base 8
    %q	a single-quoted character literal safely escaped with Go syntax.
    %x	base 16, with lower-case letters for a-f
    %X	base 16, with upper-case letters for A-F
    %U	Unicode format: U+1234; same as "U+%04X"

Floating-point and complex constituents:

    %b	decimalless scientific notation with exponent a power of two,
    	in the manner of strconv.FormatFloat with the 'b' format,
    	e.g. -123456p-78
    %e	scientific notation, e.g. -1.234456e+78
    %E	scientific notation, e.g. -1.234456E+78
    %f	decimal point but no exponent, e.g. 123.456
    %F	synonym for %f
    %g	%e for large exponents, %f otherwise
    %G	%E for large exponents, %F otherwise

String and slice of bytes (treated equivalently with these verbs):

    %s	the uninterpreted bytes of the string or slice
    %q	a double-quoted string safely escaped with Go syntax
    %x	base 16, lower-case, two characters per byte
    %X	base 16, upper-case, two characters per byte

Pointer:

    %p	base 16 notation, with leading 0x

There is no 'u' flag. Integers are printed unsigned if they have unsigned
type. Similarly, there is no need to specify the size of the operand (int8,
int64).

The default format for %v is:

    bool:                    %t
    int, int8 etc.:          %d
    uint, uint8 etc.:        %d, %x if printed with %#v
    float32, complex64, etc: %g
    string:                  %s
    chan:                    %p
    pointer:                 %p
    
Control width and precision:
    
     %f     default width, default precision
    %9f    width 9, default precision
    %.2f   default width, precision 2
    %9.2f  width 9, precision 2
    %9.f   width 9, precision 0
    
#### 一些小区别
函数 fmt.Sprintf 与 Printf 的作⽤是完全相同的，不过前者将格式化后的字符串以返 回值的形式返回给调⽤者，因此你可以在程序中使⽤包含变量的字符串
