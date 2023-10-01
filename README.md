# xdeb-install-repositories

This project aims to provide package repositories for [xdeb-install](https://github.com/thetredev/xdeb-install). Head over there to find out more about the tool itself.

## Supported repositories

Currently, only `debian.org` and `linuxmint.com` are supported besides custom ones. I cannot get `ubuntu.com` to work at the moment.

## Structure

### APT repositories

APT repositories are described in the `repositories/<arch>/lists.yaml` file, where `<arch>` is the host machine architecture reported by Void Linux (output of command `uname -m`). Due to possible discrepancies with APT repositories (`x86_64` &rarr; `amd64` for example) the separate `<arch>` directory is needed.

The `lists.yaml` file has the following format:
```yaml
providers:
  - name: <provider name>
    url: <APT repository root URL, where the directories 'dists' and 'pool' can be found>
    architecture: <architecure as required by APT repository>
    components:
      - <component1>
      - <component2>
      ...
    dists:
      - <distribution1>
      - <distribution2>
      ...
```

For example, the `debian.org` provider for `x86_64` is defined as follows:
```yaml
providers:
  - name: debian.org
    url: http://ftp.debian.org/debian
    architecture: amd64
    components:
      - contrib
      - main
      - non-free
      - non-free-firmware
    dists:
      - bookworm
      - bookworm-backports
      - bullseye
      - bullseye-backports
      - buster
      - buster-backports
      - sid
      - testing
      - testing-backports
```

### Custom repository lists

The repository `lists.yaml` file needs to have `custom: true` set for custom repositories:
```yaml
providers:
  - name: <provider name>
    custom: true
    url: <custom repository URL>
    components:
      - <component1>.yaml
      - <component2>.yaml
      ...
    dists:
      - <distribution1>
      - <distribution2>
      ...
```

For example, the `microsoft.com` provider for `x86_64` is defined as follows:
```yaml
providers:
  - name: microsoft.com
    custom: true
    url: https://raw.githubusercontent.com/thetredev/xdeb-install-repositories/main/repositories/x86_64/microsoft.com
    components:
      - vscode.yaml
    dists:
      - current
```

### Custom repositories

Custom repositories are described in the `repositories/<arch>/<provider>/<component>.yaml` file, where `<arch>` is the host machine architecture reported by Void Linux (output of command `uname -m`).

The repository components have the following format:
```yaml
xdeb:
  - name: <package name>
    url: <DEB file download location>
  ...
```

For example, the `microsoft.com` provider for `x86_64` has the following definition for component `vscode.yaml`:
```yaml
xdeb:
  - name: vscode
    url: https://code.visualstudio.com/sha/download?build=stable&os=linux-deb-x64
```
