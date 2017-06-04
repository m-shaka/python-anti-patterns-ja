``__exit__`` には必ず引数を3つ渡す： 型・値・トレースバック
============================================================
``contextmanager`` クラスは `Python Language Reference's context management protocol <https://docs.python.org/2/reference/datamodel.html#with-statement-context-managers>`_ によれば、 ``__enter__`` と ``__exit__`` メソッドを実装するようなクラスである。
コンテキスト管理プロトコル [1]_ を実装することで、 そのクラスのインスタンスで ``with`` 文を使えるようになる。
``with`` 文は、開始と終了の操作が、与えられたコードのまとまりの前後で必ず実行されることを担保するために使われる。
機能的には ``try...finally`` と等価だが、 ``with`` 文の方がより簡潔な点で異なる。

たとえば、次の ``with`` 文を使ったコード群は……

.. code:: python

    with EXPRESSION:
        BLOCK

……次の ``try`` 文と ``finally`` 文を使ったコード群と等価だ。

.. code:: python

    EXPRESSION.__enter__()
    try:
        BLOCK
    finally:
        EXPRESSION.__exit__(exception_type, exception_value, traceback)

``__exit__`` が正しく動作するためには、きっかり3つの引数を受け取らなければならない。
``exception_type``、 ``exception_value`` 、そして  ``traceback`` [2]_ だ。
メソッド定義における形式上の引数名がこれらの名前に一致している必要はないが、この順番を変えてはならない。
``with`` 文の後ろの入れ子になったコード群を実行する間にどんな例外が生じようとも、Pythonは例外に関する情報を ``__exit__`` メソッドに渡す。
したがって、 ``__exit__`` の定義をいじって、正常に（gracefully）各々の例外を処理するようにできる。 [3]_ 。


Anti-pattern
------------

以下の ``Rectangle`` クラス内で定義された ``__exit__`` メソッドは、Pythonのコンテキスト管理プロトコルに従っていない。メソッドは4つの引数を受け取ると想定されている。
``self`` 、例外の型、例外の値、そしてtracebackだ。
メソッドのシグネチャ [4]_ がPythonが予期したものでないため、 ``__exit__`` は決して呼ばれることはない。
``divide_by_zero`` メソッドが ``ZeroDivisionError`` の例外を吐くため、呼ばれるべきだったとしてもだ。


.. code:: python

    class Rectangle:
        def __init__(self, width, height):
            self.width = width
            self.height = height
        def __enter__(self):
            print("in __enter__")
            return self
        def __exit__(self): 
            # never called because
            # argument signature is wrong
            print("in __exit__")
        def divide_by_zero(self):
            # causes ZeroDivisionError exception
            return self.width / 0

    with Rectangle(3, 4) as r:
        r.divide_by_zero()
        # __exit__ should be called but isn't

    # Output:
    # "in __enter__"
    # Traceback (most recent call last):
    #   File "e0235.py", line 27, in <module>
    #     r.divide_by_zero()
    # TypeError: __exit__() takes exactly 1 argument (4 given)

Best practices
--------------

``__exit__`` を修正して4つ引数を受け取るようにすれば、 ``with`` 文に続くインデントされたコード群でエラーが生じたときに、 ``__exit__`` が適切に呼ばれることが保証される。
引数の名前は以下で与えられている名前に一致している必要がないことには注意されたい。しかし順番は以下を準拠しなければならない。

.. code:: python

    class Rectangle:
        def __init__(self, width, height):
            self.width = width
            self.height = height
        def __enter__(self):
            print("in __enter__")
            return self
        def __exit__(self, exception_type, exception_value, traceback):
            print("in __exit__")
        def divide_by_zero(self):
            # causes ZeroDivisionError exception
            return self.width / 0

    with Rectangle(3, 4) as r:
        # exception successfully pass to __exit__
        r.divide_by_zero()

    # Output:
    # "in __enter__"
    # "in __exit__"
    # Traceback (most recent call last):
    #   File "e0235.py", line 27, in <module>
    #     r.divide_by_zero()


.. [1] 【訳注】あまり自信はないが、おそらくこの'context'とは `Wikipedia<https://en.wikipedia.org/wiki/Context_(computing)>`_にページのあるこの用語を指していて、その定義によると「タスクによって使われるデータの最小セット」である。例えばプログラムでファイルを読み込む場合、まずファイルをopenして何らかの処理をした後、必ずそれをcloseする。 ``with`` はその処理の間でのみ使われるデータ（この場合ファイルの中身）を管理するための構文であると解釈できる。

.. [2] 【訳注】ご存知の通りPythonでエラーが生じると端末には ``Traceback (most recent call last)`` などという文言のあとにエラーの詳細が出力される。このTracebackは標準ライブラリで型として定義されており明示的に呼び出せる。ここでの ``traceback`` はそのインスタンスを指している。

.. [3] 【訳注】gracefulは直訳すれば「優雅な」といったところだが、計算機の文脈で'graceful shutdown'と言えばほぼ「正常終了」といった意味になるのでここの訳出もそれに従った。

.. [4] 【訳注】'signature'は、引数の情報や戻り値の型などメソッド各々に固有の情報、というぐらいに理解すればよいか。
