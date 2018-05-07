<h1>Se��o 01 - A grande variedade de impostos e o padr�o Strategy</h1>

Design Patterns
Classes e m�todos gigantes? Cinco minutos para entender o que aquele m�todo faz ou onde est� o c�digo que faz uma altera��o simples? Diversas vari�veis e diversos ifs e fors no mesmo m�todo? C�digo complexo e obscuro? Toda vez que uma altera��o aparece, voc� precisa mudar em 20 classes diferentes? Sim, problemas muito comuns do nosso dia-a-dia. Mas por que isso acontece?

Um fato que � conhecido sobre todo software � que ele, mais cedo ou mais tarde, vai mudar: novas funcionalidades aparecer�o, outras dever�o ser alteradas etc. O problema � que, geralmente, essas mudan�as n�o s�o feitas de forma muito planejada. Por esse motivo, durante o desenvolvimento de um projeto de software, � bem comum a cria��o de c�digo onde as responsabilidades se misturam e espalham por v�rias classes, fazendo com que a manuten��o do c�digo fique cada vez mais dif�cil, j� que uma simples mudan�a pode obrigar o desenvolvedor a alterar diversas classes. Nesse cen�rio, temos uma situa��o onde o 'Design' das classes n�o est� bom e � poss�vel melhor�-lo.

Para atingir o objetivo da melhora do 'Design' e diminuir o custo de manuten��o, existem algumas t�cnicas, onde podemos aplicar a orienta��o a objetos de uma maneira a simplificar o c�digo escrito.

Resolveremos diversos dos problemas que vemos em c�digos, como classes e m�todos gigantes que fazem muita coisa, classes que precisam conhecer mais 10 outras classes para fazer seu trabalho, m�todos complicados com muitos ifs e condi��es, mudan�as que se propagam por v�rias classes e assim por diante.

Algumas das principais t�cnicas para atingir um bom 'Design' s�o t�o comuns que foram catalogadas em um conjunto de alternativas para solucionar problemas de Design de c�digo, chamados de Design Patterns, mais conhecido em portugu�s como os Padr�es de Projetos, os quais, durante esse curso, aprenderemos a utilizar em um projeto. Um padr�o de projeto nada mais � do que uma solu��o elegante para um problema que � muito recorrente em nosso dia-a-dia.

Mais importante do que entender como � a implementa��o de um padr�o de projeto, � entender a motiva��o do padr�o: em quais casos ele faz sentido e deve ser aplicado. Durante os pr�ximos cap�tulos, observe o cen�rio do exemplo dado com aten��o e o leve para o seu mundo e para o contexto de sua aplica��o. Aquele cen�rio acontece? Se sim, ent�o talvez o padr�o de projeto ensinado naquele cap�tulo seja uma boa sa�da para controlar a crescente complexidade daquele c�digo.

Durante todos os outros cap�tulos, faremos muitos exerc�cios. Todas as explica��es ser�o baseadas em problemas do mundo real e voc�, ao final, utilizar� os conhecimentos adquiridos para resolver os exerc�cios, que s�o desafiadores! N�o se esque�a de prestar muita aten��o ao cen�rio e ao problema proposto. Entenda a motiva��o de cada padr�o para que voc� consiga lev�-lo para os problemas do seu mundo!

Muitas regras e c�digo complexo
Tomando como exemplo uma aplica��o cujo objetivo � a cria��o de or�amentos, temos uma regra de neg�cio na qual os valores dos or�amentos podem ser submetidos � alguns impostos, como ISS, ICMS e assim por diante. Com isso, temos a simples classe que representa o or�amento, recebendo via construtor o seu valor: 

```java
public class Orcamento { 
	private double valor; 

	public Orcamento(double valor) { 
		this.valor = valor; 
	} 

	public double getValor() { 
		return valor; 
	} 
} 

```
Com isso, podemos criar novos or�amentos, instanciando objetos do respectivo tipo e caso queiramos calcular um imposto sobre seu valor, basta utilizarmos o atributo valor para isso. Assim, podemos estipular que o ICMS valha 10% e precisamos calcul�-lo, baseado no valor do or�amento. Para isso, podemos ter a seguinte classe com um simples m�todo para realizar o c�lculo: public class CalculadorDeImpostos { public void realizaCalculo(Orcamento orcamento) { double icms = orcamento.getValor() * 0.1; System.out.println(icms); // imprimir� 50.0 } } Podemos ainda querer calcular outro imposto, como o ISS, que � 6% do valor do or�amento. Com isso, adicionamos a nova regra ao c�digo anterior. Mas devemos escolher qual o imposto que ser� calculado. Portanto, o m�todo realizaCalculo dever� receber uma informa��o, indicando qual o imposto ter� o c�lculo realizado: public class CalculadorDeImpostos { public void realizaCalculo(Orcamento orcamento, String imposto) { if( "ICMS".equals(imposto) ) { double icms = orcamento.getValor() * 0.1; System.out.println(icms); // imprimir� 50.0 } else if( "ISS".equals(imposto) ) { double iss = orcamento.getValor() * 0.06; System.out.println(iss); // imprimir� 30.0 } } } Note que uma das consequ�ncias do c�digo que acabamos de criar, � que espalhamos os c�lculos e nossas regras de neg�cio. Dessa maneira, n�o temos nenhum encapsulamento de nossas regras de neg�cio e elas se tornam bastante suscet�veis a serem replicadas em outros pontos do c�digo da aplica��o. Por que n�o encapsulamos as regras dos c�lculos em uma classe especializada para cada imposto?
Encapsulando o comportamento
Ao inv�s de mantermos as regras espalhadas pela nossa aplica��o, podemos encapsul�-las em classes cujas responsabilidades sejam realizar os c�lculos. Para isso, podemos criar as classes ICMS e ISS cada um com seu respectivo m�todo para calcular o valor do imposto de acordo com o or�amento.

```java

      public class ICMS {

          public double calculaICMS(Orcamento orcamento) {
              return orcamento.getValor() * 0.1;
          }

      }
      public class ISS {

          public double calculaISS(Orcamento orcamento) {
              return orcamento.getValor() * 0.06;
          }

      }
```

Agora temos as duas classes que separam a responsabilidade dos c�lculos de impostos, com isso, podemos utiliz�-las na classe CalculadorDeImpostos da seguinte maneira:

```java
      public class CalculadorDeImpostos {

          public void realizaCalculo(Orcamento orcamento, String imposto) {

          if( "ICMS".equals(imposto) ) {

            double icms = new ICMS().calculaICMS(orcamento);
            System.out.println(icms); // imprimir� 50.0

          } else if( "ISS".equals(imposto) ) {

            double iss = new ISS().calculaISS(orcamento);
            System.out.println(iss); // imprimir� 30.0

            }
          }
      }

```
Agora o c�digo est� melhor, mas n�o significa que esteja bom. Um ponto extremamente cr�tico desse c�digo � o fato de que quando quisermos adicionar mais um tipo diferente de c�lculo de imposto em nosso calculador, teremos que alterar essa classe adicionando mais um bloco de if, al�m de criarmos a classe que encapsular� o c�lculo do novo imposto. Parece bastante trabalho.

Eliminando os condicionais com polimorfismo e o pattern Strategy
O que queremos em nosso c�digo � n�o realizar nenhum condicional, ou seja, n�o termos mais que fazer ifs dentro do CalculadorDeImpostos. Dessa forma, n�o devemos mais receber a String com o nome do imposto, no qual realizamos os ifs. Mas como escolheremos qual o imposto que deve ser calculado?

Uma primeira possibilidade � criar dois m�todos separados na classe CalculadorDeImpostos. Um para o ICMS e outro para o ISS, dessa forma teremos:
```java

      public class CalculadorDeImpostos {

          public void realizaCalculoICMS(Orcamento orcamento) {

            double icms = new ICMS().calculaICMS(orcamento);
            System.out.println(icms);

          }

          public void realizaCalculoISS(Orcamento orcamento) {

            double iss = new ISS().calculaISS(orcamento);
            System.out.println(iss);

          }

      }

```
No entanto, agora s� transferimos o problema dos v�rios ifs para v�rios m�todos. O que n�o resolve o problema. O pr�ximo passo para conseguirmos melhorar essa solu��o � termos um �nico m�todo, gen�rico, que consegue realizar o c�lculo para qualquer imposto, sem fazer nenhum if dentro dele.

```java
      public class CalculadorDeImpostos {

          public void realizaCalculo(Orcamento orcamento) {

            double icms = new ICMS().calculaICMS(orcamento); 
            // Mas e se quisermos outro imposto?

            System.out.println(icms);

          }

      }

```
Agora estamos presos ao ICMS. Precisamos que nosso c�digo fique flex�vel o bastante para utilizarmos diferentes impostos na realiza��o do c�lculo. Uma possibilidade para resolvermos esse problema �, ao inv�s de instanciarmos o imposto que desejamos dentro do m�todo, recebermos uma inst�ncia do Imposto que queremos utilizar, como no c�digo seguinte:

```java

      public class CalculadorDeImpostos {

          public void realizaCalculo(Orcamento orcamento, Imposto imposto) {

            double valor = imposto.calcula(orcamento); 

            System.out.println(valor);

          }

      }

```
No entanto, n�o temos o tipo Imposto em nossa aplica��o e al�m disso, nesse tipo precisamos passar uma inst�ncia de ISS e ICMS. Para isso, podemos criar uma interface chamada Imposto e fazermos as classes ISS e ICMS a implementar.
```java

      public interface Imposto {
          double calcula(Orcamento orcamento);
      }

      public class ICMS implements Imposto {

          public double calcula(Orcamento orcamento) {
              return orcamento.getValor() * 0.1;
          }

      }

      public class ISS implements Imposto {

          public double calcula(Orcamento orcamento) {
              return orcamento.getValor() * 0.06;
          }

      }

```
E agora o nosso CalculadorDeImpostos est� pronto para ser utilizado e flex�vel o bastante para receber diferentes tipos (ou "estrat�gias") de impostos. Um c�digo que demonstra essa flexibilidade � o seguinte:

```java
      public class TesteDeImpostos {

          public static void main(String[] args) {
              Imposto iss = new ISS();
              Imposto icms = new ICMS();

              Orcamento orcamento = new Orcamento(500.0);

          CalculadorDeImpostos calculador = new CalculadorDeImpostos();

          // Calculando o ISS
          calculador.realizaCalculo(orcamento, iss);

          // Calculando o ICMS        
          calculador.realizaCalculo(orcamento, icms);
          }
      }

```
Agora, com um �nico m�todo em nosso CalculadorDeImpostos, podemos realizar o c�lculo de diferentes tipos de impostos, apenas recebendo a estrat�gia do tipo do imposto que desejamos utilizar no c�lculo.

Quando utilizamos uma hierarquia, como fizemos com a interface Imposto e as implementa��es ICMS e ISS, e recebemos o tipo mais gen�rico como par�metro, para ganharmos o polimorfismo na regra que ser� executada, simplificando o c�digo e sua evolu��o, estamos usando o Design Pattern chamado Strategy.

Repare que a cria��o de uma nova estrat�gia de c�lculo de imposto n�o implica em mudan�as no c�digo escrito acima! Basta criarmos uma nova classe que implementa a interface Imposto, que nosso CalculadorDeImpostos conseguir� calcul�-lo sem precisar de nenhuma altera��o!