# xdeb-install-repositories

This project aims to provide package repositories for [xdeb-install](https://github.com/thetredev/xdeb-install). Head over there to find out more about the tool itself.

## Supported repositories

### APT providers

| Provider | Components | Repository URL | Provider Architecture | Void Linux Architecture |
| --- | --- | --- | --- | --- |
| `debian.org` | all | http://ftp.debian.org/debian | `amd64` | `x86_64` |
| | all | | `i386` | `i686` |
| | all | | `arm64` | `aarch64` |
| `linuxmint.com` | all | http://packages.linuxmint.com | `amd64` | `x86_64` |
| | all | | `i386` | `i686` |
| `ubuntu.com` | all | http://archive.ubuntu.com/ubuntu | `amd64` | `x86_64` |
| | all | | `i386` | `i686` |

### Custom providers

| Provider | Components | Component URL | Provider Architecture | Void Linux Architecture |
| --- | --- | --- | --- | --- |
| `microsoft.com` | Visual Studio Code | https://code.visualstudio.com/sha/download?build=stable&os=linux-deb-x64 | `amd64` | `x86_64` |
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
