+++
title = "Testes de unidade com NUnit para aplicações C#"
date = "2022-03-18T11:45:29-03:00"
#dateFormat = "YYYY-MM-DD" # This value can be configured for per-post date formatting
author = "Lucas Silva"
authorTwitter = "" #do not include @
cover = ""
tags = ["dotnet", "tests"]
keywords = ["", ""]
description = "Crie testes de unidade para suas soluções .Net"
showFullContent = false
readingTime = false
hideComments = false
+++

Basicamente, testes automatizados são uma forma de automatizar o processo de revisão e validação de um software através de uma ferramenta programável. Esse processo, que antes podia ser extremamente trabalhoso e demorado, pode ser feito em minutos -- ou até mesmo segundos, dependendo dos tipos e da quantidade de testes existentes na sua aplicação.

Mike Cohn criou o conceito de pirâmide de testes no livro "Succeeding with Agile". É basicamente uma forma de te fazer pensar nas várias camadas de testes e em como elas devem estar presentes:
![Pirâmide de testes cunhada po Cohn. A base dela é composta por testes de unidade, seguida por testes de serviços e por fim testes de ui. Quanto mais baixo o nível do teste na pirâmide, mais desacoplado e mais rápido ele é](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/u3ey9qc5peuj6d1jwuby.png)
Quanto mais baixo o nível do teste na pirâmide, mais desacoplado e mais rápido ele é, além de mais numeroso.

Há inúmeros tipos de teste -- muitos dos quais eu mesmo desconheço a fundo -- mas abordarei apenas os testes de unidade. Eles possuem esse nome pois tendem as menores unidades de classes e métodos de forma isolada do resto da aplicação: sem acoplamento, nem nada. Apenas verificações para garantir que eles funcionem conforme o esperado, que as variáveis tenham os valores esperados etc.

Uma disciplina importante para criar testes é aquela chamada de TDD - Desenvolvimento orientado a testes. Nela, primeiro se escreve os testes, depois o código para que o teste funcione, depois o código é refatorado para que se torne a melhor versão que puder ser (esse último passo costuma ser bastante ignorado, gerando débitos técnicos). 

Ok, mas por que começar pelos testes e não pelo código ? Porque para criar uma solução, é necessário saber o que precisa ser criado. É necessário saber de antemão o que uma classe deve fazer, o que um método deve fazer, o valor que uma variável deve ter etc. Sem isso em mente, é muito fácil assumir que um resultado qualquer é o correto. E é assim que nascem os bugs 🐞! 

Sem mais delongas, bora pro código.

![Killjoy do jogo Valorant, em frente a um computador, arrumando os óculos no próprio rosto](https://media.giphy.com/media/KGdKKipVBu9vO5hKkb/giphy.gif)

## Mãos à obra
Criei uma aplicação console para exemplificar. Nela, criarei uma classe "Conta" com as seguintes especificações:
* Uma pessoa pode depositar qualquer valor maior que zero na conta;
* Uma pessoa pode sacar valores maiores que 0 e menores ou iguais ao saldo total;

Adicionei um projeto NUnit com o Visual Studio. Nele, criei uma classe chamada **ContaTests**, conforme abaixo. O atributo **[TestFixture]** indica que uma classe possui testes.
```
[TestFixture]
public class ContaTests
{
}
```
E então parti para o teste do primeiro método. Para que um método seja detectado como teste, é necessário adicionar o atributo **[Test]** acima dele. Quanto ao nome do método de teste, fiz baseando-me em algumas recomendações que podem ser encontradas [neste link.](https://docs.microsoft.com/pt-br/dotnet/core/testing/unit-testing-best-practices).
```
[Test]
public void Depositar_RetornaSaldoMaisValorDepositado()
{
    // Arrange 
    
    // Act
    
    // Assert
}
```
Gosto de deixar os 3 comentários em todos os testes que crio para separar bem as fases dele. Essas fases formam o padrão AAA, muito utilizado na indústria, e correspondem a:
* Arrange: fase na qual é necessário preparar teste preparando as variáveis, criando instâncias de objeto necessárias etc.;
* Act: fase na qual o método a ser testado é executado;
* Assert: fase na qual é feita a verificação de que o resultado obtido está de acordo com o esperado.

Na primeira fase, criei apenas uma instância da classe **Conta**, que ainda não existe, e então criei uma variável com o valor que espero obter como resultado do teste.
```
// Arrange 
Conta conta = new Conta("Lucas", 100);
decimal saldoEsperado = 200;
```

Na fase de ação, executei o método e armazenei o resultado em uma variável:
```
// Act
var resultado = conta.Depositar(100);
```

E por fim, fiz a verificação de que o resultado era igual ao saldoEsperado. Para isso, usei a classe Assert do NUnit. Ela contém vários métodos estáticos para testar inúmeras situações, como listas, igualdades, nulos etc.
```
// Assert
Assert.AreEqual(saldoEsperado, resultado);
```
O teste final ficou da seguinte forma:
```
[Test]
public void Depositar_RetornaSaldoMaisValorDepositado()
{
    // Arrange 
    Conta conta = new Conta("Lucas", 100);
    decimal saldoEsperado = 200;

    Act
    var resultado = conta.Depositar(100);

    Assert
    Assert.AreEqual(saldoEsperado, resultado);
}
```
O teste não compilará no primeiro momento pois a classe **Conta** ainda não existe. Logo, decidi criá-la:
```
public class Conta
{
    public Conta(string cliente, decimal saldo)
    {
        Cliente = cliente;
        Saldo = saldo;
    }

    public string Cliente { get; private set; }
    public decimal Saldo { get; private set; }

    public decimal Depositar(decimal valor)
    {
        Saldo += valor;
        return Saldo;
    }
}
```
Aproveitei e também criei o método **Depositar**. Ele é simples: recebe um valor e então adiciona esse valor à propriedade **Saldo** da classe.

Adicionei uma referência da classe no projeto de teste e então o executei. E então o teste passou !
![A imagem mostra que o teste foi aprovado após a execução com os valores corretos](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/h8esevtr3rklh3c27nqa.png)

⚠️⚠️ Uma forma de garantir que o teste funciona é fazendo-o quebrar propositalmente. Uma forma de quebrar o teste acima seria alterando o valor esperado para 300. Se o teste e o código estiverem certos, o teste não passará. Isso é importante para evitar que o resultado seja um falso-positivo.⚠️⚠️
 
Ok, o valor adicionado está correto, a soma está correta. Mas não é tudo a ser testado nesse método: 
* Uma pessoa pode depositar qualquer valor **maior que zero** na conta;

Logo, é necessário criar um teste para verificar se o meu programa barra o depósito de um valor negativo como -R$100. 
```
[Test]
public void Deposito_QuandoExecutadoComValorNegativo_LancaLancaInvalidOperationException()
{
    //Arrange
    Conta conta = new Conta("Lucas", 100);

    //Act
    Func<decimal> acao = () => conta.Depositar(-100);

    //Assert
    Assert.Throws<InvalidOperationException>(() => acao());
}
```
A grande diferença está nas fases **Act** e **Assert**. Assim que o valor -100 for detectado pelo método, é necessário que ele lance uma nova exceção: **InvalidOperationException**. Quando ela for lançada, o programa sairá daquele método antes de completá-lo, fazerndo com que o assert seja um pouco diferente. Para testar se ela realmente foi lançada, primeiro armazenei o método em um delegate (eu poderia explicar o significado mas tanto a [Microsoft](https://docs.microsoft.com/pt-br/dotnet/api/system.delegate?view=net-6.0) quanto o [Macoratti](http://www.macoratti.net/17/01/net_deleg1.htm) fizeram um excelente trabalho falando sobre) e então utilizei o método Throws da classe Assert. Ele recebe o tipo da exceção como generic ([artigo da Microsoft](https://docs.microsoft.com/pt-br/dotnet/standard/generics/) e [artigo do Macoratti](http://www.macoratti.net/11/07/net_gen1.htm)) e então consegue verificar se a exceção foi lançada.

Ao rodar o teste na primeira vez, ele não passoupois o método ainda não lança a exceção. Adicionei apenas 1 if para lançar a exceção e então o teste passou ! 
```
public decimal Depositar(decimal valor)
{
    if (valor <= 0)
    {
        throw new InvalidOperationException("Não é possível depositar valores negativos");
    }
    Saldo += valor;
    return Saldo
}
```
⚠️⚠️Para quebrar o teste e verificar se ele não dá um falso positivo, é possível simplesmente alterar o tipo da exceção esperada no Assert.⚠️⚠️

Por se tratar de um exemplo, o método de saque terá testes bastante parecidos com o do depósito, então é possível verificá-lo, assim como todo o resto do código, nesse repositório do GitHub que criei!

Bem, essa foi uma breve introdução sobre testes de unidade. Na próxima parte falarei sobre **Mocks** em um exemplo de uma API Rest e sobre o método **SetUp**.Se curtiu, não esqueça de compartilhar a postagem!

Até a próxima !

![Snoopy Dogg acenando em despedida](https://media.giphy.com/media/2YgCmRh9Arduw8LshF/giphy.gif)