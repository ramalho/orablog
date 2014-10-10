
Python tuples have a surprising trait: they are immutable, but their values may change. This may happen when a tuple holds a reference to any mutable type, such as a list. If you need to explain this to a colleague who is new to Python, a good first step is to debunk the common belief that variables are like boxes where you put data.

In 1997 I took a summer course about Java at MIT. The professor, Lynn Andrea Stein — an award-winning computer science educator — made the point that the usual “variables as boxes” metaphor actually hinders the understanding of reference variables in OO languages. Python variables are like reference variables in Java, so it’s better to think of them as labels attached to objects.

Here is an example inspired by Lewis Carrol's Through the Looking-Glass, and What Alice Found There.

Tweedledum and Tweedledee are identical twins. We'll represent them as tuples with the date of birth and a list of their skills::

    >>> tdum = ('1861-10-23', ['poetry', 'pretend-fight'])
    >>> tdee = ('1861-10-23', ['poetry', 'pretend-fight'])
    >>> tdum == tdee
    True
    >>> tdum is tdee
    False

It's clear that ``tdum`` and ``tdee`` refer objects that are equal, but not to the same object. Now, after the events witnessed by Alice, Tweedledum decided to become a rapper, and adopted the stage name T-Doom. This is how we can express this in Python::

    >>> t_doom = tdum
    >>> t_doom
    ('1861-10-23', ['poetry', 'pretend-fight'])
    >>> t_doom == tdum
    True
    >>> t_doom is tdum
    True

So, ``t_doom`` and ``tdum`` are equal, but Alice would rightly complain that its silly to say that, because ``t_doom`` and ``tdum`` refer to the same person: ``t_doom is tdum``. The names ``t_doom`` and ``tdum`` are aliases. In fact, I like that the official Python docs often refer to variables as "names". That helps freeing our mind from the idea that they are like boxes. Because anyone who thinks of variables as boxes can't make sense of what comes next.

After much practice, T-Doom is now skilled rapper. In code, this is what happened::

    >>> skills = t_doom[1]
    >>> skills.append('rap')
    >>> t_doom
    ('1861-10-23', ['poetry', 'pretend-fight', 'rap'])
    >>> tdum
    ('1861-10-23', ['poetry', 'pretend-fight', 'rap'])

So, T-Doom aquired the ``'rap'`` skill, and so did Tweedledum. Of course, they are one and the same. If ``t_doom`` was a box holding a date and a list, how can you explain that adding to that list also changes the data in the ``tdum`` box? But if you think of variables (or names) as labels, then it all makes perfect sense. The label analogy is much better because aliasing is explained simply as an object with two or more labels.

This exercise also shows that the value of a tuple may change. Now the brothers are no longer equal::

    >>> tdum == tdee
    False

What is immutable is the physical content of a tuple, which only holds object references. What changed was the value of the list referenced by ``tdum[1]``, but the refenced object id is still the same. This highlights the difference between the concepts of identity and value, described in Python Language Reference Data Model chapter:

    Every object has an identity, a type and a value. An object’s identity never changes once it has been created; you may think of it as the object’s address in memory. The ‘is‘ operator compares the identity of two objects; the id() function returns an integer representing its identity.

The other built-in immutable collection type in Python, ``frozenset``, does not suffer from the problem of being immutable yet potentially changing in value. That's because a ``frozenset`` (or a plain ``set``) may only hold references to hashable objects, and these by definition must be never change in value.

A common use of tuples is as ``dict`` keys, and those must be hashable -- just as set elements. So, are tuples hashable or not? The right answer is: some are hashable, some are not. Tuples are always immutable, but the value of a tuple holding a mutable object may change, and such a tuple is not hashable. To be used as a ``dict`` key or set element, the tuple must be made only of hashable objects. Our objects ``tdum`` and ``tdee`` are unhashable because each holds a list reference.    

Now let's focus on the assignment statements at the heart of this whole exercise.

Assignment in Python never copies values. It only copies references. So when I wrote ``skills = t_doom[1]`` I did not copy the list at ``t_doom[1]``, I only copied a reference to it, which I then used to change the list by doing ``skills.append('rap')``. A different assignment, ``skills_copy = t_doom[1][:]`` would first create a copy of the list at ``t_doom[1]`` by using the slice operator ``[:]``, and that copy would then be bound to the ``skills_copy`` name. Note that the assignment is not copying the list. The copy is made by the expression ``t_doom[1][:]``. The assingment merely binds a name to the newly created copy of the list. 

Prof. Stein spoke about assignment in a very deliberate way. For example, when talking about a seesaw object in a simulation, she would say: “Variable s is assigned to the seesaw”, but never “The seesaw is assigned to variable s”. With reference variables it makes much more sense to say that the variable is assigned to an object, and not the other way around. After all, the object is created before the assignment.

In an assignment such as ``y = x * 10``, the right-hand side is evaluated first. The evaluation creates a new object or retrieves an existing one. Only after the object is constructed or retrieved, the label is assigned to it.

Here is proof in code. First we create a ``Gizmo`` class, and an instance of it::

    >>> class Gizmo:
    ...    def __init__(self):
    ...         print('Gizmo id: %d' % id(self))
    ...
    >>> x = Gizmo()
    Gizmo id: 4301489152

Now let's try to create another instance and immediately try to perform an operation with it before binding a name to it::

    >>> y = Gizmo() * 10
    Gizmo id: 4301489432
    Traceback (most recent call last):
      ...
    TypeError: unsupported operand type(s) for *: 'Gizmo' and 'int'
    >>> 'y' in globals()
    False

This snippet shows that the new object was instantiated (its id was 4301489432) but before the ``y`` name could be created, a ``TypeError`` aborted the whole assignment. The ``'y' in globals()`` check proves is no ``y`` global name.

To wrap up assignment in Python: always read the right-hand side first. That’s where the object is created or retrieved. After that, the variable on the left is bound to the object, like a label stuck to it. Just forget about the boxes.
