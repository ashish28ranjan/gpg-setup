# GPG-2 setup

### Installation

1. Install ***gpg2*** (for ubuntu)
```sudo apt-get install gnupg2```

1. Add following in ***~/.bash_aliases***
```alias gpg='gpg2'```

1. Source ***~/.bashrc***
```source ~/.bashrc```

1. Create ***~/.gnupg*** using one of the following ---
    - Copy any backed up ***.gnupg*** directory to ***~/.gnupg***, or
    - Initialize ***~/.gnupg*** directory using ```gpg -k```

1. Copy ***gpg.conf*** & ***gpg-agent.conf*** from this repository to ***~/.gnupg***

1. Add the following to ***~/.bashrc*** or ***~/.bash_profile***
    ```
    export GPG_TTY=$(tty)
    unset SSH_AGENT_PID
    if [ "${gnupg_SSH_AUTH_SOCK_by:-0}" -ne $$ ]; then
      export SSH_AUTH_SOCK="${HOME}/.gnupg/S.gpg-agent.ssh"
    fi
    gpg-connect-agent /bye
    ```

1. Source ***~/.bashrc***
```source ~/.bashrc```

1. Now, **logout and login**

1. If needed,
    - Create a new primary key using ```gpg --expert --full-gen-key```
      - Use >=4096 bit RSA for primary key
      - Use >=2048 bit RSA for sub keys
    - Edit keys using ```gpg --expert --edit-key <uid>```
        - Add new subkeys using ```addkey```
        - Change passwords using ```passwd```
        - Type ```help``` for more options

1. Create one subkey each of **Authentication, Encryption, Signing**

1. To **add SSH support in gpg**, you must **create an *Authentication* subkey**, marked as ***[A]***

1. To generate ***sshcontrol*** file, type ```ssh-add -l```

1. **List gpg keys** by ---
    - Public keys: ```gpg -k```
    - Private keys: ```gpg -K```. If ***#*** appears after ***sec*** or ***ssb***, then it means private key is not present for that key-id

1. Get ***keygrip*** of the keys
```gpg -k --with-keygrip <uid>```

1. **Add keygrip** of your authentication key to ***sshcontrol*** file **in a new line**

1. Export **SSH public key** to be put on the servers' ***~/.ssh/authorized_keys***
```gpg --export-ssh-key <key-id>```

1. You may now be able to SSH directly into the server

1. *** ***VERY IMPORTANT*** *** Backup the whole ***~/.gnupg*** directory to a safe & secure place

1. After backup has been created, you **must change passwords** of the primary key & sub keys

1. Now ***delete the unnecessary private keys***. To delete the private keys ---
    - Find out the ***<keygrip>*** of the keys using the above command
    - Delete ***~/.gnupg/private-keys-v1.d/<keygrip>.key***

1. To **edit existing keys** or to create a new subkey ---
    - Backup the ***~/.gnupg*** folder before proceeding (just in case anything goes wrong)
    - Retrieve the **remotely** backed up ***.gnupg*** directory (this backup is different from the one created using the just above instruction) in a ***tmp-path/.gnupg*** folder. Note that this folder contains all of the secret keys
    - Run relevant commands by adding ***homedir*** like, ```gpg --homedir tmp-path/.gnupg ...```
    - **Push the new changes** to the keyserver using ```gpg --homedir tmp-path/.gnupg --keyserver pgp.key-server.io --send-key <YOURMASTERKEYID>```
    - **Retrieve changes** to your ***~/.gnupg*** using ```gpg --keyserver pgp.key-server.io --recv-key <YOURMASTERKEYID>```
    - To **transfer private keys** to local folder, copy the ***tmp-path**/.gnupg/private-keys-v1.d/****<keygrip>.key*** to ***~**/.gnupg/private-keys-v1.d/****<keygrip>.key***

1. To **search a key** in the keyserver use ```gpg --keyserver pgp.key-server.io --search-key <search-text>```

1. **Export keys** using ---
    - **Public** ```gpg -a --export <uid>```
      - ```-a``` option is same as ```--armor``` and is used to generate ASCII text
    - **Private** ```gpg -a --export-secret-keys <uid>```

1. Run ```gpgconf --kill gpg-agent``` to kill any running agent that might be hung


### Resources

1. https://wiki.debian.org/Subkeys
1. https://www.gnupg.org/documentation/manuals/gnupg/Agent-Configuration.html
1. https://github.com/kylef/dotfiles/blob/master/.gnupg/gpg.conf
1. https://www.gnupg.org/gph/en/manual/book1.html
1. https://www.gnupg.org/documentation/manuals/gnupg/GPG-Configuration-Options.html

