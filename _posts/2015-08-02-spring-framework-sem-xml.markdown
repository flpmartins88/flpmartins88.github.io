---
layout: post
title:  Configuração do Spring Framework sem usar XML
date:   2015-08-02 15:40:10
categories: Java SpringFramework
---

Olá,

Aqui vai a configuração do Spring Framework sem o uso de XML.

O Spring é um Framework de injeção de dependência. Basicamente, o que o Spring faz é instanciar as classes para você. Claro que, para você que não tem experiência em usar algo do tipo deve pensar que você mesmo pode instanciar suas classes, também pensei isso quando vi pela primeira vez. Mas acredite em mim, o Spring tem um monte de recursos legais que valem a pena.

Inicialmente, você vai precisar dos Jars do Spring Framework, se você usa Maven, só vai precisar disso:

{% highlight xml %}
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>4.1.0.RELEASE</version>
</dependency>
{% endhighlight %}

Ou se você usa Gradle:

{% highlight groovy %}
    compile 'org.springframework:spring-context:4.1.0.RELEASE'
{% endhighlight %}

Crie um projeto na IDE de sua preferência e crie uma classe para as configurações do Spring. Segue a minha classe de exemplo:

{% highlight java %}
package com.wordpress.flpmartins88.springsemxml.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = {"com.wordpress.flpmartins88.springsemxml"})
public class ApplicationConfig {

}
{% endhighlight %}

Não é tudo isso que faz a mágica. Explicando algumas coisas:

<ul>
    <li>@Configuration: Essa anotação diz para o Spring Framework que essa classe possui as configurações. Pode haver mais de uma no projeto</li>
    <li>@ComponentScan: Essa classe diz para o Spring onde ele vai encontrar todas as classes que ele vai usar.</li>
</ul>


Agora uma classe para testar:

{% highlight java %}
package com.wordpress.flpmartins88.springsemxml;

import org.springframework.stereotype.Component;

@Component
public class Mensagem {

    public String getMensagem() {
        return "um texto qualquer";
    }

}
{% endhighlight %}

A anotação @Component diz para o Spring que aquela classe é um recurso que será usado pelo Spring Framework. Repare que o pacote onde está a classe fica no mesmo pacote onde foi declarado pelo Spring, ele não precisa estar no mesmo, mas a sua base deve ser igual.

Agora só falta uma classe para testar:

{% highlight java %}
public class Main {

    public static void main(String[] args) {

        ApplicationContext context = new AnnotationConfigApplicationContext(ApplicationConfig.class);
        Mensagem mensagem = context.getBean(Mensagem.class);
        System.out.println(mensagem.getMensagem());
    }

}
{% endhighlight %}

A classe AnnotationConfigApplicationContext é uma classe do Spring que é responsável por procurar os arquivos de configuração e iniciar o Framework, ela recebe como parâmetro o class de um arquivo que contenha as configurações do Spring.

Com isso, você só precisa chamar o método getBean() do ApplicationContext e pedir um objeto para o Spring, ele vai te entregar o objeto instanciado, pronto para uso.

Você também pode usar a anotação @Bean para definir um objeto para o Spring.

No exemplo do acima ficaria assim:

{% highlight java %}
@Configuration
@ComponentScan(basePackages = {&amp;amp;quot;com.wordpress.flpmartins88.springsemxml&amp;amp;quot;})
public class ApplicationConfig {

    @Bean
    public Mensagem getMensagem() {
        return new Mensagem();
    }

}
{% endhighlight %}

O exemplo acima pode ser usado no lugar da anotação @Component.

Bom, esse é o básico da configuração, é possível injetar um bean dentro de algum objeto, mas isso não é assunto para esse post.

[Site do Spring][spring-link]

Você pode ver o código fonte do projeto [aqui][fonte-projeto].

Até.

[spring-link]:         http://hibernate.org/orm/
[fonte-projeto]:       https://github.com/flpmartins88/SpringSemXml
