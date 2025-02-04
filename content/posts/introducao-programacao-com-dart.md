+++
title = "Introducao à programacao com Dart"
date = "2020-01-06T10:58:10-03:00"
#dateFormat = "YYYY-MM-DD" # This value can be configured for per-post date formatting
author = "Lucas Silva"
authorTwitter = "" #do not include @
cover = ""
tags = ["dart", "flutter"]
description = "Uma breve introdução à programação utilizando Dart"
showFullContent = false
readingTime = false
hideComments = false
languageCode = 'pt-br'
+++

Tenho trabalhado com Flutter há aproximadamente um ano e queria compartilhar tudo que aprendi de algum forma. Entretanto, não adianta falar sobre o framework se as pessoas não conhecem a linguagem por trás dele. Além disso, sinto falta de conteúdos voltados aos iniciantes. Assim sendo, decidi unir o útil ao agradável e criar essa pequena introdução 😊

**Não se preocupe caso não entenda algum termo no início: todos eles (ou a maioria) serão explicados ao longo do texto. Caso fique alguma dúvida, escreva um comentário ou me mande um tweet**

**Essa postagem está disponível no [meu GitHub](https://github.com/lukelima/Flutter-tutoriais/blob/master/intoToDart.md). Caso haja algum erro, por favor, abra uma issue.**

## Dart - a linguagem
Criada em 2011 com foco inicial em desenvolvimento web, Dart é uma linguagem puramente orientada a objetos, baseada em classes e de herança única. É opcionalmente tipada pois dart suporta inferência de tipos (o interpretador tenta reconhecê-los por si mesmo), e o tipo de cada objeto durante o tempo de execução é uma instância da classe Type. ([Especificações da linguagem](https://dart.dev/guides/language/specifications/DartLangSpec-v2.2.pdf)). Isso quer dizer que tudo em Dart é um objeto, que a linguagem é fortemente baseada nas classes, o conceito fundamental da **programação orientada a objetos**, e que você pode ou não declarar o tipo da sua variável.
### Motivos para usar dart
* Compilação AoT - apps criados em dart são compilados antes de serem executados, fazendo com que rodem de forma suave;
* sintaxe semelhante às linguagens baseadas em C. Se você desenvolve em java, não terá muita dificuldade em migrar;
* [Pub](https://pub.dev/) - por mais que o gerenciados de pacotes ainda não seja tão grande quanto o NPM, há vários pacotes voltador às plataformas nas quais a roda;
* open source - sim, você pode contribui com a linguagem. [Clique aqui](https://github.com/dart-lang) para ter acesso aos repositórios no GitHub;
* animações a 60 FPS em flutter; 

## Instalação
Não é necessário instalar as ferramentas de desenvolvimento para acompanhar esta postagem pois os mantenedores da linguagem criaram o [DartPad](https://dartpad.dev/), que permite testar códigos Dart e criar aplicativos simples diretamente no seu navegador. Entretanto, caso deseje instalar, eis o passo-a-passo:
* Clique em [get dart](https://dart.dev/get-dart);
* escolha o seu sistema operacional e digite os comandos no terminal ou baixe o gerenciador de instalação;
Após instalar as ferramentas para desenvolver em Dart você poderá rodar arquivos .dart através do terminal. 

## IDEs
IDEs, ou ambientes integrados de desenvolvimento, são como editores de texto, mas com ferramentas específicas para ajudar no desenvolvimento: autocomplete, verificador de sintaxe, debuggers etc. Há vários IDEs gratuitos. Os meus preferidos são [VSCode](https://code.visualstudio.com/), [Atom](https://atom.io/) e [Android Studio](https://developer.android.com/studio), por mais que o último seja um tanto pesado para computadores com poucos recursos de memória e processamento.

## Variáveis
Variáveis são como caixinhas que armazenam um valor na memória. Em vez de utilizar o código hexadecimal correspondente ao lugar dessas caixinhas na memória (o endereço delas), é possível nomeá-las, facilitando o uso dos dados que elas contém.
Imagine que você precise somar o salário de todos os gastos de uma papelaria qualquer, e que para isso, apenas jogue os números em um papel (ou calculadora) a partir dos valores encontrados nos recibos
```
       1000
       1500
       2000
       1000
       1250
       1000
       1300
       1500
total: 10550
```
Agora imagine que daqui a 1 ano os recibos tenham se apagado e você, por alguma razão, precise achar o valor que foi gasto com cadernos. Olhando a conta acima, conseguiria achá-lo? Não seria mais simples se houvesse uma "legenda" dizendo a quê se referem esses valores? Imagine a bagunça conforme os anos se passarem...
Declarar variáveis é uma das operações fundamentais na programação. Em algumas linguagens, é algo simples como
```
pi = 3.14
```
Em Dart, declará-las é feito de forma parecida, apenas colocando a palavra "var" atrás dos nomes, ou o tipo delas na mesma posição
```
var pi = 3.14;
// ou
double pi = 3.14;
```
Tipá-las previnirá alguns erros futuros e facilitará seu trabalho com IDEs (ambientes de desenvolvimento integrados). Entretanto, as guidelines da linguagem indicam que para variáveis locais (como aquelas dentro de funções), é preferível o uso de "var".
Caso queira que suas variáveis não mudem, utilize a anotação "const" ou "final" antes do tipo
```
final name = 'Bob';
```
Em outras linguagens há uma forma de mudar o nível de acesso das variáveis, ou seja, mudar o quanto elas podem ser vistas de fora da classe. Essa forma se dá através da utilização das palavras "protected", "public" e "private". Em dart, basta escrever "_" antes do nome delas para torná-las privadas.
Há dois escopos para elas: local e global. O primeiro se refere àquelas que são declaradas dentro de funções e não podem ser acessadas fora delas. O segundo, àquelas que são declaradas fora de funções e podem ser acessadas de qualquer lugar no código.

### Como escolher o nome de uma variável
Ok, é mais fácil trabalhar com o nome de uma variável do que com o endereço na memória (exemplo: 0x9cf10c). Porém não pense que nomeá-las de qualquer jeito facilitará seu trabalho.
```
int a = 10;
```
O que significa "a" ? A quê esse valor se refere ? Tudo bem, você pode saber o significado, mas e as pessoas que darão manutenção ao código? **O computador entenderá o que significa com ou sem nome. Todo código escrito por você deve ser direcionado às pessoas que lerão ele, então facilite o trabalho delas 👍**.
Recomendo que leia o livro [Código limpo](https://www.amazon.com.br/C%C3%B3digo-limpo-Robert-C-Martin/dp/8576082675) para uma abordagem mais completa sobre o assunto.
* A linguagem não aceita que os nomes comecem por números, nem que contenham espaços em branco ou caracteres especiais(com exceção de "$" e "_"), e muito menos que seja uma das [palavras reservadas](https://dart.dev/guides/language/language-tour#keywords) dela.
```
// errado
int 1Numero = 10;

//correto  
int umNumero = 10;
```
## Algoritmos
É um tanto clichê falar que algoritmos são como receitas culinárias, mas são mesmo: uma sequência de instruções para a execução de uma tarefa. Pense nos ingredientes como "variáveis", e no modo de preparo como a execução do seu programa. Veja uma receita simples de brigadeiro (o máximo que consigo fazer em uma cozinha 🤣):
* Ingredientes: 
```
1 caixa de leite condensado;
3 colheres (sopa) de achocolatado;
1 colher (sopa) de manteiga;
1 xícara (chá) de chocolate granulado
```
Podemos dizer que o nome de cada ingrediente é igual ao nome de uma variável, e que a quantidade dos ingredientes, o valor delas. Assim sendo:
```
int caixaDeLeiteCondensado = 1;
int colheresDeAchocolatado = 3;
int colherDeManteiga = 1;
int xicaraDeChocolateGranulado = 1
```
* Quanto ao modo de preparo:
```
1 - Adicione o leite condensado, o achocolatado e a manteiga em uma panela;
2 - misture em fogo baixo por 10 minutos;
3 - deixe esfriar em um prato untado;
4 - enrole a mistura e adicione granulado;
```
Em código usando funções (última sessão do artigo):
```
var mistura = adicionarNaPanela(caixaDeLeiteCondensado, colheresDeAchocolatado, colherDeManteiga);
misturarEmFogoBaixo(mistura);
deixarEsfriar(mistura, pratoUntado);
enrolarEGranular(mistura);
```
É claro, há vários níveis de complexidade para algoritmos de uma mesma tarefa: você pode dizer que começa o dia levantando da cama, ou pode ser mais profundo e dizer que começa abrindo os olhos, se espreguiçando, levantando o tronco, sentando na cama...
Algoritmos são a base da programação. E pasme: você aprenderá sobre algumas ferramentas durante sua vida profissional, mas a maioria dos seus problemas iniciais será resultante da falta de prática com algoritmos, não com as ferramentas em si! 

**NUNCA SE ESQUEÇA DE UTILIZAR ; (PONTO E VÍRGULA) AO FINAL DE CADA INSTRUÇÃO

## Comentários
Comentários servem para escrever uma mensagem no código que não será interpretada pelo computador. Essa mensagem costuma ser criada para documentar o código (explicá-lo), mas não se engane: a necessidade de diversos comentários pode ser indicativo de que a escolha dos nomes das variáveis e funções, ou o uso delas, não está claro o suficiente. Um código bem escrito deve falar por si só.
Dart suporta 3 tipos de comentários:
* os de 1 única linha
```
// esse é um comentário de linha única
```
* os de múltiplas linhas
```
/*
  Esse é um comentário em
  múltiplas linhas
*/
```
* os de documentação
```
/// comentários de documentação podem começar com 3 barras invertidas ou /**
```
Boa parte dos resultados de instruções neste texto foram escritos em comentários na frente delas.
## Tipos de dados
### Strings
```
String nome = 'Lucas';
```
Strings são as representações de frases, palavras, caracteres etc. Sempre que for necessário armazenar uma das opções citadas, declare uma string com as palavras dentro de aspas simples ('') ou duplas(""). Elas são as responsáveis por textos em interfaces de sites e aplicativos. Em **flutter**, por exemplo, são o tipo de dado esperado pelo widget Text()
```
Text("Digite o seu nome: ");
```
Métodos são blocos de código na forma de funções (veja ao final da postagem) que facilitam o trabalho com dados de determinados tipos. Como exemplo para strings temos "toUpperCase()", que transforma a string em letras maiúsculas, ou "split()", que separa ela em uma lista de caracteres. Esse tipo também conta com algumas propriedades, como "length", que conta o número de caracteres (espaços em branco também), que, como métodos, também são acessadas utilizando um "." após o nome da variável e o nome da propriedade em seguida
```
String meuNome = "Lucas";
  
print(meuNome.toLowerCase()); // lucas
print(meuNome.toUpperCase()); // LUCAS
print(meuNome.length);        // 5
```
É possível concatenar duas ou mais strings, unindo-as uma única.
```
String nome = "Lucas";
String sobrenome = "Silva";

print(nome + ' ' + sobrenome);
```
Repare no espaço em branco entre as variáveis. Dart possui suporte à interpolação, que é uma maneira de criar strings sem a necessidade do operador "+"

* "$variavel" para expressões simples
```
String nome = "Lucas";
String sobrenome = "Silva";
print("$nome $sobrenome"); // Lucas Silva
``` 
* "${variavel.metodo}" para expressões mais complexas
```
String nome = "Lucas";
String sobrenome = "Silva";
print("${nome.toUpperCase()} $sobrenome"); // LUCAS Silva
```
### Números
Dizem que ciências da computação requerem muita matemática. *É e não é verdade*. Não precisa ser doutor em matemática para saber programar, mas não deixará os números de lado.
Você pode ter ouvido falar que os computadores entendem códigos binários e tal. Esse tipo de abordagem aos números ("binários") se refere à base deles. Podemos utilizar binários, hexadecimais (muito utilizados em cores), octais, decimais etc.
Os números possuem basicamente dois tipos: os inteiros e os decimais. O primeiro tipo é comumente chamado de "int". O segundo, em algumas linguagens, é chamado de "float" enquanto em outras, como  dart, é chamado de "double".
```
int umNumero = 10;
double pi = 3.14;
```
Várias operações matemáticas estão disponíveis para esse tipo de dados. Arredondamento, Máximo divisor comum, checar se um número é par ou ímpar etc.

### Boolean
É um tipo de dados que representa 1 ou 0, sendo esses verdadeiro ou falso, respectivamente (embora essa correspondência não seja válida em Dart). Possui esse nome em homenagem a George Boole, que definiu um sistema de lógica algébrica. São muito utilizados em expressões, principalmente as condicionais.
```
bool verdade = true;
bool mentira = false;
```
### Listas
É difícil falar que algum tipo de dado é mais importante que o outro, mas sério, aprenda **tudo** sobre listas. Saber manipular dados corretamente é o primeiro passo para conseguir um emprego na área.
Por mais que o interpretador consiga entender o tipo, é sempre interessante tipar suas variáveis para evitar erros. Logo, listas se declarariam da seguinte forma:
```
List<int> listaDeNumeros = [1, 2, 3];
List<String> vogais = ['a', 'e', 'i', 'o', 'u'];
```
Listas são como coleções de valores. As notificações de um usuário no Instagram, por exemplo, são listas de objetos chamados Notificações (veremos sobre Programação orientada a objetos na próxima postagem). A famosa lista de tarefas (todo List) é uma lista de... adivinha? Tarefas!
Já utilizou Todoist ? Pense assim: 
O app é basicamente uma lista. Nessa lista, é possível adicionar e remover tarefas. Essas operações são feitas em Dart utilizando "add()"/"insert()" e remove(). O legal é que insert pode utilizar o index do novo elemento, e é possível procurar diretamente pelo elemento a ser removido no remove. Também é possível remover todas as tarefas da lista utilizando "clear()"
```
List<String> vogais = ['a', 'e', 'i', 'o', 'u'];
vogais.add('r'); // ['a', 'e', 'i', 'o', 'u', 'r']
vogais.remove('r'); // ['a', 'e', 'i', 'o', 'u']
vogais.remove('e'); // ['a', 'i', 'o', 'u']
vogais.insert(1, 'e'); //['a', 'e', 'i', 'o', 'u']
vogais.clear(); // [] <- lista vazia
```
Index, ou índices, são as posições dos elementos em uma lista. Na maioria das linguagens a contagem começa por 0. Nunca se esqueça disso!
Adicionar uma lista à outra se tornou mais simples a partir da versão 2.3 da linguagem, na qual introduziu-se o "spread operator". Caso o valor à direita possa ser null, utilize o null-aware spread operator (última linha do código) para evitar erros na execução do código.
```
List<int> lista1 = [4, 5];
List<int> lista2 = [1, 2, 3, ...lista1]; // spread operator ->...lista1 copia os elementos
List<int> lista3 = [...?lista2]; // null-aware spread operator 
```
Outras novidades são "collection if" e "collection for", onde as listas são criadas através de uma condição ou de um loop.

````
/// collection if
List<String> listaDeCompras = ['chocolate', 'refrigerante', if(estiveremEmPromocao)];

// collection for
List<int> primeiraListaDeNumeros = [3, 4, 5, 6];
List<int> segundaListaDeNumeros = [1, 2, for(int numero in primeiraListaDeNumeros) numero];
``````

Uma propriedade útil das listas é "length", que retorna o número de elementos dentro delas.

```
List<int> listaDeNumeros = [1, 2, 3];
print(listaDeNumeros.length); // 3
```
### Conjuntos (Sets)
Conjuntos em Dart são como conjuntos matemáticos: a ordem e a quantidade de vezes que um elemento aparece não possuem importância. Para utilizá-los, é necessário colocar um tipo antes de {}. Caso contrário, o interpretador entenderá que se trata de um Map.
```
var conjuntoDeNumeros = <int>{};
print(conjuntoDeNumeros is Set); // true

var outroConjuntoDeNumeros = {};
print(outroConjuntoDeNumeros is Set); // false

var letras = <String>{'a', 'a', 'b', 'b', 'c', 'c'};
print(letras); // {a, b, c}, pois elementos repetidos não importam para conjuntos
```
### Mapas 
Mapas são objetos que associam chaves, em strings, a valores que podem ser de qualquer tipo! Apesar dos valores poderem se repetir, as chaves devem ser únicas.
```
Map<String, dynamic> listaDeCompras = {
    'chocolates': 2,
    'refrigerantes': 4,
    'carne': 1.5 // quilos
};

// Ou através do construtor Map()
var listaDeCompras = Map();
listaDeCompras['chocolates'] = 2;
listaDeCompras['refrigerantes'] = 4;
listaDeCompras['carne'] = 1.5;
```
Os valores de um mapa podem ser acessados da mesma forma que construímos a variável com o construtor Map(): 
```
print(listaDeCompras['chocolates']); // 2
```
São utilizados na construção de objetos a partir de **dados fornecidos por uma API ou query**.
### Runas
Strings em Dart são uma sequência de caracteres Unicode no formato UTF-16. Um caractere unicode é escrito da seguinte forma:
```
\u2665    // ♥
```  
As runas expõe o unicode de um string e podem ser usadas para formar caracteres especiais como símbolos e emojis.
```
Runes coracao = Runes('\u2665');
print(String.fromCharCodes(coracao)); // ♥
```
### Símbolos
Símbolos são nomes (strings) utilizados para refletir metadados de uma biblioteca de códigos ou API. Essas informações costumam ser otimizadas para que o computador as interprete, e os símbolos servem para torná-las mais legível para nós, humanos. Nome de classes, métodos e libs podem ser símbolos. 
Confesso que não vejo a utilização deles com frequência e os exemplos em relação a eles são quase sempre iguais a esse aqui:
* [Dart Symbol](https://www.w3adda.com/dart-tutorial/dart-symbol)

### Dynamic
Essa é uma declaração responsável por permitir que qualquer valor seja atribuido a uma variável. É preferível declarar uma variável com dynamic do que deixar a inferência falhar. Entretanto é recomendado utilizar "Object" em vez dele, deixando-o para quando for necessário dinamismo em uma parte do programa ou quando os valores virão de uma interoperabilidade entre JS/Kotlin, por exemplo.

## Operadores
Operadores são como os sinais matemáticos: pegam dois ou mais valores e transformam em um resultado diferente. Há diversos tipos de operadores, então o uso deles não se restringe a números. Vimos, por exemplo, a concatenação de strings utilizando o sinal "+".
```
print("Hello" + " World!"); // Hello World!
```
### Operadores aritméticos
São os mesmos que utilizamos em contas. Considere:
```
double umNumero = 10.0;
double outroNumero = 4.0;
```
então: 
* + -- soma dois valores (podendo ser strings);
```
print(umNumero + outroNumero); // 14
```
* - -- subtrai o segundo valor do primeiro;
```
print(umNumero - outroNumero); // 6
```
* * -- multiplica ambos os valores;
```
print(umNumero * outroNumero); // 40
```
* / -- divide o primeiro valor pelo segundo;
```
print(umNumero / outroNumero); // 2.5
```
* ~/ -- divide o primeiro valor pelo segundo mas retorna um número inteiro, sem parte fracionada;
```
print(umNumero ~/ outroNumero); // 2
```
* % -- divide o primeiro valor pelo segundo e retorna o resto da divisão
```
print(umNumero % outroNumero); // 2, pois 10 / 4 = 8, com resto = 2
```
A ordem de precedência pode ser consultada [nessa tabela](https://dart.dev/guides/language/language-tour#operators), mas pode ser alterada usando parênteses "()", como em contas matemáticas.
```
1 + 2 * 3 

é diferente de

(1 + 2) * 3
```
### Operadores de incremento
São aqueles que aumentam ou diminuem a variável em 1. São divididos em dois tipos: prefixo, que aumenta a variável e depois avalia a expressão, e *postfix* (como um sufixo), que só aumenta a variável após avaliar a expressão
* Postfix:
```
variavel++;
variavel--;
```
* Prefixo:
```
++variavel;
--variavel;
```
### Operadores de igualdade e relacionais
São aqueles usados para comparar valores. Considere:
```
double umNumero = 10.0;
double outroNumero = 4.0;
``` 
Então:
* == -- retorna true se os valores forem iguais e falso se forem diferentes;
```
print(umNumero == outroNumero); // false
print(umNumero == umNumero); // true
```
* != -- retorna true se os valores forem diferentes;
```
print(umNumero != outroNumero); // true
print(umNumero != umNumero); // false
```
* >  -- retorna true se o primeiro valor for maior que o segundo;
```
print(umNumero > outroNumero); // true
print(outroNumero > umNumero); // false
print(umNumero > umNumero); // false
```
* <  -- retorna true se o primeiro valor for menor que o segundo; 
```
print(umNumero < outroNumero); // false
print(outroNumero < umNumero); // true
print(umNumero < umNumero); // false
```
* >= -- retorna true se o primeiro valor for maior ou igual ao segundo;
```
print(umNumero >= outroNumero); // true
print(outroNumero >= umNumero); // false
print(umNumero >= umNumero); // true, pois os números são iguais
```
* <= -- retorna true se o primeiro valor for menor ou igual ao segundo;
```
print(umNumero <= outroNumero); // false
print(outroNumero <= umNumero); // true
print(umNumero <= umNumero); // true, pois os números são iguais
```

### Operadores de teste de tipos
São úteis para converter ou checar tipos de dados. Considere:
```
int num = 10;
```
* as  --- converte o tipo de um objeto. Pode ser utilizado como uma versão enxuta do operador "is"; 
```
if (emp is Person) {
  // Type check
  emp.firstName = 'Bob';
}

// com o operador "as"
(emp as Person).firstName = 'Bob';
```
* is  --- retorna true se o valor for de um determinado tipo;
```
print(num is int); // true
```
* is! --- retorna true se o valor não for de um determinado tipo;
```
print(num is! String); // true
```
### Operadores de atribuição
São aqueles que atribuem valores às variáveis:
* =   --- utilizado para atribuir um valor a uma variável
```
int umNumero = 2;
```
* +=  --- utilizado para somar um valor ao valor já existente na variável
```
int umNumero = 2;
umNumero += 3;
print(umNumero); // 5, pois é o mesmo que umNumero = umNumero + 3
```
* -=  --- utilizado para subtrair um valor ao valor já existente na variável
```
int umNumero = 2;
umNumero += 1;
print(umNumero); // 1
```
* *=  --- utilizado para multiplicar um valor ao valor já existente na variável
```
int umNumero = 2;
umNumero *= 2;
print(umNumero); // 4
```
* /=  --- utilizado para dividir um valor ao valor já existente na variável
```
double umNumero = 2.0;
umNumero /= 2;
print(umNumero); // 1
```
* ~/= --- utilizado para dividir arredondando para baixo um valor ao valor já existente na variável
```
int umNumero = 5;
umNumero ~/= 2;
print(umNumero); // 2
```
### Operadores lógicos
Servem para trabalhar com operações booleanas, que podem inverter ou não o fluxo de execução de um programa.
Considere:
```
bool verdade = true;
bool mentira = false;
```
Então:
* !expressão --- inverte o valor da expressão. 
```
print(!verdade); // false
```
* && --- retorna true se ambas as expressões forem verdadeiras.
```
print(verdade && verdade); // true
print(verdade && mentira); // false
```
* || --- retorna true se uma das expressões for verdadeira.
```
print(verdade || verdade); // true
print(verdade || mentira); // true
print(mentira || mentira); // false
```
### Notação de cascata
A ideia da notação de cascata é evitar que seja necessária a criação de variáveis temporárias, permitindo o acesso a um mesmo objeto linha após linha.
```
querySelector('#confirm') // Pega um objeto
  ..text = 'Confirm' // Usa as propriedades dele
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'));
```
### Outros operadores
São os responsáveis por chamadas de função e acesso a listas e propriedades de objetos.
* () -- chamada de função
```
// definir a função
void helloWorld() {
  print("Hello World!");
}

//chamar a função
helloWorld();
```
* [] -- acesso a elementos de lista
```
List<int> listaDeNumeros = [1, 2, 3, 4, 5];
int segundoNumeroDaLista = listaDeNumeros[1]; // pois os índices começam por 0

```
* .  -- acesso a alguma propriedade de uma expressão. Um exemplo é o acesso à propriedade *length* de uma lista, que retorna o número de elementos nela
```
List<String> vogais = ['a', 'e', 'i', 'o', 'u'];
print(vogais.length); // 5
```
## Sentenças de controle de fluxo
### Condicionais 
É possível testar as condições no seu programa para que ele rode pedaços de código diferentes para cada uma delas. Pense na seguinte sentença: 
* (1) **Se** o usuário estiver logado, (2) **então** poderá acessar a página inicial. (3) **Caso contrário**, (4) será direcionado para a página de Login. Traduzindo para o código:
```
if(usuarioEstiverLogado) { // 1
  redirecionarParaPaginaInicial(); // 2
} else { // 3
  redirecionarParaPaginaDeLogin(); // 4
}
```
Perceba os números na frase e no código. Eles traduzem a forma na qual as condicionais funcionam:
1 - if -- se(condição a ser testada)
2 - código a ser executado se a condição for verdadeira
3 - else -- caso contrário
4 - código executado caso a condição do número 1 seja falsa.
Outro exemplo: 
* Se o aluno tiver média **maior ou igual** a 7 **e** frequência **maior** que 75%, será aprovado. 
```
if(media >= 7 && frequencia > 75) {
  print("O aluno foi aprovado!");
} else {
  print("O aluno foi reprovado!");
}
```
As palavras em negrito correspondem aos operadores '>=', && e >. Agora, e se eu quiser testar a seguinte condição:
* Se o aluno tiver média **maior ou igual** a 7, será aprovado. Caso a média seja maior ou igual a 5 e menor que 7, ficará em recuperação. Caso contrário, será reprovado.
Há duas condições a serem testadas: se ele será aprovado, ou se ficará de recuperação (apenas duas, pois qualquer nota menor que 5 fará com que ele seja reprovado). Nesse tipo de situação, utilizamos as palavras reservadas "else if":
```
if(media >= 7) { // primeira condição
  print("O aluno foi aprovado");
} else if (media >= 5 && media < 7) { // segunda condição
  print("O aluno ficou em recuperação");
} else { // qualquer outra condição
  print("O aluno foi reprovado");
}
``` 
### Ternário
O operador ternário funciona como o "if", mas a sintaxe dele é mais enxuta:
```
media >= 7 ? print("O aluno foi aprovado") : print("O aluno foi reprovado");
```
A sintaxe funciona da mesma forma:
```
condição a ser testada ? código caso seja verdadeira : código caso seja falsa. 
```
É possível encadear várias sentenças no ternário. O mesmo exercício do if-else ficaria assim:
```
media >= 7 ? print("O aluno foi aprovado") : media >= 5 ? print("O aluno ficou em recuperação") : print("O aluno foi reprovado"); 
```
### Switch - case
Em vez de usar vários "ifs" e "elses", use switch-case (**nem sempre é recomendado por questão de performance**). A sintaxe funciona assim:
```
switch(variavel) {
    case('caso1'):
        print('caso1');
        break;
    default:
        print('qualquer mensagem padrão');
}
```
Os possíveis valores da variável devem ser escritos dentro dos casos (case), e na linha seguinte, as instruções para aquela determinada condição. Ao final há a palavra-chave "break", que indica que o programa pode executar as sentenças seguintes ao switch. Se essa palavra for esquecida, ocorrerá uma erro durante a execução.
Se mais de um caso for executar as mesmas sequências, é possível utilizar "casos vazios":
```
switch(variavel) {
    case('caso1'):
    case('caso2'):
    case('caso3'):
        print('Qualquer mensagem');
        break;
    default:
        print('qualquer mensagem padrão');
}
```
### Laços de repetição -- While
Partes de um código costumam ser repetidas durante a execução. Entretanto, não é necessário escrevê-las várias vezes! Para isso, utiliza-se os laços de repetição!
O **while** é um laço que executa um determinado bloco de código **enquanto** uma condição for verdadeira. Com ele é possível realizar algoritmos simples, como contar números, e até aplicações mais práticas, como por exemplo o [loop de postagens do WordPress](https://developer.wordpress.org/themes/basics/the-loop/).
```
int contador = 0;
while(contador < 10) {
  print(contador);
  contador++;
}

Resultado:
0
1
2
3
4
5
6
7
8
9
```
Todo o código localizado entre "{}" será executado repetidas vezes até que a condição deixe de ser verdadeira. Preste muita atenção na última expressão. 
```
contador++;
```
O contador aumentará em 1 toda vez que o código se repetir até que a condição seja falsa. **O que acontecerá nos esquecermos de incrementá-lo?**

### Laços de repetição -- Do While
Esse laço é parecido com o anterior. A diferença é que o while checa a condição e depois executa o código. Já o do while executa o código e depois checa. Veja a diferença em um algoritmo com a mesma lógica:
```
int contador1 = 1;
while(contador1 < 1) {
  print("contador1: $contador1");
  contador1++;
}

Resultado: nada é exibido no console.
  
int contador2 = 1;
do {
  print("contador2: $contador2");
  contador2++;
} while(contador2 < 1);

Resultado:
contador2: 1
"
```
### Laços de repetição -- For 
Esse laço é comumente utilizado em conjunto com listas, pois pode usar o número de elementos delas como "contador" da repetição. 
* Sintaxe tradicional:
```
List<int> listaDeNumeros = [1, 2, 3, 4, 5];
for(int i = 0; i < listaDeNumeros.length; i++) {
  print(listaDeNumeros[i]);
}
//Resultado:
1
2
3
4
5
```
* for...in:
```
List<int> listaDeNumeros = [1, 2, 3, 4, 5];
for(int i in listaDeNumeros) {
  print(i);
}
```
* forEach() -- um método das listas que realiza uma ação programada para cada elemento em uma determinada lista:
```
List<int> listaDeNumeros = [1, 2, 3, 4, 5];
listaDeNumeros.forEach((numero) {
  print(numero);
});
```
Pense que "numero" é como o nome de uma variável que, durante cada repetição terá um valor na lista seguindo uma ordem sequencial.

## Funções
```
tipo nomeDaFuncao(parametros) {
  declarações a serem executadas
}
```
A parte mais interessante desse texto. As funções são blocos de código que executam alguma operação. Quando utilizadas sozinhas, são chamadas de **funções**. Quando associadas a algum objeto, são chamadas de **métodos**. Algumas delas foram demonstradas ao longo dessa postagem. "stringQualquer.toUppercase()" é uma delas.
A mais importante em programas Dart é a função main(), pois é a responsável por executar todo o código. Sempre que abrir o DartPad, o editor virá com ela preenchida.
```
void main() {
  // digite o código aqui
}
```   
A palavra "void" significa que a função não possui um valor de retorno. As funções podem retornar algum valor, que é geralmente o resultado das instruções executadas dentro delas. No caso de uma função de soma, é comum que ela retorne o resultado da soma dos números passados como argumento. 
```
int soma(int umNumero, int outroNumero) {
  return umNumero + outroNumero; // o valor de retorno (return) será o resultado da expressão.
}
```
Perceba que declarei duas variáveis dentro dos parênteses: int umNumero e int outroNumero, mas não atribui um valor a elas. Essas variáveis, chamadas de **argumentos**, terão seus valores atribuídos durante a chamada da função e separados por uma vírgula:
```
print(soma(1, 2)); // 3
```
É possível utilizar o valor de retorno de uma função como argumento de outra função. Essa é uma abordagem de um outro **paradigma de programação**, que trataremos em uma postagem próxima.

## Fontes
* [Language tour | Dart](https://dart.dev/guides/language/language-tour);
* [Effective Dart: Design](https://dart.dev/guides/language/effective-dart/design#prefer-annotating-with-dynamic-instead-of-letting-inference-fail  );
* [Platforms](https://dart.dev/platforms)
* [How Does Dart AOT Work?](https://stackoverflow.com/questions/50012596/how-does-dart-aot-work)
* [Why Flutter Uses Dart](https://hackernoon.com/why-flutter-uses-dart-dd635a054ebf)
