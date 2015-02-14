Tuplas em Python: são imutáveis mas seu valor pode mudar
========================================================

Por Luciano Ramalho, autor de `Fluent Python`_

Tuplas em Python têm uma característica surpreendente: elas são imutáveis, mas seus valores podem mudar. Isso pode acontecer quando uma ``tupla`` contém uma referência para qualquer objeto mutável, como uma ``lista``. Se você precisar explicar isso a um colega iniciando com Python, um bom começo é destruir o senso comum sobre variáveis serem como caixas em que armazenamos dados.

Em 1997 participei de um curso de verão sobre Java no MIT. O professor, Lynn Andrea Stein - um premiado educador em ciência da computação - enfatizou que a habitual metáfora de "variáveis como caixas" acaba atrapalhando o entendimento sobre variáveis de referência em linguagens OO. Variáveis em Python são como variáveis de referência em Java, portanto é melhor pensar nelas como etiquetas afixadas em objetos.

Eis um exemplo inspirado no livro *Alice Através do Espelho e O Que Ela Encontrou Por Lá*, de Lewis Carroll.

.. image:: Tweedledum-Tweedledee_500x390.png

Tweedledum e Tweedledee eram gêmeos. Do livro: “Alice soube no mesmo instante qual era qual porque um deles tinha 'DUM' bordado na gola e o outro, 'DEE'”.

.. image:: diagrams/dum-dee.png

Vamos representá-los como tuplas contendo a data de nascimento e uma lista de suas habilidades::

    >>> dum = ('1861-10-23', ['poesia', 'nervosinho'])
    >>> dee = ('1861-10-23', ['poesia', 'nervosinho'])
    >>> dum == dee
    True
    >>> dum is dee
    False
    >>> id(dum), id(dee)
    (4313018120, 4312991048)

É claro que ``dum`` e ``dee`` referem-se a objetos que são iguais, mas que não são o mesmo objeto. Eles tem identificadores diferentes.

Agora, depois dos eventos testemunhados por Alice, Tweedledum decidiu ser um rapper, adotando o nome artístico T-Doom. Podemos expressar isso em Python dessa forma::

    >>> t_doom = dum
    >>> t_doom
    ('1861-10-23', ['poesia', 'nervosinho'])
    >>> t_doom == dum
    True
    >>> t_doom is dum
    True

Então, ``t_doom`` e ``dum`` são iguais - mas Alice pode entender ser tolice dizer isso, porque ``t_doom`` e ``dum`` referem-se à mesma pessoa: ``t_doom is dom``.

.. image:: diagrams/dum-t_doom-dee.png

Os nomes ``t_doom`` e ``dum`` são apelidos. Agrada-me ver que nos documentos oficiais do Python muitas vezes referem-se a variáveis como “nomes“. Variáveis são nomes que damos a objetos. Nomes alternativos são apelidos. Isso ajuda a limpar da nossa mente a ideia de que variáveis são como caixas. Qualquer um que pense variáveis sendo caixas, não pode entender o que vem a seguir.

Depois de muito praticar, T-Doom é agora um rapper experiente. Codificando, foi isso o que aconteceu:

    >>> skills = t_doom[1]
    >>> skills.append('rap')
    >>> t_doom
    ('1861-10-23', ['poesia', 'nervosinho 'rap'])
    >>> dum
    ('1861-10-23', ['poesia', 'nervosinho 'rap'])

T-Doom conquistou a habilidade ``rap``, e também Tweedledum — óbvio, pois eles são um e o mesmo. Se ``t_doom`` fosse uma caixa contendo dados do tipo ``str`` e ``list``, como você poderia explicar a inclusão à lista ``t_doom`` também altera a lista na caixa ``dum``?  Contudo, é perfeitamente plausível se você entende variáveis como etiquetas.

A analogia da etiqueta é muito melhor porque apelidos são explicados mais facilmente como um objeto com duas ou mais etiquetas. No exemplo, ``t_doom[1]`` e ``skills`` são dois nomes dados ao mesmo objeto da lista, da mesma forma que ``dum`` e ``t_doom`` são dois nomes dados ao mesmo objeto da tupla.

Abaixo está uma ilustração alternativa dos objetos que representam Tweedledum. Esta figura enfatiza o fato de a tupla possuir referências a objetos.

.. image:: diagrams/dum-skills-references.png

Imutável é o conteúdo físico de uma tupla, que contém apenas referências a objetos. O valor da lista referenciado por ``dum[1]`` mudou, mas o identificador do objeto referenciado permanece o mesmo. Uma tupla não tem meios de prevenir mudanças nos valores de seus itens, que são objetos independentes e podem ser encontrados através de referencias fora da tupla, como o nome ``skills`` que nós usamos anteriormente. Listas e outros objetos imutáveis dentro de tuplas podem ser alterados, mas seus identificadores serão sempre os mesmos.

Isso enfatiza a diferença entre os conceitos de identidade e valor, descritos em *Python Language Reference*, no capítulo `Data model`_::

    Cada objeto tem um identificador, um tipo e um valor. A identificação de um
    objeto nunca muda, uma vez que tenha sido criado; você pode pensar como se
    fosse o endereço do objeto na memória. O operador ‘is’ compara o
    identificador de dois objetos; a função id() retorna um inteiro
    representando o seu identificador.

Após ``dum`` tornar-se um rapper, os irmãos gêmeos não são mais iguais::

    >>> dum == dee
    False

Nós temos duas tuplas que foram criadas iguais, mas agora elas são diferentes.

O outro tipo interno de coleção imutável em Python, ``frozenset``, não sofre do problema de ser imutável mas com possibilidade de mudar seus valores. Isso ocorre porque um ``frozenset`` (ou um ``set`` simples, nesse sentido) pode apenas conter referências a objetos ``hashable`` (objetos que podem ser usados como chave em um dicionário), cujo valor, por definição, nunca pode mudar.

Tuplas são comumente usadas como chaves para objetos ``dict``, e precisam ser ``hashable`` - assim como os elementos de um conjunto. Então, as tuplas são ``hashables`` ou não? A resposta certa é **algumas** tuplas são. O valor de uma tupla contendo um objeto mutável pode mudar, e como uma tupla, não é ``hashable``. Para ser usada como chave para um objeto ``dict`` ou elemento de um objeto ``set``, a tupla precisa ser constituída apenas de objetos ``hashable``. Nossas tuplas de nome ``dum`` e ``dee`` não são ``hashable`` porque cada elemento contem uma referência a uma lista, e listas não são ``hashable``.

Agora vamos nos concentrar nos comandos de atribuição que são o coração de todo esse exercício.

A atribuição em Python nunca copia valores. Ela apenas copia referências. Então quando eu escrevi ``skills = t_doom[1]`` eu não copiei a lista referenciada por ``t_doom[1]``, eu apenas copiei a referência a ela, que eu então usei para alterar a lista executando ``skills.append('rap')``.

Voltando ao MIT, o Prof. Stein falou sobre atribuição de uma forma muito direcionada. Por exemplo, quando falando sobre um objeto gangorra em uma simulação, ele dizia: “A variável ``s`` é atribuída à gangorra“, mas nunca “A gangorra é atribuída à variável ``s`` “. Como variáveis de referência ele tornou muito mais coerente dizer que a variável é atribuída ao objeto, e não outra forma. Afinal, o objeto é criado antes da atribuição.

Em uma atribuição como ``y = x * 10``, o lado direito é computado primeiro. Isto cria um novo objeto ou retorna um já existente. Somente após o objeto ser computado ou retornado, o nome é atribuído a ele.

Aqui está o código demonstrando. Primeiro nós criamos uma classe ``Gizmo``, e uma instância dela::

    >>> class Gizmo:
    ...     def __init__(self):
    ...         print('Gizmo id: %d' % id(self))
    ...
    >>> x = Gizmo()
    Gizmo id: 4328764080

Observe que o método ``__init__`` mostra o identificador do objeto tão logo criado. Isso será importante na próxima demonstração.

Now let's instantiate another ``Gizmo`` and immediately try to perform an operation with it before binding a name to the result::

    >>> y = Gizmo() * 10
    Gizmo id: 4328764360
    Traceback (most recent call last):
      ...
    TypeError: unsupported operand type(s) for *: 'Gizmo' and 'int'
    >>> 'y' in globals()
    False

This snippet shows that the new object was instantiated (its id was ``4328764360``) but before the ``y`` name could be created, a ``TypeError`` aborted the assignment. The ``'y' in globals()`` check proves there is no ``y`` global name.

To wrap up: always read the right-hand side of an assignment first. That’s where the object is created or retrieved. After that, the name on the left is bound to the object, like a label stuck to it. Just forget about the boxes.

As for tuples, make sure they only hold references to immutable objects before trying to use them as dictionary keys or put them in sets.

    This post was based on chapter 8 of my `Fluent Python`_ book. That chapter, titled *Object references, mutability and recycling* also covers the semantics of function parameter passing, best practices for mutable parameter handling, shallow copies and deep copies, and the concept of weak references — among other topics. The book focuses on Python 3 but most of its content also applies to Python 2.7, like everything in this post.

.. _Fluent Python: http://shop.oreilly.com/product/0636920032519.do
.. _Data Model: https://docs.python.org/3/reference/datamodel.html#objects-values-and-types
