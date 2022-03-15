Mac Configuration via Ansible
=============================

I use this project to set up my Mac workstations in an automated way that facilitates re-installation and updates with much less effort.

1. First follow the [initial Mac set-up steps](https://hackercodex.com/guide/mac-development-configuration/) outlined in a companion article I wrote, the primary components of which are installing the Mac Command-Line Tools and Homebrew: [Configuring MacOS](https://hackercodex.com/guide/mac-development-configuration/)

2. Use Homebrew to install [Pipx](https://pypa.github.io/pipx/):

    ```sh
    brew install pipx
    ```

3. Use Pipx to install [Ansible 2.9](https://docs.ansible.com/ansible/2.9/):

    ```sh
    PIPX_DEFAULT_PYTHON=/usr/bin/python3 pipx install "ansible~=2.9.0"
    ```

4. If not already present, generate an SSH key using the following commands, entering a long passphrase when prompted (`ssh-add` will add that passphrase to the MacOS key-chain):

    ```sh
    cd ~/.ssh/
    ssh-keygen -t ed25519 -o -a 100
    ssh-add --apple-use-keychain ~/.ssh/id_ed25519
    ```

5. Clone this repository:

    ```sh
    mkdir ~/Projects
    git clone https://github.com/justinmayer/mac-ansible.git ~/Projects/git-ansible
    cd ~/Projects/mac-ansible/
    ```

6. Review `roles/mac/files/Brewfile` and determine if you need to make any changes. If so, copy that file to `~/.Brewfile` and then make your desired changes:

    ```sh
    cp roles/mac/files/Brewfile ~/.Brewfile

    # Otherwise, if you don’t need to make any changes,
    # you can symlink it instead:

    ln -s $PWD/roles/mac/files/Brewfile $HOME/.Brewfile
    ```

7. Run the Ansible playbook, which comes with several configurable options. The following invocation will use the default settings:

    ```sh
    ./playbook.yml --ask-become-pass -e "sudo=yes"
    ```

If you want to use the default shell (`zsh`) instead of Fish shell:

```sh
./playbook.yml --ask-become-pass -e "sudo=yes" --skip-tags shell
```

To skip installation of packages and applications via Homebrew:

```sh
./playbook.yml --ask-become-pass -e "sudo=yes homebrew_use_brewfile=false"
```

See `roles/mac/defaults/main.yml` for a list of other variables that can be overridden using the above `-e "key=value"` method.

Once initial set-up has been done, most follow-up invocations (that will likely no longer require elevated permissions) can be performed via:

    ./playbook.yml
