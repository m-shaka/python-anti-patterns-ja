lambda式を変数に代入する
=============================================

``lambda`` 式が ``def`` に対して持つ唯一の利点は、 無名のまま大きな式に埋め込めるという点にある。 ``lambda`` に名前を割り当てようと思うなら、サクッと ``def`` として定義したほうがうまくいく。

PEP 8 Style Guideより:

Yes:

.. code:: python

    def f(x): return 2*x

No:

.. code:: python

    f = lambda x: 2*x

一つ目の形は、作られる関数オブジェクトの名前が総称的な（generic）
'<lambda>'ではなく、明確に'f'であることを意味する。これは一般にトレースバックや文字列表現を使いやすくする [1]_ 。
代入文を使うことでlambda式がdefに対して持つただ1つの利点（すなわち、大きな式の内部に埋め込めること）が失われてしまう。

Anti-pattern
------------

以下のコードは 入力の2倍値を返す ``lambda`` 関数を変数に割り当てている。これは機能上は ``def`` を作るのと違いがない。

.. code:: python

    f = lambda x: 2 * x

Best practice
-------------

名前付きの表現には ``def`` を使う
.............................

``lambda`` 式を名前付きの ``def`` に修正する

.. code:: python

    def f(x): return 2 * x

原文:https://docs.quantifiedcode.com/python-anti-patterns/correctness/assigning_a_lambda_to_a_variable.html

.. [1] 【訳注】わかりにくいので補足。関数オブジェクトは ``__name__``という特殊フィールドでその名前を取得できる。lambdaを変数に割り当てた場合、どんな変数名を用いようともこれが'<lambda>'になってしまうのである。これでは、例えば関数名による分岐などが不可能になる。
