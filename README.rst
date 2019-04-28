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

Securing your Inventory with Ansible-Vault
==========================================

Using Ansible-Vault with a Single Password
------------------------------------------

Let's suppose we have an ``inventory.yml`` file that looks like this::

    webservers:
        hosts: 
            tomcat123:
                ansible_host: 10.10.10.10
                ansible_user: root
                ansible_password: password123

We may not want people in our organization or on our team to be able to view the passwords or addresses to our servers. The good news is we can easily secure this file.

We simply need to run something like this (**note:** you need mvim and sshpass for this to work)::

    ansible-vault create secret.yml
    New Vault Password:
    Confirm New Vault Password:

This will open up a vim session where we can paste the contents of our existing ``inventory.yml`` file. If you've never used vim before, just simply hit ``i``, Ctrl-v to paste, hit ``esc`` and the type ``:wq`` to write and quit the file.

We can now verify the file has been encrypted by concatenating it to the screen::

    ▶ cat secret.yml
    ;1.1;AES256
    66343538356331646661373363396165333562366538336135643365613464316533373364383538
    6530393634363037373736306361353131386465373665380a366631386534613836333365613939
    etc
    etc

We can now utilize our encrypted file by running it with our playbook::

    ansible-playbook playbook.yml -i secret.yml --ask-vault-pass


Using Ansible-Vault with Multiple Passwords
-------------------------------------------

Using a single password is fun and easy, but what if we have multiple files with different passwords?

We can tell Ansible to prompt us for each password individually, for instance::

    ansible-playbook --vault-id playbook@prompt playbook.yml --vault-id secret@prompt secret.yml
    Vault password (playbook):
    Vault password (secret):

If you're working with larger systems you can use a password file, just switch out the ``@prompt`` for a filename, and Ansible will read the password from a file.

The ``--vault-id`` can be used in lieu of the ``--vault-password-file`` or ``--ask-vault-pass`` options, or it can be used in combination with them.


Using SSH Keys in Ansible
=========================

Setup your environment for SSH
------------------------------

If your target machine doesn't have a ``.ssh`` folder you'll want to create it under the root directory::

    sudo su
    cd
    mkdir .ssh
    chmod 700 .ssh
    touch .ssh/authorized_keys
    chmod 644 .ssh/authorized_keys
    
You'll want to add the public key of the host you're sshing from into the authorized keys file

Make sure they keys on your Ansible host have the correct permissions::

    chmod 600 for private keys
    chmod 644 for public keys
    chmod 700 for ssh directory

Next you'll need to edit the /etc/ssh/sshd_config file on your target machine::

    vim /etc/ssh/sshd_config
    # add the line
    PermitRootLogin yes

This line will allow Ansible to ssh in as root.

Ensure the ``ssh`` service is running::

    systemctl status ssh
    # or
    service ssh status


Setting up your Playbook and Inventory for SSH
----------------------------------------------

In your playbook you will need to add the field ``remote_user: root`` somewhere between hosts and tasks::

    ---
    - hosts: webservers
      connection: local
      remote_user: root
      gather_facts: False
      tasks:

In your inventory you will need to add the field "ansible_ssh_private_key_file" for each host or under global variables::

    [servers]
    host1 ansible_ssh_private_key_file=/root/.ssh/id_rsa
    host2 ansible_ssh_private_key_file=/root/.ssh/id_rsa
    # or
    [servers:vars]
    ansible_ssh_private_key_file=/root/.ssh/id_rsa

Finally under your ``ansible.cfg`` you will need to disable ``host_key_checking`` this is what prompts you to verify the fingerprint, which you won't want to manually do every single time::

    [defaults]
    host_key_checking=False

