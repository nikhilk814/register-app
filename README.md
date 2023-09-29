# Install and Configure Jenkins - Master and Agent

This guide will walk you through the installation and configuration of Jenkins on your server, along with setting up Java and SSH for Jenkins.

## Install Java

1. Update the package list and upgrade existing packages:

    ```bash
    $ sudo apt update
    $ sudo apt upgrade
    ```

2. Set the hostname (optional, but useful for identifying your server):

    ```bash
    $ sudo nano /etc/hostname
    $ sudo init 6   # Reboot the server for the hostname change to take effect
    ```

3. Install OpenJDK 17 (or the desired Java version):

    ```bash
    $ sudo apt install openjdk-17-jre
    ```

4. Verify the Java installation:

    ```bash
    $ java -version
    ```

## Install Jenkins

Follow these steps to install Jenkins on your server:

1. Add the Jenkins repository key:

    ```bash
    $ curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
    ```

2. Add the Jenkins repository to the package sources:

    ```bash
    $ echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
    ```

3. Update the package list:

    ```bash
    $ sudo apt-get update
    ```

4. Install Jenkins:

    ```bash
    $ sudo apt-get install jenkins
    ```

5. Enable Jenkins to start at boot:

    ```bash
    $ sudo systemctl enable jenkins
    ```

6. Start Jenkins as a service:

    ```bash
    $ sudo systemctl start jenkins
    ```

7. Check Jenkins service status:

    ```bash
    $ systemctl status jenkins
    ```

## Configure SSH

1. Edit the SSH server configuration file:

    ```bash
    $ sudo nano /etc/ssh/sshd_config
    ```

2. Reload the SSH server configuration:

    ```bash
    $ sudo service sshd reload
    ```

3. Generate SSH keys (Choose either RSA or Ed25519):

    For RSA keys:

    ```bash
    $ ssh-keygen
    ```

    OR for Ed25519 keys:

    ```bash
    $ ssh-keygen -t ed25519
    ```

4. Navigate to the SSH key directory (usually `~/.ssh`) and make any necessary modifications:

    ```bash
    $ cd ~/.ssh
    ```

That's it! You've successfully installed and configured Jenkins on your server, along with Java and SSH for Jenkins to use.


# Jenkins Master and Agent Setup

## Table of Contents
- [Introduction](#introduction)
- [Install Java](#install-java)
- [Install Jenkins](#install-jenkins)
- [Configure SSH](#configure-ssh)

## Introduction

This guide provides instructions for setting up a Jenkins master and agent, including installing Java, Jenkins, and configuring SSH for secure communication.

## Install Java

1. Update the package list and upgrade existing packages:

   ```shell
   $ sudo apt update
   $ sudo apt upgrade

Set the hostname (optional but useful for server identification):
$ sudo nano /etc/hostname
$ sudo init 6
Install OpenJDK 17 (or the desired Java version):
$ sudo apt install openjdk-17-jre
Verify the Java installation:
$ java -version

Certainly, you can convert your instructions into a unique format for a GitHub README file using Markdown syntax. Here's how you can format your content:

