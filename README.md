# Ansible Vault Demo

Sample for using Ansible Vault to secure secrets in Git repository.

## Configure

To make this tutorial simple, create a file with the vault key:

```
pwgen 64 > .vault_key
```

Edit the template file and encrypt it with the vault password:

```sh
ansible-vault encrypt tasks/files/config-plain.yml --output tasks/files/config-encrypted.yml
```

If you're using the file in place, revert the changes:

```sh
git restore tasks/files/config-plain.yml
```

These operations can be automated with `--vault-password-file <path-to-vault-key-file>`.


## Pull

On your remote destination, use the `--vault-password-file` argument to pull:

```sh
sudo ansible-pull --vault-password-file ~/.vault_key -U https://github.com/epomatti/ansible-vault-demo
```

The file will be decrypted and copied to your current directory:

```sh
cat config.yml
```

## Changing the Vault Password

To change key for the same file, use the `rekey` command:

```sh
ansible-vault rekey tasks/files/config-encrypted.yml
```

## Git Locks

There are a few options that can be used to protect a versioned file that will retain temporary sensitive information locally if you choose to edit it in place:

- [Git Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks) can be used to `diff` the filesystem and detect undesired changes with the `.git/hooks/pre-commit` file.
- Change file permissions with `chmod 444` but this is not really effective.
- Use proprietary SCM protection mechanisms, such as GitHub [protected branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches) functionality.
- Git submodules with `git submodule add <repo-url> locked_file`

For simplicity, this project implements a `chmod 444` control.
