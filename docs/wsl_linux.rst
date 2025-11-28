.. _chapter-wsllinux:

==============
Running on WSL
==============

.. _section-wsllinux-installation:

Platform Dependencies
=====================

.. _section-wsllinux-platformdependencies:

* Ubuntu : Tested for ``24.04``. Please follow `Install Ubuntu on WSL2 - Ubuntu on WSL documentation <https://documentation.ubuntu.com/wsl/latest/howto/install-ubuntu-wsl2/>`_.

| 依存物のインストールには ``wsl`` を用います。
| Install the dependencies via ``wsl``.

| 管理者権限でPowerShellターミナルを開いて、以下を実行します:
| Open PowerShell terminal as administrator and run:

.. code-block:: powershell

    # Ubuntu 24.04 on WSL
    > wsl --update
    > wsl --install Ubuntu-24.04

.. _section-wsllinux-dependencies:

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

.. _section-wsllinux-prerequisites:

Prerequisites
=============

Installing for Linux on WSL
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Tested for **Windows 11 Pro 24H2** and **Ubuntu 24.04 on WSL2**.

| Ubuntu-24.04 ターミナルを開き、以下を実行します:
| Open Ubuntu-24.04 terminal and run:

| 依存物のインストールには ``apt`` を用います。
| Install the dependencies via ``apt``.

.. code-block:: bash

    $ sudo apt update -y
    $ sudo apt upgrade -y --no-install-recommends
    # basic dependencies
    $ sudo apt install -y build-essential git curl

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

Jump to :ref:`Build Instructions <section-wsllinux-build>` for the next step.

.. _section-wsllinux-build:

Build Instructions
==================

| Ubuntu-24.04 ターミナルを開いて、以下を実行します:
| Open Ubuntu-24.04 terminal and run:

.. code-block:: bash

    # get the soshinki_fw source
    $ mkdir -p ~/lib
    $ cd ~/lib
    $ git clone https://<your_personal_access_token>@github.com/questar-ac/soshinki_fw.git
    # build soshinki_fw
    $ cd soshinki_fw
    $ cargo build

.. _section-wsllinux-run:

Run Instructions
================

| Ubuntu-24.04 ターミナルを開いて、以下を実行します:
| Open Ubuntu-24.04 terminal and run:

.. code-block:: bash

    # move to the directory of soshinki_fw source
    $ cd ~/lib/soshinki_fw
    # copy AWS IoT client certificate and key to the soshinki_fw's specific place
    $ mkdir -p ~/.aws/iot
    $ cp -r terminal_setup/.aws/iot ~/.aws

    # always allow direnv to load .envrc in the soshinki_fw directory
    $ mkdir -p ~/.config/direnv
    $ echo -e '[whitelist]\nprefix = [ "'$PWD'" ]' > ~/.config/direnv/direnv.toml
    $ cd ..
    $ cd soshinki_fw
    direnv: loading ~/lib/soshinki_fw/
    direnv: export +SERIAL_PORT_NAMES +SERIAL_PORT_SPEEDS

    # run the soshinki_fw program
    $ cargo run


.. _subsection-wsllinux-build-vscode:

Build Instructions for using Visual Studio Code
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. | Windows側でVisual Studio Codeを開き、以下の操作を行います:
   | Open Visual Studio Code in Windows side and run:

- | 拡張機能 ``WSL`` をインストールします。
  | Install ``WSL`` extension.


.. image:: ./img/wsl_vscode_extensions_install_1.png
    :width: 800px
    :align: center


- | 拡張機能 ``rust-analyzer`` と ``CodeLLDB`` をインストールします。
  | Install ``rust-analyzer`` and ``CodeLLDB`` extensions.


.. image:: ./img/wsl_vscode_extensions_install_2.png
    :width: 800px
    :align: center


2. | Ubuntu-24.04 ターミナルを開いて、以下を実行します:
   | Open Ubuntu-24.04 terminal and run:

.. code-block:: bash

    # get the soshinki_fw source
    $ mkdir -p ~/lib
    $ cd ~/lib
    $ git clone https://<your_personal_access_token>@github.com/questar-ac/soshinki_fw.git
    $ cd soshinki_fw
    # copy AWS IoT client certificate and key to the soshinki_fw's specific place
    $ mkdir -p ~/.aws/iot
    $ cp -r terminal_setup/.aws/iot ~/.aws

    # create an alias for Visual Studio Code in Windows side
    $ echo 'alias code='\''"/mnt/c/Program Files/Microsoft VS Code/bin/code"'\''' >> ~/.bashrc
    $ source ~/.bashrc
    # open Visual Studio Code
    $ code .

3. | 開いたVisual Studio Code内で以下の操作を行います:
   | In Visual Studio Code opened:

- | 拡張機能 ``rust-analyzer`` と ``CodeLLDB`` をUbuntu 24.04 on WSLへインストールします。
  | Install ``rust-analyzer`` and ``CodeLLDB`` extensions in Ubuntu 24.04 on WSL.


.. image:: ./img/wsl_vscode_extensions_install_3.png
    :width: 800px
    :align: center


- | メニュー ``[ターミナル]`` > ``[ビルド タスクの実行...]`` を実行し、コマンドパレットに表示されたメニューから ``[rust: cargo build]`` を選択します。
  | Execute ``[Terminal]`` > ``[Run Build Task...]`` menu, then select ``[rust: cargo build]`` from the menus displayed in Command Palette.


.. image:: ./img/wsl_vscode_rust_build_menu.png
    :width: 800px
    :align: center

.. _subsection-wsllinux-run-vscode:

Run Instructions for using Visual Studio Code
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Tools required
--------------

* `usbipd-win <https://github.com/dorssel/usbipd-win>`_ : Windows software for sharing locally connected USB devices to other machines, including Hyper-V guests and WSL 2.

| ツールのインストールには ``winget`` を用います。
| Install the tools via ``winget``.

| 管理者権限でPowerShellターミナルを開いて、以下を実行します。
| Open PowerShell terminal as administrator and run:

.. code-block:: powershell

    # usbipd-win
    > winget install --interactive --exact dorssel.usbipd-win


Running for using Visual Studio Code
------------------------------------

1. | 管理者権限でPowerShellターミナルを開いて、以下を実行します:
   | Open PowerShell terminal as administrator and run:

.. code-block:: powershell

    # check if the USB-serial adapter is available on Windows side
    > usbpid list
    Connected:
    BUSID  VID:PID    DEVICE                                      STATE
    2-7    3554:fa09  USB Input Device                            Not shared
    2-10   8087:0033  Intel(R) Wireless Bluetooth(R)              Not shared
    4-2    0403:6001  USB Serial Converter                        Not shared
    
    # attach the USB-serial adapter to Ubuntu-24.04 on WSL
    > usbipd bind --busid 4-2
    > usbipd attach --wsl --busid 4-2

    # With "--busid <busid>", specify the ID for the USB-serial adapter connected to target measure-device

2. | Ubutnu-24.04 ターミナルを開いて、以下を実行します:
   | Open Ubuntu 24.04 terminal and run:

.. code-block:: bash

    # check if the USB-serial adapter is accessible on Ubuntu-24.04 side
    $ lsusb
    Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
    Bus 001 Device 002: ID 0403:6001 Future Technology Devices International, Ltd FT232 Serial (UART) IC
    Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
    $ ls /dev/ttyUSB*
    /dev/ttyUSB0

    # move to the directory of soshinki_fw source
    $ cd ~/lib/soshinki_fw
    # open Visual Studio Code
    $ code .

3. | 開いたVisual Studio Code内で以下の操作を行います:
   | In Visual Studio Code opened:

- | 拡張機能 ``direnv`` をインストールします。
  | Install ``direnv`` extension.


.. image:: ./img/wsl_vscode_extensions_install_4.png
    :width: 800px
    :align: center


.. warning::

    | 同名の拡張機能が他にもあります。アイコンと作者が上のようになっている ``direnv`` を見つけてください。
    | There are other extensions with the same name. Please find the ``direnv`` with the above icon and author.

4. | Visual Studio Code内で以下の操作を行います:
   | In Visual Studio Code:

.. raw:: html

    <style> .green {color:green} </style>

.. role:: green

- | ``[実行とデバッグ]`` パネルを開いて、 ``[実行とデバッグ]`` メニューから ``[Debug executable 'soshinki_fw' | Ubuntu]`` を選択します。
  | Open ``[Run and Debug]`` panel, and select ``[Debug executable 'soshinki_fw' | Ubuntu]`` from the ``[RUN AND DEBUG]`` menus.


.. image:: ./img/wsl_vscode_rundebug_menu_select.png
    :width: 800px
    :align: center



- | ``[実行とデバッグ]`` バネル内の [:green:`▷`] ボタンを押します (または、``[実行]`` > ``[デバッグの開始]`` メニューを実行します)。
  | Push [:green:`▷`] button in ``[Run and Debug]`` panel (or execute ``[Run]`` > ``[Start Debugging]`` menu).

- | 下のようなポップアップダイアログが表示されたら、 ``[Allow]`` ボタンを押してください。
  | Push ``[Allow]`` button if the pop-up dialog below is displayed.


.. image:: ./img/wsl_vscode_direnv_envrc_changed.png
    :width: 800px
    :align: center


- | プログラムがまだ実行状態でなければ、再度 [:green:`▷`] ボタンを押してください。
  | Push [:green:`▷`] button again, if the program is not yet running.

5. | 管理者権限でPowerShellターミナルを開いて、以下を実行します:
   | Open PowerShell terminal as administrator and run:

- | プログラムの停止後Ubuntu-24.04側でUSB-シリアル・アダプタをもう使わないなら、以下を実行します:
  | If you do not use the USB-serial adapter anymore on Ubuntu-24.04 side after stopping the program, run:

.. code-block:: powershell

    # detach the USB-serial adapter from Ubuntu-24.04 on WSL
    > usbipd detach --busid 4-2
    > usbipd unbind --busid 4-2
