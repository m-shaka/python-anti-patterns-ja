クラスの外側からprotectedメンバーにアクセスする
========================================

クラスの外側からprotectedメンバー（``_``から始まるメンバー）にアクセスすることで問題が生じることは多い。クラスの実装者はこのメンバーを公開する意図がなかったからだ。

Anti-pattern
------------

.. code:: python

    class Rectangle(object):
        def __init__(self, width, height):
            self._width = width
            self._height = height

    r = Rectangle(5, 6)
    # direct access of protected member
    print("Width: {:d}".format(r._width))

Best practice
-------------
外側からprotectedメンバーにアクセスする必要があると絶対に思えるなら次のようにしよう。

 * クラス外からのメンバーへのアクセスが意図しない副作用を生まないことを確かめる。
 * そのメンバーがクラスの公開インターフェイスになるよう修正する。

原文
^^^
 https://docs.quantifiedcode.com/python-anti-patterns/correctness/accessing_a_protected_member_from_outside_the_class.html
