``super()`` の第一引数を間違える
=============================

``super()`` によって、親クラスを名指ししなくてもそのメソッドやメンバーを呼び出せるようになる。継承元が1つの場合、 ``super()`` の第一引数にはそれを呼んでいる現在の子クラスの名前が、そして第二引数には ``self`` （つまり、呼び出しもとの子オブジェクトへの参照）が入るべきだ。

.. note:: このアンチパターンはPython2系にのみ適用される。3系での ``super()`` の正しい呼び出し方についてはページ最下部の"Super in Python 3"を参照されたい。

Anti-pattern
------------
以下のように ``super()`` を呼び出そうとすると、Pythonは ``TypeError`` を起こす。第一引数には ``super()`` を呼び出した子クラスの名前が期待されている。コードの作者は間違えて ``self`` を与えてしまっている。


.. code:: python

    class Rectangle(object):
        def __init__(self, width, height):
            self.width = width
            self.height = height
            self.area = width * height

    class Square(Rectangle):
        def __init__(self, length):
            # bad first argument to super()
            super(self, Square).__init__(length, length)

    s = Square(5)
    print(s.area)  # does not execute


Best practice
-------------

``super()`` の第一引数には子クラスの名前を入れる
...........................................................

以下の修正されたコードでは、作者は ``super()`` を呼んでいる子クラスの名前（ここでは ``Square`` ）がメソッドの第一引数になるように ``super()`` の呼び出しを修正している。

.. code:: python

    class Rectangle(object):
        def __init__(self, width, height):
            self.width = width
            self.height = height
            self.area = width * height

    class Square(Rectangle):
        def __init__(self, length):
            # super() executes fine now
            super(Square, self).__init__(length, length)

    s = Square(5)
    print(s.area)  # 25


Super in Python 3
-----------------

Python 3では新しくよりシンプルな ``super()`` が加えられており、これは引数を必要としない。Python 3での正しい ``super()`` の呼び出し方は次のとおりだ。

.. code:: python
    
    class Rectangle(object):
        def __init__(self, width, height):
            self.width = width
            self.height = height
            self.area = width * height

    class Square(Rectangle):
        def __init__(self, length):
            # This is equivalent to super(Square, self).__init__(length, length)
            super().__init__(length, length)

    s = Square(5)
    print(s.area)  # 25 

原文: https://docs.quantifiedcode.com/python-anti-patterns/correctness/bad_first_argument_given_to_super.html
