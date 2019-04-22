Getting Started with Ansible
============================

.. contents::


Installing Ansible
==================

How to install Ansible on your machine

Requirements
------------

- ``Python 2.6`` or *higher*, ``Python 3.x`` is not currently supported.
- If using Windows, ``VirtualBox`` with Ubuntu, CentOS or Mininet

Install using Pip
-----------------

This is probably the easiest way if you have pip installed on your machine.

**Note:** using a virtualenv is ALWAYS recommended.

If you need to install pip:

.. code-block:: bash

    curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
    python get-pip.py

Once pip is installed:

.. code-block:: bash

    #sudo if not in Virtualenv
    sudo pip install ansible

Installing on Mac w/ Homebrew
-----------------------------

.. code-block:: bash

    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew update
    brew install ansible

Installing on Linux w/ repository
---------------------------------

.. code-block:: bash

    #Ubuntu
    sudo apt-get install software-properties-common
    sudo apt-add-repository ppa:ansible/ansible
    sudo apt-get update
    sudo apt-get install ansible

    #CentOS
    sudo yum install ansible