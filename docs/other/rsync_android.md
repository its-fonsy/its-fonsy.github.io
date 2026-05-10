# How to setup rsync on android

The phone will be referred as REMOTE, the computer as HOST.

## Setup on the REMOTE

First download Termux from the Fdroid store on the phone.
Open Termux and update the system

    pkg upgrade

Next give Android storage permission to Termux

    termux-setup-storage

Install "ssh", and "rsync" packages

    pkg install openssh termux-auth rsync

Start the ssh-server

    sshd

Get username and ip address of the phone

    whoami
    ifconfig

Set a new password for the user

    passwd

## Copy HOST ssh key to REMOTE

On HOST, if not having one, generate a ssh key

    ssh-keygen

it should be located in "$HOME/.ssh", copy the public key to the phone

    ssh-copy-id -i $HOME/.ssh/id.pub -p 8022 TERMUX_USRNAME@TERMUX_IP_ADDRESS

*IMPORTANT*: Termux use port "8022" for the ssh connection.

### Add REMOTE to the ssh configuration

To add to the ssh configuration in "$HOME/.ssh/config"

    Host phone
        HostName 192.168.1.191
        User u0_a329
        Port 8022
        IdentityFile ~/.ssh/phone

## Sync files

    # Sync music folder from HOST to REMOTE
    rsync -avP -e 'ssh -p 8022' $HOME/Music/ TERMUX_USERNAME@TERMUX_IP_ADDRESS:storage/music
