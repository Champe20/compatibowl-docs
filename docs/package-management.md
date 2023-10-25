# Package Management
Compatibowl has many options for installing linux-native software from any distro.

## Flatpak
Flatpak runs each app in its own isolated and containerized evironment. This is the prefered method of installing linux apps on Compatibowl. You can use a Desktop Environment's software store or use it via the command line using `flatpak install <package-name>.` It is only really a valid option for desktop apps.

## APX
APX installs packages in containers and tighly integrates them with the base system. Packages from the AUR and DNF can be installed simply running `apx install --aur <package-name>` and `apx install --dnf <package-name>`, repectively.

## Nix 
Packages installed from Nix are just as easy to install. Simply run `nix-env -iA <package-name>`. If the software is not cached on Nix's severs, it will build it from source.

## Distrobox and Toolbox

Should none of the above options work you should utilize toolbox or distrobox. Toolboxes are little CLI podman containers that you can install anything into without it polluting your host system. Read more on [the dedicated page](/guide/toolbox).

## rpm-ostree
`rpm-ostree` is the system package manager for Compatibowl. Unlike more traditional Linux desktops, on an image-based Linux distribution you probably shouldn't install everything directly onto your base system. The more liberally you use the system package manager, the more likely you are to run into some instability that affects your base operating system, therefore we recommend that you use it **sparingly**.

To install a single package (for example, `zsh`), run:

```bash
sudo rpm-ostree install zsh
```

To install multiple packages (for example, various CLI text editors), run:

```bash
sudo rpm-ostree install micro neovim helix
```

To apply the staged new staged image that includes your packages, reboot your computer.

There is no search functionality in `rpm-ostree`, but you can use a general package search website such as [Repology](https://repology.org/).
There is also no command to enable [copr](https://copr.fedorainfracloud.org/) or other repositories in `rpm-ostree`, but you can add the repo files manually to `/etc/yum.repos.d/`