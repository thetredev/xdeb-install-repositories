# xdeb-install-repositories

This project aims to provide package repositories for [xdeb-install](https://github.com/thetredev/xdeb-install). Head over there to find out more about the tool itself.

## Supported repositories

### APT providers

| Provider | Components | Repository URL | Provider Architecture | Void Linux Architecture |
| --- | --- | --- | --- | --- |
| `debian.org` | all | http://ftp.debian.org/debian | `amd64` | `x86_64` |
| | all | | `i386` | `i686` |
| | all | | `arm64` | `aarch64` |
| `linuxmint.com` | all | https://mirrors.edge.kernel.org/linuxmint-packages | `amd64` | `x86_64` |
| | all | | `i386` | `i686` |
| `ubuntu.com` | all | http://archive.ubuntu.com/ubuntu | `amd64` | `x86_64` |
| | all | | `i386` | `i686` |

Note: The official mirror for `linuxmint.com` (http://packages.linuxmint.com) was replaced with https://mirrors.edge.kernel.org/linuxmint-packages on 2023-12-09 due to the official one being unreachable sporadically. This hinders development, testing, and last but not least the usability of `xdeb-install` as a whole. The mirror will be changed back to the official one once it's fixed, if at all.

### Custom providers

| Provider | Components | Component URL | Provider Architecture | Void Linux Architecture |
| --- | --- | --- | --- | --- |
| `code.visualstudio.com` | Visual Studio Code | https://code.visualstudio.com/sha/download?build=stable&os=linux-deb-x64 | `amd64` | `x86_64` |
| | | https://code.visualstudio.com/sha/download?build=stable&os=linux-deb-arm64 | `arm64` | `aarch64` |
| `google.com` | Google Chrome | https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb | `amd64` | `x86_64` |
| | | https://dl.google.com/linux/direct/google-chrome-stable_current_arm64.deb | `arm64` | `aarch64` |
| `google.com` | Google Chrome (unstable) | https://dl.google.com/linux/direct/google-chrome-unstable_current_amd64.deb | `amd64` | `x86_64` |
| | | https://dl.google.com/linux/direct/google-chrome-stable_current_arm64.deb | `arm64` | `aarch64` |

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
      - main
      - contrib
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
    architecture: <architecture>
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
    architecture: amd64
    url: repositories/x86_64/microsoft.com
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

Optionally, `post-install` hooks can be defined which allow for executing shell commands after package installation:
```yaml
xdeb:
  - name: <package name>
    url: <DEB file download location>
    post-install:
      - name: Hook1 - some descriptive name
        commands:
          - root: true # whether to run as root user (prepends 'sudo' if xdeb-install is executed as non-root user)
            command: command1 arg1 arg2 arg3
          - root: false
            command: command2 arg1 arg2 arg3
      - name: Hook2 - another descriptive name
        commands:
          - root: true
            command: command1 arg1 arg2 arg3
          - root: true
            command: command2 arg1 arg2 arg3
          ...
      ...
  ...
```

For example, the `code.visualstudio.com` provider for `x86_64` has the following definition for component `vscode.yaml`:
```yaml
xdeb:
  - name: vscode
    url: https://code.visualstudio.com/sha/download?build=stable&os=linux-deb-x64
    post-install:
      - name: Link 'code' executable
        commands:
          - root: true
            command: ln -sf /usr/share/code/bin/code /usr/local/bin/code
```
