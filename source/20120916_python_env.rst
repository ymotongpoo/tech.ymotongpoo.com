.. -*- coding: utf-8 -*-

=====================================================
 How to establish development environment for Python
=====================================================

:Event: PyCon JP 2012
:Version: 1.0.0
:Presented on: 2012-09-16 11:00-11:45 JST
:Author: @ymotongpoo
:URL: http://2012.pycon.jp/program/sessions.html#session-16-1100-room357-ja

Agenda
======

- Self introduction
- Overview
- Local development environment

  - Code editing
  - Managing Python binaries
  - Managing Python packages
  - Workspaces
  - Tests

- Integrated development environment

  - Contination Test
  - Documentation

Self introduction
=================

- id:ymotongpoo

  - YAMAGUCHI::weblog http://ymotongpoo.hatenablog.com (Japanese)

- Google Inc., YouTube Technical Account Manager
- Using Python for creating tools for daily basis tasks
- Dedicating to OSS documents translation projects, such as:

  - Jinja2
  - Tornado

- Published translation of "`Funcitional Programming for Java Developers <fpjd>`_"

  - `Java開発者のための関数プログラミング <fpjd_ja>`_

.. _fpjd: http://shop.oreilly.com/product/0636920021667.do
.. _fpjd_ja: http://www.oreilly.co.jp/books/9784873115405/

Overview
========

This document introduces how to establish the fundamental development environment for Python, and ramps up Python beginners' developments. And this documents suggests good references on creating basic Python development environment for programmers who have experienced development in other languages.

As this document only argues the points which solved by tools and libraries, it will not provides things related more to operations or conventions such as coding styles, review systems, best practices for developing Web applications with frameworks and so on.

Though at time of my presentation on PyCon JP 2021 (Sep. 16th), Python's latest version is 3.3.0 rc1, this presantaion is mainly based on Python 2.x, especially Python 2.7 if I mention ``python`` without version number.

Code editing
============

I think you can use the editor which you like, but I recommend to choose the one which offers nice auto indentation and so on so to follow PEP8 and Python's language specifications. For instance, Python forces you to indent to express code block, and imagine if you have to use "Notepad" for all those indentation, which is undesired burden.

- Emacs (Python mode)

  - python-mode.el (distributed by python.org)
  - python.el (Emacs)

- Vim (Python mode)
- Sublime Text 2 (extension can be implemented in Python)
- PyCharm (Python exclusive IDE)
- Eclipse (PyDev)

Managing Python binaries
========================

Next thing to think about is how to manage Python binaries. This should depends on Operation Systems, such as Windows, Linux, Unix/BSD, Mac OS X.
In this session "Python" means CPython and I do not mention IronPython, PyPy and Jython as they are used in very specific cases.

In the case of CPython, you can pick one of following 2 methods for Python installation:

- install pre-build binary
- build from source code

Each way has pros and cons, and which to choose is up to situations and your favor. In terms of easiness, pre-build binary is the one, and in terms of ability to configuration, building from source code is batter. When you use pre-build binary installer, you should care about whether you can specify installation target.

Windows
-------

On Windows, Microsoft Visual C++ is required to build from source code, which is kind of complex to use for the first time. Usually we can use MSI package for installation.

- MSI package

You can specify installation target in installation process of MSI. For multiple version installation, installation root should be the same like as:

.. code-block:: none

   D:\Python\
   ├── 2.6
   ├── 2.7
   ├── 3.1
   └── 3.2

Linux
-----

- apt-get, yum, YaST
- make

Usually Linux distribution has its package management tool, which can install pre-build Python binary, while you can still build Python from source code with quite easy command ``./configure && make && make install`` though you need to specify several options in order to configure installation target.


Mac OS X
--------

Mac OS X do not have OS standard package management tools but homebrew and MacPorts provides fancy features to manage packages. Especially MacPorts does pre-build binary installation from version 2.0 and this is almost the same function as Linux. 
In the case of dmg file, ``.mpkg`` file which included in dmg file installs Python binaries into ``/Library/Frameworks/Python.framework``.

- homebrew, MacPorts
- dmg
- make

Using ``make`` on Mac OS X requires Xcode, and sometimes it is hard for beginner. Personally I recommend MacPorts for Python installation.

pythonz (pythonbrew)
--------------------

pythonz is Python installation manager for exclusive use. Though it is called "installation manager", it doesn't download and install pre-build binary, but just automates:

1. downlaading and expanding tarball
2. running ``configure && make && make install``

pythonz is originally forked from pythonbrew, and pythonz has simpler features than pythonbrew. From my point of view, I don't recommend using pythonbrew related tools. The reason why I don't is:

- Installing Python from source code is not that difficult. (Just running ``make`` command)
- It addes unnecessary extra entry points
- We do not install Python binary into system frequently

Managing Python packages
========================

- setup.py (distutils, setuptools, distribute)
- easy_install (setuptools, distribute, distutils2)
- pip
- packaging

setup.py
--------

``setup.py`` is the important script which is necessary for distributing, building and installing Python modules into system. It contains ``setup`` function which handles all metadata describing the module. You can refer to official Python documantation for details of ``setup.py``, so I provide current status of ``setup.py``.

setup関数自体はPython標準ライブラリのdistutils内で定義されていますが、依存ライブラリの解消や、エントリポイントなどの重要な機能がないため、setuptoolsがdistutilsの拡張として開発され、さらにその拡張としてdistributeが開発されています。

  distutils -> setuptools -> distribute

歴史的な経緯はスライドを参照して頂ければと思いますが、とりあえずsetup.py使うときはdistributeを使いましょう。

easy_install
------------

easy_installコマンドでややこしいのは、これ自体はsetuptoolsモジュールを叩くためのスクリプトであって、setuptoolsモジュールが複数のパッケージで実装されているということです。歴史的な経緯はスライドにあるとおりですが、setuptoolsよりもdistributeの方が後発で、開発も活発であり、多機能です。そしてそのdistributeの機能を取り込んだPython3.3以降の標準機能をバックポートしたものがdistutils2です。

発表時現在で現状有姿でPython3に対応しているのはdistributeのみであり、distributeが現時点でデファクトスタンダートとなっています。distributeで使えるeasy_installのオプションは次の通り。


:--upgrade,-U: 強制アップグレード
:--always-unzip,-Z: zipは展開してインストール
:--multi-version,-m: 複数バージョンのインストールもしくはアンインストール

他のオプションは ``easy_install --help`` で確認してください。

pip
---

パッケージ管理ツールとして、easy_installにはなかったパッケージのアンインストール機能を始め、多くの機能があるツールです。

:install: パッケージをインストール
:uninstall: パッケージをアンインストール
:bundle: pybundleを作成する（複数のパッケージを含むアーカイブを作る）
:freeze: 現在インストールされているパッケージを標準出力に表示
:search: PyPIを検索
:zip: 個々のパッケージをzipする
:unzip: 個々のパッケージをunzipする

pipの場合、requirements.txtと呼ばれるファイルにsetup.pyのinstall_requestsと似た形式でパッケージを指定しておくと、次のようにまとめてパッケージのインストールができます::

  $ cat requirements.txt
  MyApp
  Framework==0.9.4
  Library>=0.2

  $ pip install -r requirements.txt

pipは便利なのですが、easy_installとの互換性がないので、使うのであればどちらか一方にしたほうがいいでしょう。またpipはバイナリインストール機能がないため、例えばIPythonのようなモジュールはインストールできないことに注意しましょう。


ワークスペースの管理
====================

Pythonでは、開発しようとする製品に応じて、利用するPythonやライブラリのバージョンが異なることが往々にしてあります。

OSのすぐ上にあるPython環境でこれらの切り分けを行うことは現状では難しいため、3rd partyツールを利用して各プロジェクト用のワークスペースを作成／管理します。

- virtualenv (+virtualenvwrapper)
- zc.buildout
- venv (pyvenv from Python3.3)

virtualenv (+virtualenvwrapper)
-------------------------------

virtualenvはIan Bicking作のPython仮想環境です。ここでいう仮想環境というのは、システムインストールされたPython本体から独立した、site-packagesを構築できるという意味です。

仮想環境の作成
~~~~~~~~~~~~~~

virtualenvパッケージをインストールしたら、 ``virtualenv`` コマンドで仮想環境を作成します。ここでは ``spam`` という環境を作成します::

  $ virtualenv spam

virtualenvではデフォルトではpipが標準のパッケージ管理ツールとして選択され、仮想環境にインストールされるわけですが、distributeのeasy_installを利用することもできます。その場合は次のように仮想環境を作成します::

  $ virtualenv --distribute spam

distributeをデフォルトにする場合は環境変数 ``VIRTUALENV_DISTRIBUTE`` を ``true`` にしておきましょう::

  $ export VIRTUALENV_DISTRIBUTE=true
  $ virtualenv egg

仮想環境の利用
~~~~~~~~~~~~~~

仮想環境 ``spam`` を利用するには作成した仮想環境の ``activate`` スクリプトを実行します::

  $ source spam/bin/activate
  (spam) $ easy_install -UZ bucho
  (spam) $ deactivate
  $

PS1に仮想環境名が表示されたら仮想環境がアクティブになっています。このときPATHの先頭に ``spam/bin`` が追加されているはずです。 ``deactivate`` を実行すると環境変数が元に戻ります。

virtualenvwrapper
~~~~~~~~~~~~~~~~~

virtualenvではワークスペースの管理や仮想環境の切り替えなどを手動で行う必要がありましたが、それらを使いやすくvirtualenvのラップしてくれたのがvirtualenvwrapperです。インストール方法等は公式ドキュメントやブログエントリ等に任せるとして、仮想環境の作成や切り替えなどが非常に簡単になります::

  $ mkvirtualenv spam
  (spam) $ mkvirtualenv egg
  (egg) $ workon
  spam
  egg
  (egg) $ workon spam
  (spam) $ deactivate
  $ rmvirtualenv egg
  spam
  $ workon spam
  (spam) $


buildout (zc.buildout)
----------------------

昨日のセッションで北崎さんが説明されていたので資料等はそちらもご参照ください。zc.buildout（以下、buildout）はPython製のビルドツールで、本来であればワークスペースの管理用のツールではありません。しかしながら、十分ワークスペース管理に便利に利用できるため、ここでご紹介します。なおbuildoutは2.0は現状buggyなので1.6を使うことをおすすめします。

buildoutを利用する方法は次の2通りあります。

1. packageとしてzc.buildoutをインストールする
2. bootstrap.py単体を取得する

buildout初期化
~~~~~~~~~~~~~~~

まずbuildoutで環境を作成する場合は、専用のディレクトリを作成します。

1.で取得した場合は次の通り::

  $ easy_install zc.buildout
  $ buildout init --distribute

2.で取得した場合は次の通り::

  $ curl "https://raw.github.com/buildout/buildout/master/bootstrap/bootstrap.py -o /tmp/bootstrap.py
  $ python /tmp/boostrap.py init --distribute


buildout.cfg設定
~~~~~~~~~~~~~~~~

この時点でディレクトリは次のようになっています::

  .
  ├── bin
  ├── buildout.cfg
  ├── develop-eggs
  ├── eggs
  └── parts

buildoutはデフォルトでターゲットディレクトリ内のbuildout.cfgというini形式ファイルを設定ファイルとして認識します。ここにビルドツールとして機能させるための設定を書くわけですが、今回は独立したsite-packagesを作るために利用します::

  [buildout]
  parts =
    dev

  [dev]
  recipe = zc.recipe.egg
  eggs =
    sphinx
    sphinxcontrib-blockdiag
    sphinxcontrib-networkdiag
    PIL
  interpreter = py


buildoutの実行
~~~~~~~~~~~~~~

buildout.cfgの作成が完了したらbuildoutで環境を構築します。先ほどbootstrapした際に作成されたbinディレクトリに必要なコマンドが入っていますので、そちらから実行します::

  $ bin/buildout
  ...
  zip_safe flag not set; analyzing archive contents...
  Got funcparserlib 0.3.5.
  

pyvenv (venv module)
--------------------

pyvenvはvirtualenvに刺激を受けてPEP 405として提案された標準コマンドです。
Python3.3より導入された機能ですので現時点ではまだ実用では使われないと思いますが、今後は上記のような3rd partyツールではなく標準で使われる可能性も高いので触れておきます。


仮想環境の作成
~~~~~~~~~~~~~~

仮想環境の作成は非常に簡単です::

  $ pyvenv /path/to/workspace

virtualenvによく似ていますね。

仮想環境の利用
~~~~~~~~~~~~~~

作成した仮想環境のディレクトリに行き ``bin/`` ディレクトリにある ``actibate`` スクリプトを実行するだけ::

  $ cd /path/to/workspace
  $ source bin/acivate
  (workspace) $ 

仮想環境内のパッケージの管理に関しては ``pip`` や ``easy_install`` をインストールする必要があります。


テストの管理
============

単バージョンテスト
------------------

単体バージョンテストはテストフレームワークを利用します。

- unittest
- doctest
- nose
- py.test

unittest, doctest
~~~~~~~~~~~~~~~~~

unittestとdoctestはPythonの標準ライブラリとして提供されるテストツールです。
noseやpy.testほどの機能はありませんが、簡単なテストを書くだけなら十分に利用できます。

unittestでは名前の通りユニットテストを行うのに必要な基本的なツールは揃っていて、
setup(), teardown()なども当然利用できます。

doctestは更に便利で、コード中にdocstringとして書いたものがそのままテストになります。
簡単なスクリプトなどでわざわざ別にテスト用ファイルを作成するのが面倒なときには、
ドキュメントついでにテストを書いておくと非常に便利です::

  def square(n):
      """ square given number n
  
      >>> square(2)
      4
      >>> square(-5)
      25
      >>> square('one')
      Traceback (most recent call last):
        ...
      TypeError: unsupported operand type(s) for ** or pow(): 'str' and 'int'
      """
      return pow(n, 2)

  if __name__ == "__main__":
      import doctest
      doctest.testmod()

nose vs py.test
~~~~~~~~~~~~~~~

noseとpy.testはどちらも似た思想で作られているので、どちらでもいいかなと思っていますが、
個人的にはpy.testを推奨します。その理由は次のとおり。

1. xUnit形式の出力がプラグインなしで可能（→Jenkinsとの連携が楽）
2. 後述のtoxが依存しているため余計にインストールする必要がない
3. テストデータとロジックを分けて書けるためすっきり書ける
4. setup()とteardown()含めたテストシナリオが書きやすい

あくまで個人的な印象です。noseのほうが先発だったこともあり、ユーザも多く多くの3rd partyプラグインや
ドキュメントが出ていて既存資産も多い方がたくさんいると思います。
一方で後発のpy.testは必要な機能をデフォルトで実装していたため、単体で非常に利用しやすい
テストランナーになっていると思います。


複数バージョンテスト
--------------

ライブラリの作成などに於いては、複数のバージョンに対応するために、複数のバージョンでのテストを行う必要があります。

- tox
- zc.buildout

tox
~~~

toxの設定ファイルである ``tox.ini`` は次のように書けます::

  [tox]
  envlist = py26, py27

  [testenv:py27]
  basepython = /opt/python/2.7/bin/python2.7

  [testenv]
  deps = 
    pytest
    requests

  commands =
    py.test \
      --junitxml=junit-{envname}.xml \
      test


zc.buildout
~~~~~~~~~~~

先ほどワークスペースの管理でも利用しましたが、テストランナーとしてもbuildoutが利用できます。
使うrecipeは次のどれか。

- zc.recipe.testrunner （通常のテスト）
- pbp.recipe.noserunner （noseを使う場合）
- z3c.recipe.scripts
- collective.recipe.template

継続テストの管理
================

継続テストでは、継続的インテグレーション（CI）ツールを利用します。

- Jenkins (+plugin)
- Travis CI
- Buildbot（紹介だけ）

Jenkins
-------

Jenkinsは言わずと知れたJava製の継続的インテグレーションシステムです。単純に動作させるだけであればインストールは非常に簡単です。コミュニティベースの開発も盛んで、数多くのプラグインが開発されており、それらもダッシュボードからインストールが可能になっています。

Pythonプロジェクト用にもいくつかプラグインが存在していますが、その中でもShining Pandaが非常に優秀です。


Travis CI
---------

Travis CIはGitHubでホストしているレポジトリを継続ビルドしてくれるサービスです。発表時現在はGitHubしかサポートしていないため、社内プロジェクト等だと利用は難しいかもしれません。

Pythonを含め14種類の言語が利用可能で、設定は ``.travis.yaml`` ファイルに書くだです。まずはTravis CIの機能としてpy.testを走らせる例::

  language: python
  python:
    - "2.6"
    - "2.7"
    - "3.1"
    - "3.2"
  install: 
    - pip install -r requirements.txt --use-mirrors
  script:
    - py.test test

またtoxを利用している場合には次のようにtoxを走らせる指定をするだけ::

  language: python
  install:
    - pip install tox
  script:
    - tox


ドキュメントの管理
==================

ドキュメントはPythonコードとの連携のしやすさや、既存のreStructuredText資産を活かす意味でSphinxを利用します。手元では上記で触れたローカル開発環境に入れたSphinxでビルドを行います。Sphinxのインストールに関してはSphinx-Users.jpのサイトが詳しいので割愛しますが、 ``sphinx`` パッケージをインストールするだけです。

統合環境ではCIツールにてビルドを行うか、別途ドキュメント用ビルドツールを利用します。

- Jenkins
- ReadTheDocs

Jenkins
-------

Jenkinsを利用する場合には、継続テストの場合と同様にドキュメントに更新があるごとにビルドを行うのが良いでしょう。Jenkinsの設定次第ですが、一番簡単な方法はJenkins用のワークスペースをそのまま公開することです。大人数で閲覧する可能性がある場合はJenkinsでドキュメントをビルドする際に、専用Webサーバのドキュメントルートにコピーするようなフローを用意してもいいでしょう。


ReadTheDocs
-----------

ReadTheDocsは2010年のDjango Dashで作成されたドキュメントホスティングシステムです。Sphinxプロジェクトを検知し、有名なVCSと連携してドキュメントがプッシュされると自動でビルドを行います。

現在、サービスとしての http://readthedocs.org/ と、パッケージとしてのReadTheDocsが存在します。オープンソースのものであれば前者を利用しても良いでしょう。

後者はまだ不完全ではありますが、自前でホスティングすることも可能です。
（実際に家でテストしてみる）


参照
====

参考資料
--------

Web
~~~

2012.05版 Python開発のお気に入り構成（ポロリもあるよ）
  http://ymotongpoo.hatenablog.com/entry/20120516/1337123564

継続開発のススメ
  https://gist.github.com/9ee65f0dfa9b7dd78fde

PEP 8 -- Style Guide for Python Code
  http://www.python.org/dev/peps/pep-0008/

pythonz(pythonbrew)に関する私見
  https://plus.google.com/114892104481751903211/posts/aPgWC3B7Wrg

Pythonモジュールの配布
  http://docs.python.jp/distutils/index.html

  原文 Distributing Python Module
    http://docs.python.org/distutils/index.html

2009/12/05 distutils, setuptools, distribute, pip, virtualenv, buildout
  http://www.freia.jp/taka/blog/691/index.html

PYTHON PACKAGING （PyFes 2012.03 発表資料)
  http://www.slideshare.net/shimizukawa/python-packaging-pyfes-201203

26.13. venv — Creation of virtual environments
  http://docs.python.org/dev/library/venv.html

  PEP 405 -- Python Virtual Environments
    http://www.python.org/dev/peps/pep-0405/

Python3.3のvenvを試す
  http://ymotongpoo.hatenablog.com/entry/2012/09/03/002604

AutoQA nose pytest comparison
  http://fedoraproject.org/wiki/User:Tflink/AutoQA_nose_pytest_comparison

データ駆動テストを nose と pytest でやってみた
  http://d.hatena.ne.jp/t2y-1979/20120209/1328740274

Jenkinsでtox経由のpytestの結果を受け取るように設定する
  http://ymotongpoo.hatenablog.com/entry/20120425/1335322398

Travis CIでtoxを実行してみる
  http://d.hatena.ne.jp/nullpobug/20120817/1345138660

Sphinx-Users.jp
  http://sphinx-users.jp/


書籍
~~~~

エキスパートPythonプログラミング
  http://www.amazon.co.jp/dp/4048686291

  原著
    http://www.amazon.co.jp/dp/184719494X

Pythonプロフェッショナルプログラミング
  http://www.amazon.co.jp/dp/4798032948


ツール
------

homebrew
  http://mxcl.github.com/homebrew/

MacPorts
  http://www.macports.org/

pythonz
  https://github.com/saghul/pythonz

pip
  http://pypi.python.org/pypi/pip/

  http://www.pip-installer.org/en/latest/index.html

distribute
  http://pypi.python.org/pypi/distribute/

  http://packages.python.org/distribute/index.html

virtualenv
  http://www.virtualenv.org/

  http://pypi.python.org/pypi/virtualenv/

virtualenvwrapper
  http://pypi.python.org/pypi/virtualenvwrapper

  http://virtualenvwrapper.readthedocs.org/en/latest/
  

zc.buildout
  http://www.buildout.org/

  http://pypi.python.org/pypi/zc.buildout/

  https://github.com/buildout/buildout/

  https://docs.google.com/spreadsheet/ccc?key=0AjmtypRXAd8zdEV0Y3dPbzN3YnZJUFAyUlhKeGNUNnc#gid=0

nose
  http://nose.readthedocs.org/

  http://pypi.python.org/pypi/nose/

py.test
  http://pytest.org/latest/

  http://pytest.readthedocs.org/en/latest/contents.html

  http://pypi.python.org/pypi/pytest/

unittest
  http://www.python.jp/doc/release/library/unittest.html

tox
  http://tox.readthedocs.org/

  http://pypi.python.org/pypi/tox

buildbot
  http://trac.buildbot.net/

  http://pypi.python.org/pypi/buildbot/

Attest
  http://packages.python.org/Attest/

  http://pypi.python.org/pypi/Attest/

Jenkins
  http://jenkins-ci.org/

  Shining Panda
    https://wiki.jenkins-ci.org/display/JENKINS/ShiningPanda+Plugin

Travis CI
  http://travis-ci.org/

  http://about.travis-ci.org/docs/

Sphinx
  http://pypi.python.org/pypi/Sphinx

  http://sphinx.pocoo.org/
