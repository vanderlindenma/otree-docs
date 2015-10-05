Treatments
==========

If you want to assign participants to different treatment groups, you
can put the code in the subsession's ``before_session_starts`` method
(for more info see :ref:`before_session_starts`).
For example, if you want some participants to have a blue background to
their screen and some to have a red background, you would first define
a ``color`` field on the ``Player`` model:

.. code-block:: python

    class Player(BasePlayer):
        # ...

        color = models.CharField()


Then you can assign to this field randomly:

.. code-block:: python

    class Subsession(BaseSubsession):

        def before_session_starts(self):
            # randomize to treatments
            for player in self.get_players():
                player.color = random.choice(['blue', 'red'])

(To actually set the screen color you would need to pass
``player.color`` to some CSS code in the template, but that part is
omitted here.)

You can also assign treatments at the group level (put the ``CharField``
in the ``Group`` class and change the above code to use
``get_groups()`` and ``group.color``).

If your game has multiple rounds, note that the above code gets executed
for each round. So if you want to ensure that participants are assigned
to the same treatment group each round, you should set the property at
the participant level, which persists across subsessions, and only set
it in the first round:

.. code-block:: python

    class Subsession(BaseSubsession):

        def before_session_starts(self):
            if self.round_number == 1:
                for p in self.get_players():
                    p.participant.vars['color'] = random.choice(['blue', 'red'])

Then elsewhere in your code, you can access the participant's color with
``self.player.participant.vars['color']``.

There is no direct equivalent for ``participant.vars`` for groups,
because groups can be re-shuffled across rounds.
You should instead store the variable on one of the participants in the group:

.. code-block:: python

    def before_session_starts(self):
        if self.round_number == 1:
            for g in self.get_groups():
                p1 = g.get_player_by_id(1)
                p1.participant.vars['color'] = random.choice(['blue', 'red'])

Then, when you need to access a group's color, you would look it up like this:

.. code-block:: python

    p1 = self.group.get_player_by_id(1)
    color = p1.participant.vars['color']

For more on vars, see :ref:`vars`.

The above code makes a random drawing independently for each player,
so you may end up with an imbalance between "blue" and "red".
To solve this, you can alternate treatments, using ``itertools.cycle``:

.. code-block:: python

    import itertools

    class Subsession(otree.models.BaseSubsession):

        def before_session_starts(self):
            treatments = itertools.cycle([True, False])
            for g in self.get_groups():
                g.treatment = treatments.next()



Choosing which treatment to play
--------------------------------

In the above example, players got randomized to treatments. This is
useful in a live experiment, but when you are testing your game, it is
often useful to choose explicitly which treatment to play. Let's say you
are developing the game from the above example and want to show your
colleagues both treatments (red and blue). You can create 2 session
configs in settings.py that have the same keys to session config dictionary,
except the ``treatment`` key:

.. code-block:: python

    SESSION_CONFIGS = [
        {
            'name':'my_game_blue',
            # other arguments...

            'treatment':'blue',

        },
        {
            'name':'my_game_red',
            # other arguments...
            'treatment':'red',
        },
    ]

Then in the ``before_session_starts`` method, you can check which of the
2 session configs it is:

.. code-block:: python

    def before_session_starts(self):
        for p in self.get_players():
            if 'treatment' in self.session.config:
                # demo mode
                p.color = self.session.config['treatment']
            else:
                # live experiment mode
                p.color = random.choice(['blue', 'red'])

Then, when someone visits your demo page, they will see the "red" and
"blue" treatment, and choose to play one or the other. If the demo
argument is not passed, the color is randomized.

Treatment variables not fitting into a model field
--------------------------------

Because treatment variables are stored as model, your treatment 
variable must fit into one of the field types available in Django.
If you want to use treatment variable which do not fit into a 
Django field type, you will have to convert your variable into a string
first, and then convert the string back to its intended format.

To do so, you can use the ``ast`` package. 
For instance, if your treatment variable consists in chosing between the
two lists ``[1,2,3]`` and ``[3,1,2]`` at the level of the group, you 
would first define a ``treatment`` field on the ``Group`` model:

.. code-block:: python

    class Group(BaseGroup):
        # ...

        treatment = models.CharField()

Then inside the ``before_subsession_starts`` method, you would turn the 
chosen list into a string and assign it to ``treatment`` (because 
``treatment`` is a ``CharField``, it can only be assigned strings)

.. code-block:: python

    def before_session_starts(self):
        for group in self.get_groups():
            # Turn the chosen list into a string 
            group.treatment = '%s' %(random.choice([1,2,3],[3,1,2])

Assuming ``[1,2,3]`` was picked, ``group.treatment`` is now a string of the 
form ``'[1,2,3]'``. Thus, you will need to define a method that will 
convert the string ``group.treatment`` back to a list whenever 
you need it. To do so, first import the package ``ast``:

.. code-block:: python

        # Add the following to the import statements before 
        # class Constants(BaseConstants):
        
        import ast

Then define the following method inside the ``Group`` model:

.. code-block:: python

       class Group(BaseGroup):
        # ...
        
        def convert_to_list(self):

            self.treatment = ast.literal_eval(self.treatment)

You can now use your treatment variable as a list by calling 
``convert_to_list``. For instance, if you want to use your list
in the ``Decide`` page, you could call ``convert_to_list`` inside
the ``vars_for_template`` method inside ``view.py``:

.. code-block:: python

       class Decide(Page):
        # ...
        def vars_for_template(self):
            #...
            self.group.convert_to_list()
            
This will make ``group.treatment`` available as a list in the 
corresponding template.
            

