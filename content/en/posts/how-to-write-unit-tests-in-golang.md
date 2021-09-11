---
title: "How to write unit tests in the Go programming language?"
date: 2021-09-01T22:00:03+00:00
tags: ["unit test","Go"]
author: "Jucelio Floresta"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
disableHLJS: false # to disable highlightjs
disableShare: true
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---

## Why should you write tests?

Programming is not an easy task, write test is neither, but part of the software development process is writing tests. Testing our code is a good way to ensure quality and improve reliability, [Introducing Go, 2016][book-introducing-go]. When we talk about testing, we're implicitly referring to automated testing, it's the practice of writing small programs that check that code behaves as expected for defined entries, The Go Programming Language, 2016][book-the-go-programming-language].

## How are tests written in Golang?

The Golang standard library [_testing_][gopkg-testing] package provides support for writing automated tests. A file with the suffix `_test.go` must exist, for example, to test `sum.go`, a `sum_test.go` file created.

Inside this file with suffix `_test.go` the test functions are defined, with the signature `func TestFuncName(t *testing.T)`, where the parameter _t_ is a reference to the type _testing.T_, imported from the _testing_ package, the prefix _Test_ is a requirement in the function name, to test the function `Sum(x, y) result` of the math package, we can create a test function with the following signature, `func TestSum(t *testing.T)`.


```go
func TestSum(t *testing.T) {
    x := 1
    y := 2
    expected := 3
    result := math.Sum(x, y)

    if result != expected {
        t.Errorf(
            "Expected that %d+%d is %d, but actual take %d",
            x, y, expected, result)
    }
}
```

In Go, it is common to use a test table to define test cases, a _struct_ is created, abstracting all inputs and expected output, likes in the sum test, there would be two numbers to be added, and the expected result of the sum.

Instead of calling the sum function, once, and validating its result, using a test table, a loop is defined to go through the entire table, calling the function several times.

```go
func TestSum(t *testing.T) {
    testTable := []struct {
        x int
        y int
        expected int
    }{
        {1, 1, 2},
        {0, 1, 1},
        {0, 0, 0},
        {0, -1, -1},
        {-1, -1, -2},
    }

    for _, test := range testTable {
        result := math.Sum(test.x, text.y)
        if result != tabela.expected {
            t.Errorf(
                "Expected that %d+%d is %d, but actual take %d",
                test.x, test.y, test.expected, result)
        }
    }
}
```

WARNING: Go is very strict when naming a package, the only exception for compound names is the _\_test_ suffix, as in `math_test` package, so anyone using the math package will not have visibility of the test functions.

When everything goes as expected, there was no error, the tests are said to have passed, so in Go, if you don't call any method `t.Fail()` or `t.Error()` or `Errorf()` to format the error, if none of these functions are invoked, the tests will pass.

If you prefer to use an assertion line of reasoning, there is a package called [_testify_][gopkg-testify], which provides helper functions validations, and comparisons.


## How to run tests in Golang?

The `$go test` tool checks all files ending with `_test.go` for test functions, generates a temporary core package that uses it properly, runs the tests, reports the results, and then cleans them up, [The Go Programming Language, 2016][book-the-go-programming-language].

```shell
$ go test ./...

?   	example.com/test	[no test files]
ok  	example.com/test/math	0.001s
```

If the tests do not pass, a _FAIL_ message will be displayed, showing the name of the failed test function, and if any message's error has been implemented will show it, explaining the reasons for the error, will also appear in the output of the _stdout_. terminal.

```shell
$ go test ./...

--- FAIL: TestSum (0.00s)
    sum_test.go:25: Expected that 1+1 is 2, but actual take 4
    sum_test.go:25: Expected that 0+1 is 1, but actual take 3
    sum_test.go:25: Expected that 0+0 is 0, but actual take 2
    sum_test.go:25: Expected that 0+-1 is -1, but actual take 1
    sum_test.go:25: Expected that -1+-1 is -2, but actual take 0
FAIL
FAIL	example.com/test/math	0.001s
FAIL
```

## Conclusions!

Using test tables is more complex than writing multiple test functions to cover possible scenarios, but it reduces the code's amount.

You don't need any packages outside the standard library to write tests in Golang, the testing package provides what you need. The testify package can be used in more complex cases, with more validations, and to work better with an error message and log formatting.

## References

- [Book: The Go Programming Language, Chapter 11, Tests.][book-the-go-programming-language]
- [Book: Mastering Go, chapter 11, Code Testing, Optimization, and Profiling][book-mastering-go]
- [Book: Introducing Go, chapter 9, Testing][book-introducing-go]
- [Website: Standard library, testing package][gopkg-testing]
- [Website: Github stretchr, testify package][gopkg-testify]

[gopkg-testing]:https://golang.org/pkg/testing/
[gopkg-testify]:https://pkg.go.dev/github.com/stretchr/testify
[book-mastering-go]:https://www.amazon.com.br/Mastering-production-applications-concurrency-structures-ebook/dp/B07WC24RTQ
[book-the-go-programming-language]:https://www.amazon.com.br/Programming-Language-Addison-Wesley-Professional-Computing-ebook/dp/B0184N7WWS
[book-introducing-go]:https://www.amazon.com.br/Introducing-Go-Reliable-Scalable-Programs-ebook/dp/B01AB3G496
