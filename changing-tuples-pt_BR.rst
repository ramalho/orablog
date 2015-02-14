Tuplas em Python: são imutáveis mas seu valor pode mudar
========================================================

Por Luciano Ramalho, autor de `Fluent Python`_

Tuplas em Python têm uma característica surpreendente: elas são imutáveis, mas seus valores podem mudar. Isso pode acontecer quando uma ``tupla`` contém uma referência para qualquer objeyo mutável, como uma ``lista``. Se você precisar explicar isso a um colega iniciando com Python, um bom começo é destruir o senso comum sobre variáveis serem como caixas em que armazenamos dados.

Em 1997 participei de um curso de verão sobre Java no MIT. O professor, Lynn Andrea Stein - um premiado educador em ciência da computação - enfatizou que a habitual metáfora de "variáveis como caixas" acaba atrapalhando o entendimento sobre variáveis de referência em linguagens OO. Variáveis em Python são como variáveis de referência em Java, portanto é melhor pensar nelas como etiquetas afixadas em objetos.

Eis um exemplo inspirado no livro *Alice Através do Espelho e O Que Ela Encontrou Por Lá*, de Lewis Carroll.

.. image:: Tweedledum-Tweedledee_500x390.png

Tweedledum e Tweedledee eram gêmeos. Do livro: “Alice soube no mesmo instante qual era qual porque um deles tinha 'DUM' bordado na gola e o outro, 'DEE'”.

.. image:: diagrams/dum-dee.png

Vamos representa-los como tuplas contendo a data de nascimento e uma lista de suas habilidades::

    >>> dum = ('1861-10-23', ['poesia', 'nervosinho'])
    >>> dee = ('1861-10-23', ['poesia', 'nervosinho'])
    >>> dum == dee
    True
    >>> dum is dee
    False
    >>> id(dum), id(dee)
    (4313018120, 4312991048)

É claro que ``dum`` e ``dee`` referem-se a objetos que são iguais, mas que não são o mesmo objeto. Eles tem identificadores diferentes.

Agora, depois dos eventos testemunhados por Alice, Tweedledum decidiu ser um rapper, adotanto o nome artístico T-Doom. Podemos expressar isso em Python dessa forma::

    >>> t_doom = dum
    >>> t_doom
    ('1861-10-23', ['poesia', 'nervosinho'])
    >>> t_doom == dum
    True
    >>> t_doom is dum
    True

Então, ``t_doom`` e ``dum`` são iguais - mas Alice pode entender ser tolice dizer isso, porque ``t_doom`` e ``dum`` referem-se à mesma pessoa: ``t_doom is dom``.

.. image:: diagrams/dum-t_doom-dee.png

Os nomes ``t_doom`` e ``dum`` são apelidos. Agrada-me ver que nos documentos oficiais do Python muitas vezes referem-se a variáveis como “nomes“. Variáveis são nomes que damos a objetos. Nomes alternativos são apelidos. Isso ajuda a limpar da nossa mente a ideia de que variáveis são como caixas. Qualquer um que pense variáveis sendo caixas, não pode enteder o que vem a seguir.

Depois de muito praticar, T-Doom é agora um rapper experiente. Codificando, foi isso o que aconteceu:

    >>> skills = t_doom[1]
    >>> skills.append('rap')
    >>> t_doom
    ('1861-10-23', ['poesia', 'nervosinho 'rap'])
    >>> dum
    ('1861-10-23', ['poesia', 'nervosinho 'rap'])

T-Doom conquistou a habilidade ``rap``, e também Tweedledum — óbvio, pois eles são um e o mesmo. Se ``t_doom`` fosse uma caixa contendo dados do tipo ``str`` e ``list``, como você poderia explicar a inclusão à lista ``t_doom`` também altera a lista na caixa ``dum``?  Contudo, é perfeitamente plausível se você entende variáveis como etiquetas.

A analogia da etiqueta é muito melhor porque apelidos são explicados mais facilmente como um objeto com duas ou mais etiquetas. No exemplo, ``t_doom[1]`` e ``skills`` são dois nomes dados ao mesmo objeto da lista, da mesma forma que ``dum`` e ``t_doom`` são dois nomes dados ao mesmo objeto da tupla.

Below is an alternative depiction of the objects that represent Tweedledum. This figure emphasizes that a tuple holds references to objects.

.. image:: diagrams/dum-skills-references.png

What is immutable is the physical content of a tuple, consisting of the object references only. The value of the list referenced by ``dum[1]`` changed, but the referenced object id is still the same. A tuple has no way of preventing changes to the values of its items, which are independent objects and may be reached through references outside of the tuple, like the ``skills`` name we used earlier. Lists and other mutable objects inside tuples may change, but their ids will always be the same.

This highlights the difference between the concepts of identity and value, described in *Python Language Reference* `Data model`_ chapter:

    Every object has an identity, a type and a value. An object’s identity never changes once it has been created; you may think of it as the object’s address in memory. The ‘is’ operator compares the identity of two objects; the id() function returns an integer representing its identity.

After ``dum`` became a rapper, the twin brothers are no longer equal::

    >>> dum == dee
    False

We have two tuples that were created equal, but now they are different.

The other built-in immutable collection type in Python, ``frozenset``, does not suffer from the problem of being immutable yet potentially changing in value. That's because a ``frozenset`` (or a plain ``set``, for that matter) may only hold references to hashable objects, and the value of hashable objects may naver change, by definition.

Tuples are commonly used as ``dict`` keys, and those must be hashable — just as set elements. So, are tuples hashable or not? The right answer is: **some** tuples are hashable. The value of a tuple holding a mutable object may change, and such a tuple is not hashable. To be used as a ``dict`` key or set element, the tuple must be made only of hashable objects. Our tuples named ``dum`` and ``dee`` are unhashable because each contains a list reference, and lists are unhashable.    

Now let's focus on the assignment statements at the heart of this whole exercise.

Assignment in Python never copies values. It only copies references. So when I wrote ``skills = t_doom[1]`` I did not copy the list at ``t_doom[1]``, I only copied a reference to it, which I then used to change the list by doing ``skills.append('rap')``. 

Back at MIT, Prof. Stein spoke about assignment in a very deliberate way. For example, when talking about a seesaw object in a simulation, she would say: “Variable ``s`` is assigned to the seesaw”, but never “The seesaw is assigned to variable ``s``”. With reference variables it makes much more sense to say that the variable is assigned to an object, and not the other way around. After all, the object is created before the assignment.

In an assignment such as ``y = x * 10``, the right-hand side is evaluated first. This creates a new object or retrieves an existing one. Only after the object is constructed or retrieved, the name is assigned to it.

Here is proof in code. First we create a ``Gizmo`` class, and an instance of it::

    >>> class Gizmo:
    ...     def __init__(self):
    ...         print('Gizmo id: %d' % id(self))
    ...
    >>> x = Gizmo()
    Gizmo id: 4328764080

Note that the ``__init__`` method displays the id of the object just created. This will be important in the next demonstration.

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
