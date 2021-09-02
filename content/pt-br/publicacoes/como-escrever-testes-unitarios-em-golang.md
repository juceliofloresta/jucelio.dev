---
title: "Como escrever testes unitários na linguagem de programação Go?"
date: 2021-09-01T22:00:03+00:00
tags: ["testes unitários","Go"]
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

## Por que você deveria escrever testes?

Programar não é uma tarefa fácil, escrever testes também não, mas parte do
processo de desenvolvimento de software é escrever testes. Os testes do nosso
código são uma boa maneira de garantir a qualidade e melhorar a confiabilidade,
[Introducing Go, 2016][book-introducing-go]. Quando falamos em testes, estamos
nos referindo implicitamente a testes automatizados, é a prática de escrever
pequenos programas que verificam se o código se comporta conforme o esperado
para determinadas entradas, [The Go Programming Language,
2016][book-the-go-programming-language].

## Como são escritos testes em Golang?

O pacote [_testing_][gopkg-testing] da biblioteca padrão de Golang, provê
suporte a escrita testes automatizados. Um arquivo com o sufixo `_test.go` deve
existir, por exemplo, para testar `sum.go`, é criado um arquivo `sum_test.go`.

Dentro deste arquivo com sufixo `_test.go` são definidas as funções de teste,
com a assinatura `func TestFuncName(t *testing.T)`, onde o parametro _t_ é uma
referência ao tipo _testing.T_, importado do pacote _testing_, o prefixo _Test_
é um requerimento no nome da função, para testar a função `Sum(x, y) result` do
pacote _math_, podemos criar uma função de teste com a seguinte assinatura,
`func TestSum(t *testing.T)`.

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

Em Go, é comum o uso de uma tabela de testes para definir casos de testes, uma
_struct_ é criada, abstraindo todas as entradas e a saída esperada, por exemplo
no teste de soma, teriam dois números a serem somados, e o resultado esperado
da soma.

Ao invés de chamar a função soma, uma única vez, e validar seu resultado, com o
uso de tabela de testes, um laço é definido para percorrer toda a tabela,
chamando diversas vezes a função.

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

AVISO: Go é bem restrito quanto ao nomear um pacote, a única excessão para
nomes compostos é o sufixo _\_test_, como em `package math_test`, dessa forma
quem estiver usando o pacote _math_ não terá visibilidade das funções de teste.

Quando tudo ocorreu conforme esperado, não houve nenhum erro, é dito que _os
testes passaram_, por isso em Go, se não chamarmos nenhum método `t.Fail()` ou
`t.Error` ou `Errorf()` para formatar o erro, se nenhuma destas funcões forem
invocadas, os testes passarão.

Caso você prefira utilizar a linha de racíocio de asserções, existe um pacote
chamado [_testify_][gopkg-testify], que provê funções auxiliares em validações,
e comparações.

## Como rodar os testes em Golang?

A ferramenta de teste `$ go test` verifica todos os arquivos que terminam com
`_test.go` em busca de funções de teste, gera um pacote principal temporário
que o utiliza da maneira adequada, executa os testes, relata os resultados e,
em seguida, os limpa, [The Go Programming Language,
2016][book-the-go-programming-language].

```shell
$ go test ./...

?   	example.com/test	[no test files]
ok  	example.com/test/math	0.001s
```

Caso os testes _não passem_, uma mensagem _FAIL_ será exibida, apresentando o
nome da função de teste de falhou, e caso tenha sido implementado, alguma
mensagem de erro, explicando os motivos do erro, ela também vai aparecer no na
saída do terminal _stdout_.

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

## Conclusões!

Utilizar tabela de testes é mais complexo do que escrever várias funções de
testes para cobrir os possíveis cenários, mas diminui a quantidade de código.

Não é necessário nenhum pacote externo à biblioteca padrão para escrever testes
em Golang, o pacote _testing_ provê o necessário. O pacote _testify_ pode ser
usado em casos mais complexos, com mais validações, e para trabalhar melhor com
formatações de mensagens de erro e logs.

## Referências

- [Livro: A Linguagem de Programção Go, capítulo 11, Testes.][book-the-go-programming-language]
- [Livro: Mastering Go, chapter 11, Code Testing, Optimization, and Profiling][book-mastering-go]
- [Livro: Introducing Go, chapter 9, Testing][book-introducing-go]
- [Página Web: Biblioteca padrão, pacote testing][gopkg-testing]
- [Página Web: Github stretchr, pacote testify][gopkg-testify]

[gopkg-testing]:https://golang.org/pkg/testing/
[gopkg-testify]:https://pkg.go.dev/github.com/stretchr/testify
[book-mastering-go]:https://www.amazon.com.br/Mastering-production-applications-concurrency-structures-ebook/dp/B07WC24RTQ
[book-the-go-programming-language]:https://www.amazon.com.br/Programming-Language-Addison-Wesley-Professional-Computing-ebook/dp/B0184N7WWS
[book-introducing-go]:https://www.amazon.com.br/Introducing-Go-Reliable-Scalable-Programs-ebook/dp/B01AB3G496
