# Ansible Vault Demo

Sample for using Ansible Vault to secure secrets in Git repository.

## Configure

To make this tutorial simple, create a file with the vault key:

```sh
pwgen 64 > .vault_key
chmod 600 .vault_key
```

Edit the template file and encrypt it with the vault password:

```sh
ansible-vault encrypt tasks/files/config-plain.yml --vault-password-file .vault_key --output tasks/files/config-encrypted.yml
```

If you're using the file in place, revert the changes:

```sh
git restore tasks/files/config-plain.yml
```

If the vault key password is protected, changes can now be security committed to the repository.

## Pull

On your remote destination, use the `--vault-password-file` argument to pull the configuration:

```sh
sudo ansible-pull --vault-password-file .vault_key -U https://github.com/epomatti/ansible-vault-demo
```

By default the output will be relative to `/root/.ansible/pull/`.

The file will be decrypted and copied to the pull directory.

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
