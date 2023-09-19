# Reproduction of error

## Tasks

### create-multipass-vm

Create a new VM. If it already exists, delete it with `multipass delete nix-issue-8953`, then `multipass purge`

```sh
multipass launch -n nix-issue-8953 --disk 10G --cloud-init cloud-init.yaml --verbose
```

### view-startup-logs

You can check the startup logs to check that the commands in cloud-init.yaml were successful.

```sh
multipass exec nix-issue-8953 -- sudo cat /var/log/cloud-init-output.log
```

### view-registry-json

We can see that there's a registry.json already created by the startup script. It contains a redirection from github to the local disk.

```sh
multipass exec nix-issue-8953 -- sudo cat /root/.config/nix/registry.json
```

### view-registry-list

We can see that Nix is aware of the user flake registry override.

```sh
multipass exec nix-issue-8953 -- sudo bash --login -c "nix registry list"
```

### view-system-manager-source-code-dir

We can see that the source code is present on the disk, in the expected place.

```sh
multipass exec nix-issue-8953 -- sudo ls /flakes/github.com/numtide/system-manager
```

### run

We can see that it can't run the flake, because it's trying to download from Github, when it should be looking on the local disk instead.

```sh
multipass exec nix-issue-8953 -- sudo bash --login -c "nix run github:numtide/system-manager"
```
