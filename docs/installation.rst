.. _chapter-installation:

============
Installation
============

| USB-シリアル・アダプタの設定については :ref:`Setup USB-serial adapters <chapter-usbserial>` を参照してください。
| About setup of USB-serial adapters, see :ref:`Setup USB-serial adapters <chapter-usbserial>`

| soshinki_fwをWindows上で実行したい場合は :ref:`Running on WSL <chapter-windows>` を参照してください。
| If you want to run soshinki_fw on Windows, see :ref:`Running on WSL <chapter-windows>`

.. _section-get-source:

Source code
===========

| ソースコードはこちらの `GitHub repository <https://github.com/questar-ac/soshinki_fw>`_ で閲覧できます。
| The source code can be viewed from this `GitHub repository <https://github.com/questar-ac/soshinki_fw>`_.

| リポジトリのクローン:
| Cloning the repository:

.. code-block:: bash

    git clone https://github.com/questar-ac/soshinki_fw.git

| これはプラベートリポジトリなので、あなた自身のGitHubアカウントとコラボレータとしてのアクセス権限が必要です。また、``git`` コマンドによってプライベートリポジトリへアクセスするには、あなたのアカウント上でパーソナルアクセストークンを作成する必要もあります。パーソナルアクセストークンの作成方法についてはこちらの  `Github Docs <https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-personal-access-token-classic>`_ を参照してください。
| This is a private repository, so you will need your own GitHub account with access permissions as a collaborator. You also need to create a personal access token on your account to access to a private repository by ``git`` command. See this `Github Docs <https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-personal-access-token-classic>`_ on creating a personal access token.

| プライベートリポジトリをクローンするには、以下のコマンドを使います:
| To clone the private repository, you should use this command:

.. code-block:: bash

    git clone https://<your_personal_access_token>@github.com/questar-ac/soshinki_fw.git

.. _section-dependencies:

Dependencies
============

| soshinki_fwはRustコンパイラを必要とします。
| soshinki_fw requires a Rust compiler.

Requirements for soshinki_fw
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* `Redis <https://github.com/redis/redis>`_ : Redis is the preferred, fastest, and most feature-rich cache, data structure server, and document and vector query engine.

Tools required
^^^^^^^^^^^^^^

* `direnv <https://direnv.net/>`_ : direnv is an extension for your shell. It augments existing shells with a new feature that can load and unload environment variables depending on the current directory.

.. _section-prerequisites:

Prerequisites
=============

.. _section-prerequisites-linux:

Installing for Linux
^^^^^^^^^^^^^^^^^^^^

Tested for **Ubuntu 24.04**.

| 依存物のインストールには ``apt`` を用います。
| Install the dependencies via ``apt``.

.. code-block:: bash

    $ sudo apt update -y
    $ sudo apt upgrade -y --no-install-recommends
    # basic dependencies
    $ sudo apt install -y build-essential git curl

    # disable brltty.service
    $ sudo systemctl mask brltty-udev.service
    $ sudo systemctl stop brltty-udev.service

    # Rust compiler
    $ curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
    $ cat ~/.bashrc
    ....    ....
    . "$HOME/.cargo/env"
    $ source ~/.bashrc
    $ rustc --version
    rustc 1.87.0 (17067e9ac 2025-05-09)
    $ cargo --version
    cargo 1.87.0 (99624be96 2025-05-09)

    # Redis
    $ sudo apt install -y redis-server
    $ sudo sh -c 'echo "supervised systemd" >> /etc/redis/redis.conf'
    $ cd /tmp
    $ git clone https://<your_personal_access_token>@github.com/questar-ac/soshinki_fw.git
    $ sudo sh -c 'cat /tmp/soshinki_fw/terminal_setup/soshinki_redis.conf >> /etc/redis/redis.conf'
    $ sudo systemctl restart redis

    # direnv
    $ sudo apt install -y direnv
    $ echo 'eval "$(direnv hook bash)"' >> ~/.bashrc
    $ source ~/.bashrc

.. warning::

    | "``... >> ...``" を "``... > ...``" とミスタイプしないように注意してください。このミスタイプをすると、変更対象ファイルの既存の内容が削除されてしまいます。
    | Be careful not to make mis-type "``... >> ...``" as "``... > ...``".  If you do, the existing contents of the target files will be deleted.

Jump to :ref:`Build Instructions <section-build>` for the next step.

.. _section-prerequisites-macos:

Installing for macOS
^^^^^^^^^^^^^^^^^^^^

Tested for **macOS Ventura**.

| 依存物のインストールには ``brew`` を用います。
| Install the dependencies via ``brew``.

.. code-block:: bash

    % brew update
    # basic dependencies
    % brew install git

    # Rust compiler
    % brew install rustup-init
    % rustup-init
    % cat ~/.bashrc
    ....    ....
    . "$HOME/.cargo/env"
    % source ~/.bashrc
    % rustc --version
    rustc 1.87.0 (17067e9ac 2025-05-09)
    % cargo --version
    cargo 1.87.0 (99624be96 2025-05-09)

    # Redis
    % brew install redis
    % cd /tmp
    % git clone https://<your_personal_access_token>@github.com/questar-ac/soshinki_fw.git
    % cat /tmp/soshinki_fw/terminal_setup/soshinki_redis.conf >> "$(brew --prefix)"/etc/redis.conf
    % sudo brew services start redis

    # direnv
    % brew install direnv
    % echo 'eval "$(direnv hook bash)"' >> ~/.bashrc
    % source ~/.bashrc

Jump to :ref:`Build Instructions <section-build>` for the next step.

.. _section-build:

Build Instructions
==================

.. _section-build-unix:

Building for Linux or macOS
^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    # get the soshinki_fw source
    $ mkdir -p ~/lib
    $ cd ~/lib
    $ git clone https://<your_personal_access_token>@github.com/questar-ac/soshinki_fw.git
    # build soshinki_fw
    $ cd soshinki_fw
    $ cargo build

.. _section-run:

Run Instructions
================

.. _section-run-unix:

Running for Linux
^^^^^^^^^^^^^^^^^

.. code-block:: bash

    # move to the directory of soshinki_fw source
    $ cd ~/lib/soshinki_fw
    # copy AWS IoT client certificate and key to the soshinki_fw's specific place
    $ mkdir -p ~/.aws/iot
    $ cp -r terminal_setup/.aws/iot ~/.aws

    # check if the USB-serial adapter is available on the system
    $ lsusb
    Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
    Bus 001 Device 002: ID 0403:6001 Future Technology Devices International, Ltd FT232 Serial (UART) IC
    Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
    $ ls -l /dev/ttyUSB*
    crw-rw---- 1 root dialout 188, 0 Jun 20 09:03 /dev/ttyUSB0D
    # add your user-id to dialout group
    $ sudo usermod -a -G dialout $USER

    # Logout here

    # Login again

    # always allow direnv to load .envrc in the soshinki_fw directory
    $ mkdir -p ~/.config/direnv
    $ echo -e '[whitelist]\nprefix = [ "'$PWD'" ]' > ~/.config/direnv/direnv.toml
    $ cd ..
    $ cd soshinki_fw
    direnv: loading ~/lib/soshinki_fw/
    direnv: export +SERIAL_PORT_NAMES +SERIAL_PORT_SPEEDS
    # run the soshinki_fw program
    $ cargo run

.. _section-run-macos:

Running for macOS
^^^^^^^^^^^^^^^^^

.. code-block:: bash


    # move to the directory of soshinki_fw source
    % cd ~/lib/soshinki_fw
    # copy AWS IoT client certificate and key to the soshinki_fw's specific place
    % mkdir -p ~/.aws/iot
    % cp -r terminal_setup/.aws/iot ~/.aws

    # check if the USB-serial adapter is available on the system
    % ls /dev/tty.*
    /dev/tty.usbserial-FTE3YEM3

    # always allow direnv to load .envrc in the soshinki_fw directory
    % mkdir -p ~/.config/direnv
    % echo -e '[whitelist]\nprefix = [ "'$PWD'" ]' > ~/.config/direnv/direnv.toml
    % cd ..
    % cd soshinki_fw
    direnv: loading ~/lib/soshinki_fw/
    direnv: export +SERIAL_PORT_NAMES +SERIAL_PORT_SPEEDS
    # run the soshinki_fw program
    % cargo run

.. _subsection-build-unix-vscode:

Build Instructions for using Visual Studio Code
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Building for using Visual Studio Code
-------------------------------------

1. | ターミナルを開いて、以下を実行します:
   | Open Terminal and run:

.. code-block:: bash

    # get the soshinki_fw source
    $ mkdir -p ~/lib
    $ cd ~/lib
    $ git clone https://<your_personal_access_token>@github.com/questar-ac/soshinki_fw.git
    $ cd soshinki_fw

    # copy AWS IoT client certificate and key to the soshinki_fw's specific place
    $ mkdir -p ~/.aws/iot
    $ cp -r terminal_setup/.aws/iot ~/.aws

    # open Visual Studio Code
    $ code .

2. | Visual Studio Code内で以下の操作を行います:
   | In Visual Studio Code:

- | 拡張機能 ``rust-analyzer`` と ``CodeLLDB`` をインストールします。
  | Install ``rust-analyzer`` and ``CodeLLDB`` extensions.


.. image:: ./img/ubuntu_vscode_extensions_install_1.png
    :width: 800px
    :align: center


-  | メニュー ``[ターミナル]`` > ``[ビルド タスクの実行...]`` を実行し、コマンドパレットに表示されたメニューから ``[rust: cargo build]`` を選択します。
   | Execute ``[Terminal]`` > ``[Run Build Task...]`` menu, then select ``[rust: cargo build]`` from the menus displayed in Command Palette.


.. image:: ./img/ubuntu_vscode_rust_build_menu.png
    :width: 800px
    :align: center

.. _subsection-run-unix-vscode:

Run Instructions for using Visual Studio Code
---------------------------------------------

1. | ターミナルを開いて、以下を実行します:
   | Open Terminal and run:

.. code-block:: bash

    # move to the directory of soshinki_fw source
    $ cd ~/lib/soshinki_fw
    # copy AWS IoT client certificate and key to the soshinki_fw's specific place
    $ mkdir -p ~/.aws/iot
    $ cp -r terminal_setup/.aws/iot ~/.aws

    # open Visual Studio Code
    $ code .

2. | 開いたVisual Studio Code内で以下の操作を行います:
   | In Visual Studio Code opened:

- | 拡張機能 ``direnv`` をインストールします。
  | Install ``direnv`` extension.


.. image:: ./img/ubuntu_vscode_extensions_install_2.png
    :width: 800px
    :align: center

.. warning::

    | 同名の拡張機能が他にもあります。アイコンと作者が上のようになっている ``direnv`` を見つけてください。
    | There are other extensions with the same name. Please find the ``direnv`` with the above icon and author.

3. | Visual Studio Code内で以下の操作を行います:
   | In Visual Studio Code:

.. raw:: html

    <style> .green {color:green} </style>

.. role:: green

- | ``[実行とデバック]`` パネルを開いて、 ``[実行とデバック]`` メニューから ``[Debug executable 'soshinki_fw' | Ubuntu]`` を選択します。
  | Open ``[Run and Debug]`` panel, and select ``[Debug executable 'soshinki_fw' | Ubuntu]`` from the ``[RUN AND DEBUG]`` menus.


.. image:: ./img/ubuntu_vscode_rundebug_menu_select.png
    :width: 800px
    :align: center


.. NOTE::

    | macOS上でVisual Studio Codeを使っている場合は、 ``[実行とデバック]`` メニューから ``[Debug executable 'soshinki_fw' | macOS]`` を選択してください。
    | Please select ``[Debug executable 'soshinki_fw' | macOS]`` from the ``[RUN AND DEBUG]`` menus if you are using Visual Studio Code on macOS.


- | ``[実行とデバッグ]`` バネル内の [:green:`▷`] ボタンを押します (または、``[実行]`` > ``[デバッグの開始]`` メニューを実行します)。
  | Push [:green:`▷`] button in ``[Run and Debug]`` panel (or execute ``[Run]`` > ``[Start Debugging]`` menu).

- | 下のようなポップアップダイアログが表示されたら、 ``[Allow]`` ボタンを押してください。
  | Push ``[Allow]`` button if the pop-up dialog below is displayed.


.. image:: ./img/ubuntu_vscode_direnv_envrc_changed.png
    :width: 800px
    :align: center


- | プログラムがまだ実行状態でなければ、再度 [:green:`▷`] ボタンを押してください。
  | Push [:green:`▷`] button again, if the program is not yet running.
