---
layout: post
title:  "Design Patterns: Model-View-Controller"
date:   2012-04-19 16:32:00
categories: [Arquitetura, Design Pattern, Java]
---
**Model-View-Controller** ou simplesmente **MVC**, é um **Design Pattern** que é muito utilizado no universo de aplicações java. O meu primeiro contato com o MVC foi em um projeto desenvolvido para o Banco Votorantin. Este projeto não possuia telas amigáveis para o usuário, pois era executado via linha de comando, mas enfim, com este projeto eu obtive uma boa base de como o MVC funciona em uma aplicação real e neste artigo eu irei compartilhar este meu conhecimento com vocês.

Apesar de a minha primeira experiência ter sido com uma aplicação robô (assim eram chamadas as aplicações Java que eram executadas via linha de comando), o MVC se aplica a outros sistemas, como sistemas java web (seja usando Struts, seja JSF ou mesmo o VRaptor).

Muitas aplicações reais utilizam este Design Pattern, pois facilitam, e muito, no desenvolvimento e principalmente na manutenção e expansão da aplicação.


## Mas o que é MVC?

Como eu já havia começado a descrever logo no começo, MVC é uma sigla para Model-View-Controller e é um Design Pattern muito utilizado atualmente. Ele consiste em uma estrutura que dividi o seu código em 3 camadas:

* **Model:** camada responsável pela comunicação com o banco de dados assim como o tratamento dos dados, como por exemplo, aplicação de regras de negócio em cima dos dados coletados.
* **View:** camada responsável pela exibição dos dados, para isso ela obtém os dados passados pela camada de controle para obter os dados e apenas exibi-los da melhor forma, seja exportando em um arquivo, seja mostrando valores em telas.
* **Controller:** camada responsável por controlar os dados de entrada do sistema, trabalhando algumas informações obtidas e enviando para a camada de Model para que a mesma aplique regras de negócio e persista a informação. Da mesma forma, é responsável pelos dados de saída, ou seja, chamar a camada de View a qual irá exibir a informação ao usuário.

E esta foi uma explicação rápida sobre o que é e como funciona o MVC. A figura abaixo ilustra de forma simples o que foi explicado:

<!--- ![MVC Puro]({{ site.url }}/assets/MVCPuro.png) -->
![MVC Puro](/assets/MVCPuro.png)

Porém isto é apenas o conceito do MVC, mas como será que ele é aplicado em um sistema real?


## MVC em aplicações reais Java

Em aplicações reais desenvolvidas com a linguagem Java é normal você se deparar com algo similar a seguinte estrutura:

![MVC Puro](/assets/MVCPratica.png)

E enfim, vamos a uma explicação mais detalhada sobre a funcionalidade de cada camada do modelo acima.


## Entidade

A entidade é a parte do seu código que representa as tabelas ou views do seu banco de dados. Aqui eu estou chamando de entidade, mas você pode acabar se deparando com diversos outros nomes, como por exemplo Value Object ou VO, Domain Object ou DO, Data-Transfer Objects ou DTO, entre outros. Em minha pequena experiência de trabalho, já me deparei com todos os citados acima. Apesar de nomenclaturas diferentes, eles exercem o mesmo papel dentro da camada de Entidade: ser um espelho do seu banco de dados.

As entidades são implementadas em java na forma de [JavaBeans](http://pt.wikipedia.org/wiki/JavaBeans), ou seja, possuem as variáveis com acesso privado e proveem um método para o acesso à estas variáveis seguindo os padrões do JavaBeans (getters e setters). De forma geral, cada atributo da sua classe de entidade será equivalente a uma coluna de uma tabela de seu banco de dados, assim como cada classe de entidade será equivalente a uma tabela ou uma view do seu banco de dados. Pensando de forma orientada a objetos, cada instância da sua classe de entidade será equivalente a uma linha da tabela a qual a classe de entidade se espelha.


## Camada de Persistência

A camada de persistência é a camada que possui acesso ao banco e assim insere dados novos, atualiza algumas informações e as deleta quando necessário, além de ser capaz de executar pesquisas para localizar essas informações salvas. Aqui você encontrará os **Data Access Objects** ou simplesmente **DAOs**. Assim como foi comentado que a Entidade pode ter sido nomeada de forma diferente no projeto em que você trabalha, a camada de persistência também pode. O importante é intender o papel desta camada, saber exatamente o que ela faz e evitar ao máximo fugir do escopo dela para que desta forma seja criado um código claro, além de facilitar a manutenção ou expansão deste código no futuro.

Seguindo a mesma linha de raciocínio da entidade, cada classe de DAO existente no seu código irá possuir uma ou mais classe de entidades. Isto se dá porque o DAO será o encarregado de saber como **inserir**, **atualizar** e **deletar** esta informação no banco de dados (lembre-se que de forma geral, uma instância da sua classe de entidade equivale a uma linha da tabela), além de saber **consultar** no banco as informações salvas anteriormente. Estas quatro operações citadas (inserção, busca, atualização e exclusão) são mais conhecidas como operações **CRUD** o que nada mais é do que a sigla de cada operação em inglês (<b>C</b>reate, <b>R</b>ead, <b>U</b>pdate e <b>D</b>elete).


## Camada de Serviço

A camada de serviço é a camada que executa chamadas à camada de persistência e trata os dados obtidos aplicando as regras de negócio do sistema. O ideal é isolar todas as regras de negócio nesta camada focando assim em um código mais limpo e de fácil entendimento, além de auxiliar na manutenção deste código. Esta camada não pode nunca executar chamadas diretas ao banco de dados. Para ter acesso ao banco é preciso obrigatoriamente acessar a camada de persistência.

Regra de negócio são regras especificas do sistema, como por exemplo o tratamento específico de um dado para ser exibido na camada de exibição ou inserido no banco de dados. Aplicar uma taxa de juros em um valor ou executar um cálculo faz parte da regra de negócio e é exatamente o tipo de código que você poderá encontrar nesta camada. Então de forma resumida, o papel desta camada é obter as informações da camada de persistência e tratar estas informações de acordo com as regras do sistema.


## Camada de Controle

A camada de controle é a camada que executa chamadas à camada de serviço para obter os dados que serão exibidos ou para enviar os dados a serem persistidos no banco de dados. é uma camada que serve de forma intermediadora entre a camada de exibição e a camada que detém as regras de negócio, obtendo os dados e os enviando para a exibição. Nenhuma regra de negócio deve constar nesta camada, pois assim o seu código acaba sendo dividido e irá dificultar a legibilidade e manutenção, além de futuramente poder acarretar em uma duplicação de código.


## Exibição

A exibição é como o próprio nome diz, a aparência do seu sistema ou a forma de entrada e saída do seu sistema. A exibição pode ser representada por telas de sistema, por arquivos, pelo console, entre outras formas. é aqui que você receberá os dados de entrada que serão mandados para a camada de controle e receberá a resposta do processamento da sua informação. Em sistemas que possuem telas, por exemplo, você pode ter uma tela com um formulário para o usuário preencher um cadastro (forma de entrada dos dados) e após enviar para a camada de controle as informações cadastradas, pode querer exibir uma tela confirmando os dados que foram salvos (forma de saída dos dados) com sucesso no banco de dados.


## Falando um pouco mais sobre o assunto

De forma resumida, o MVC é um design pattern que visa manter o seu código mais limpo e melhor estruturado. Como consequência disto você tornará o seu código mais legível e facilitará alterações futuras e até mesmo a expansão do projeto. Cada camada pode aplicar um ou mais frameworks disponíveis no mercado para auxiliar na melhor resolução do problema.

Todas as camadas possuem acesso direto a classe de entidade para ler os dados, porém para tratar as informações é necessário seguir um fluxo:

* A **camada de controle** precisa chamar a **camada de serviço** para obter ou enviar as informações desejadas.
  * A **camada de serviço** por sua vez, precisa obter as informações obtidas da camada de controle (se existirem) e aplicar as devidas regras de negócio (se for necessário) para só então chamar a **camada de persistência**.
    * A **camada de persistência** receberá as informações passadas e irá processá-las no banco de dados, seja fazendo consultas, seja persistindo. Ao finalizar a operação no banco, esta camada deve **retornar** alguma informação para a camada que a chamou (camada de serviço), seja uma informação de sucesso (para quando um dado foi persistido no banco de dados), seja os valores retornados de uma pesquisa.
  * A **camada de serviço** recebe as informações obtidas da **camada de persistência** e aplica as regras de negócio novamente em cima delas (se for necessário), tratando as informações e deixando-as prontas para serem exibidas para o usuário.
* A **camada de controle** recebe as informações obtidas da **camada de serviço** e as envia para a **exibição** do sistema.

Acredito então ter coberto de forma geral o assunto, mas posso ter me esquecido de falar de alguma coisa. Caso eu perceba que faltou algo ou que uma parte não ficou muito bem explicada, irei alterar e complementar este artigo. Enfim, **dúvidas**?
