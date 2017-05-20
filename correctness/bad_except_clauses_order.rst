except句の順番を間違える
=====================

例外が起こったとき、Pythonは当の例外の型にマッチする最初の例外句を探す。厳密にマッチする必要はない。例外句が起こった例外の基底クラスに対応しているなら、Pythonはその例外句がマッチするとみなす。たとえば、 ``ZeroDivisionError`` が起きたとき最初の例外句が ``Exception`` なら、 ``Exeception`` 句が実行される。 ``ZeroDivisionError`` は ``Exception`` のサブクラスだからだ。したがって、サブクラスの中でも具体性の高い（specific）例外句は、常にそれらの基底クラスの例外句より前に置かれるべきだ。例外処理ができるだけ具体的で有用なことを担保したいのである。

Anti-pattern
------------

以下のコードは ``ZeroDivisionError`` を吐く割り算を実行する。この型の例外句はコードに含まれており、問題の原因をぴたりと指し示しているので実に有用だ。だが、 その前に ``Exception`` が置かれているため ``ZeroDivisionError`` には到達しない。例外に出くわしたときPythonは上から下へ（linearly）各々の例外句を試していき、当の例外にマッチする最初のものを実行する。マッチは完全一致でなくてもよい。生じた例外が並べられた例外句の1つのサブクラスならば、Pythonはそれを実行し後ろの句はスキップする。こうして、できる限り正確に例外を同定して処理しようという例外句の目論見は打ち砕かれる。

.. code:: python

    try:
        5 / 0
    except Exception as e:
        print("Exception")
    # 到達しないコード！
    except ZeroDivisionError as e:
        print("ZeroDivisionError")

Best practice
-------------
サブクラスの例外句を親クラスのそれの前に移す。
............................................................

以下の修正後のコードは ``ZeroDivisionError`` を ``Exception`` の前に置いている。こうすれば例外が起こったとき、より具体的なため適切である ``ZeroDivisionError`` の例外句が実行されるようになるのだ。


.. code:: python

    try:
        5 / 0
    except ZeroDivisionError as e:
        print("ZeroDivisionError")
    except Exception as e:
        print("Exception")

原文: https://docs.quantifiedcode.com/python-anti-patterns/correctness/bad_except_clauses_order.html
