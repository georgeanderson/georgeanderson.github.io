---
layout: post
title: Criando um Projeto JEE com Maven
tags:
- maven java jee
---
A algumas semanas uns colegas no trabalho receberam a incubência de criar uma aplicação do zero. A decisão foi por fazer uma aplicação JEE, utilizando JSF no front-end e EJB + JPA no back-end. Fui acionado para dar uma consultoria ao time e então nos reunimos para revisar pontos importantes da arquitetura, design e execução do projeto. Quando o assunto entrou em build, eu e outro amigo que também estava como consultor fomos unânimes em indicar o Maven. Como grande parte do time não tinha conhecimento no assunto, foi então que propus a seguinte estrutura.
Primeiro preferi adotar a última versão do Maven (no momento a 3.0.4). Segundo tentei usar um arquétipo padrão fornecido pelo próprio Maven[1], o maven-archetype-j2ee-simple.

{% highlight bash %}
$ mvn archetype:generate -DgroupId=com.company.docgen -DartifactId=docgen_jee \
-DarchetypeArtifactId=maven-archetype-j2ee-simple
{% endhighlight %}

Parecia ser promissor mas a estrutura criada por ele é um tanto confusa na minha opnião. Nâo fiquei muito certo aonde colocar o quê.

	.
	├── ear
	├── ejbs
	├── pom.xml
	├── primary-source
	├── projects
	├── servlets
	└── src
	    └── main
	        └── resources

Bom que não precisamos ficar restritos ao que o Maven oferece por padrão, muito menos ter que escrever nosso pom.xml na mão. Foi aí que larguei mão do uso de arquétipos produzidos por terceiros. Uma lista completa pode ser facilmente produzida com o comando abaixo.

{% highlight bash %}
$ mvn archetype:generate | tee archetypes.txt
{% endhighlight %}

Depois é só sair vendo o que mais se aproxima com o tipo de projeto que estamos querendo criar. No meu caso procurei direto por j22 e encontrei o seguinte:

{% highlight bash %}
$ cat archetypes.txt | grep jee
160: remote -> net.thejeearchitectcookbook:jsf-jqmobile-archetype 
	(JSF 2 and JQuery Mobile basic web archetype.)
382: remote -> org.codehaus.mojo.archetypes:appclient-jee5 (-)
386: remote -> org.codehaus.mojo.archetypes:ear-jee5 (-)
390: remote -> org.codehaus.mojo.archetypes:ejb-jee5 (-)
400: remote -> org.codehaus.mojo.archetypes:webapp-jee5 (-)
426: remote -> org.fluttercode.knappsack:jee6-basic-archetype (-)
427: remote -> org.fluttercode.knappsack:jee6-minimal-archetype (-)
428: remote -> org.fluttercode.knappsack:jee6-sandbox-archetype (-)
429: remote -> org.fluttercode.knappsack:jee6-sandbox-demo-archetype (-)
430: remote -> org.fluttercode.knappsack:jee6-servlet-basic-archetype (-)
431: remote -> org.fluttercode.knappsack:jee6-servlet-demo-archetype (-)
432: remote -> org.fluttercode.knappsack:jee6-servlet-minimal-archetype (-)
433: remote -> org.fluttercode.knappsack:jee6-servlet-sandbox-archetype (-)
453: remote -> org.imixs.application:imixs-workflow-jee-archetype 
	(Imixs Workflow JEE Archetype provides a JEE Sample Application)
482: remote -> org.jboss.weld.archetypes:weld-jsf-jee 
	(Weld archetype for creating a Java EE 6 application using JSF 2.0, 
	CDI 1.0, EJB 3.1 and JPA 2.0 (persistence unit included))
483: remote -> org.jboss.weld.archetypes:weld-jsf-jee-minimal 
	(Weld archetype for creating a minimal Java EE 6 application using JSF 2.0, 
	CDI 1.0 and EJB 3.1 (persistence unit not included))
{% endhighlight %}

Os números 386, 390 e 400 pareciam ser o que eu estava buscando. Para gerar o projeto usando esses arquétipos podemos executar a mesma linha de comando usada acima, só trocando maven-archetype-j2ee-simple pelo nome do arquétipo desejado. No entanto a forma que preferi foi digitando apenas: $ mvn archetype:generate. Nesse modo o Maven vai perguntar qual o número do arquétipo e seguir com a geração, simples assim. Gerei os 3 projetos em separados e dentro da mesma pasta ficando assim:

	├── docgen_ear
	├── docgen_ejb
	├── docgen_web
	└── pom.xml

O grande lance é fazer com que esses 3 projetos se tornem interligados por um único build. Criei um pom.xml na mão com o conteúdo abaixo e o coloquei na mesma pasta dos outros projetos:

{% highlight xml %}
[pom.xml]
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" 
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.company.docgen</groupId>
  <artifactId>docgen</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging>
  <modules>
    <module>docgen_ear</module>
    <module>docgen_ejb</module>
    <module>docgen_web</module>
  </modules>
</project>
{% endhighlight %}

Esse pom.xml uniu os 3 projetos em um único. Resta apenas referenciar esse pom.xml em cada um dos projetos, completando assim o link entre eles.

{% highlight xml %}
  <parent>
    <groupId>com.company.docgen</groupId>
    <artifactId>docgen</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>
{% endhighlight %}

Outro dia recebi e-mail dos caras dizendo que já fizeram até deploy da aplicação usando o build acima. Parece que funciona mesmo.

Referências:
[1]: http://maven.apache.org/guides/introduction/introduction-to-archetypes.html
[2]: http://maven.apache.org/guides/getting-started/maven-in-five-minutes.html
