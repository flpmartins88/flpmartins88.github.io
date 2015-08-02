---
layout: post
title:  Removendo acentos de uma forma bem rápida em Java
date:   2015-08-02 16:52:27
categories: Java
---

Para remover acentos sempre fiquei naquele maldito replace que vai substituindo letra por letra, dava um trabalhão.

Resolvi pesquisar para ver se encontrava alguma solução mais prática para resolver meu problema.

Eis que encontro o salvador já incluído no Java desde a versão 6:

{% highlight java %}
    Normalizer.normalize(address, Form.NFD).replaceAll("\\p{InCombiningDiacriticalMarks}+", "");
{% endhighlight %}

A mágica (eu sei que não é um mágina) é feita pela combinação da classe normalizer com a expressão regular \\\\p{InCombiningDiacriticalMarks}+ que retira apenas os acentos do texto.

E eu apanhando há um bom tempo disso.
