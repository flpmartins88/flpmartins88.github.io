---
layout: post
title:  Configuração do Hibernate 4 sem usar XML"
date:   2015-08-02 14:57:27
categories: Java Hibernate
---

Sempre odiei usar XML para configurar frameworks.

O Hibernate é um framework ORM, ele basicamente transforma a visão estruturada do banco de dados em algo que possa ser melhor compreendido no mundo da Orientação a Objetos.

Aqui vai a configuração do Hibernate 4 sem o uso de XML.

[Você pode obter o Hibernate 4 aqui][hibernate-link]

Se você usa Maven:

{% highlight xml %}
<dependency>
   <groupId>org.hibernate</groupId>
   <artifactId>hibernate-core</artifactId>
   <version>4.3.6.Final</version>
</dependency>
{% endhighlight %}

Ou se você usa Gradle:

{% highlight groovy %}
    compile 'org.hibernate:hibernate-core:4.3.6.Final'
{% endhighlight %}

Crie um projeto na IDE de sua preferência e adicione as dependências.

Crie uma classe para criar as sessões. As sessões são as conexões do banco de dados para o Hibernate. Eu criei uma classe chamada HibernateUtils.java.

Segue o fonte:

{% highlight java %}
import org.hibernate.SessionFactory;
import org.hibernate.boot.registry.StandardServiceRegistryBuilder;
import org.hibernate.cfg.AvailableSettings;
import org.hibernate.cfg.Configuration;
import org.hibernate.service.ServiceRegistry;</code>

public class HibernateUtils {

    private static SessionFactory sessionFactory;

    static {
        Configuration c = new Configuration();

        c.addAnnotatedClass(Pessoa.class);

        c.setProperty(AvailableSettings.DIALECT, "org.hibernate.dialect.MySQLDialect");
        c.setProperty(AvailableSettings.DRIVER, "com.mysql.jdbc.Driver");
        c.setProperty(AvailableSettings.USER, "root");
        c.setProperty(AvailableSettings.PASS, "root");
        c.setProperty(AvailableSettings.URL, "jdbc:mysql://localhost:3306/test");

        c.setProperty(AvailableSettings.HBM2DDL_AUTO, "create");

        ServiceRegistry registry = new StandardServiceRegistryBuilder().applySettings(c.getProperties()).build();

        sessionFactory = c.buildSessionFactory(registry);

    }

    public static SessionFactory getSessionFactory() {
        return sessionFactory;
    }

}
{% endhighlight %}

As configurações são feitas através do Objeto Configuration e são basicamente:
<ul>
    <li>Classes de entidades: configuradas através do método addAnnotatedClass(class);</li>
    <li>Propriedades:</li>
    <ul>
        <li>Dialect: Linguagem de banco de dados que será usada, no exemplo utilizei o MySQL;</li>
        <li>Driver: Uma string com o driver do banco de dados</li>
        <li>User: usuário do banco de dados</li>
        <li>Pass: Senha do banco de dados;</li>
        <li>URL: URL de conexão do banco de dados</li>
    </ul>
</ul>

A ultima propriedade é uma configuração especial do Hibernate em que ele executa algumas ações no banco de dados. Existem 4 valores que podem ser setados:

<ul>
    <li>update: Atualiza o banco de dados;</li>
    <li>create: Cria o banco de dados;</li>
    <li>create-drop: Apaga tudo e recria o banco de dados;</li>
    <li>validate: Apenas valida se todos os campos mapeados realmente existem no banco de dados assim como seus relacionamentos e tipo de dados;</li>
</ul>

Agora vamos criar uma classe para fazer o teste, eu criei uma chamada Pessoa com apenas dois campos, id e nome:

{% highlight java %}
@Entity
public class Pessoa implements Serializable {

    @Id @GeneratedValue
    private Long id;

    private String nome;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getNome() {
        return nome;
    }

    public void setNome(String nome) {
        this.nome = nome;
    }
}
{% endhighlight }

E agora só colocar uma classe para testar:

{% highlight java %}
public class Main {

    public static void main(String[] args) {

        Session session = HibernateUtils.getSessionFactory().openSession();


        session.beginTransaction();

        Pessoa p1 = new Pessoa();
        p1.setNome("Felipe");
        session.save(p1);

        Pessoa p2 = new Pessoa();
        p2.setNome("Ana");
        session.save(p2);

        session.getTransaction().commit();

        for (Pessoa p:(List) session.createCriteria(Pessoa.class).list()) {
            System.out.println(p.getNome());
        }

        session.close();

        System.exit(0);
    }

}
{% endhighlight }

Se tudo funcionar corretamente, será criado dois registros na tabela e logo em seguida serão listados.

Em caso de dúvidas, você pode obter o [fonte aqui][fonte-projeto].

[hibernate-link]:      http://hibernate.org/orm/
[fonte-projeto]:       https://github.com/flpmartins88/hibernate-basico-sem-xml
