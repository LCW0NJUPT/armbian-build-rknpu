
```shell
apt-get -y install git
sudo apt install build-essential texinfo gcc  
git clone --depth=1 --branch=main https://github.com/LCW0NJUPT/armbian-build-rknpu
cd armbian-build-rknpu  
./compile.sh KERNEL_GIT=shallow # If giving problems, you could try with sudo, but not recommended  
```
如果您在中国，请全程挂代理。检测和设置代理如下： 
```shell
env |grep -i proxy  
unset http_proxy  
unset https_proxy  
export http_proxy="您的代理IP:端口"  
export https_proxy="您的代理IP:端口"
```
检测和设置ssh代理如下：  
```shell
git config --list --show-origin  
git config --global http.proxy http://您的代理IP:端口  
git config --global https.proxy https://您的代理IP:端口
```
或者  
```shell
git config --global http.proxy 您的代理IP:端口    
git config --global http.sslverify false
```  
取消代理    
```shell
git config --global --unset http.proxy    
git config --global --unset http.sslverify
```  


接下来可以参考这篇材料 https://github.com/Pelochus/armbian-build-rknpu-updates  
• Change kernel config? No (unless you are sure what you're doing)  
更改内核配置？否（除非您确定自己在做什么）  
• Board: Select your board I use Orange Pi 5. Other supported are OPi 5 Plus, Rock Pi 5...  
板子：选择你的板子 I use Orange Pi 5。其他支持的有 OPi 5 Plus、Rock Pi 5…  
我选Rock5B  
• Kernel: Select vendor  
内核：选择供应商  
• Release: Jammy, since its closer to Ubuntu, hence easier to use. You can choose Bookworm if you prefer Debian  
发布：Jammy，因为它更接近 Ubuntu，因此更易于使用。如果您更喜欢 Debian，可以选择 Bookworm  
我选了Nobel  
• Desktop: Choose based on personal preference, use CLI if low on RAM  
桌面：根据个人喜好选择，如果 RAM 不足，请使用 CLI  
很显然，为了桌面硬件加速，肯定选Desktop+gnome  
• Minimal/Standard: Standard unless you really prefer installing your packages by yourself, but not worth the effort usually  
Minimal/Standard：除非您真的喜欢自己安装软件包，否则 Standard （标准），但通常不值得付出努力  
我选了需要的几个  

接下来是漫长的等待时间，国内的网络非常影响开发效率  


• 到了内核设置后，先各种选择，最后在出现packages的时候ctrl+c出去，手动换个源  
```shell
• find ./ -name sources.list  
• find ./ -name armbian.list
```
![image](https://github.com/user-attachments/assets/b87f7d8c-0e9a-451e-818e-e06f72942abd)


需要重复编译的完整指令如下
desktop版本：
```shell
./compile.sh build BOARD=rock-5b BRANCH=vendor BUILD_DESKTOP=yes BUILD_MINIMAL=no DESKTOP_APPGROUPS_SELECTED='browsers desktop_tools editors internet multimedia programming remote_desktop' DESKTOP_ENVIRONMENT=gnome DESKTOP_ENVIRONMENT_CONFIG_NAME=config_base KERNEL_CONFIGURE=yes KERNEL_GIT=shallow RELEASE=noble
```
server版本：
```shell
./compile.sh build BOARD=rock-5b BRANCH=vendor BUILD_DESKTOP=no BUILD_MINIMAL=no KERNEL_CONFIGURE=yes KERNEL_GIT=shallow RELEASE=noble
```

• Then, if everything goes alright, output image will be in output/images. Flash it to your SD card and you are done!  
然后，如果一切正常，输出图像将位于 output/images 中。将其刷入您的 SD 卡，您就完成了！  
If not, check Armbian documentation or README below  
如果没有，请查看下面的 Armbian 文档或 README 文件  

<p align="center">
  <a href="#build-framework">
  <img src=".github/armbian-logo.png" alt="Armbian logo" width="144">
  </a><br>
  <strong>Armbian Linux Build Framework</strong><br>
<br>
<a href=https://github.com/armbian/build/graphs/contributors><img alt="GitHub contributors" src="https://img.shields.io/github/contributors-anon/armbian/build?logo=stackexchange&label=Contributors&style=for-the-badge&branch=main&logoColor=white"></a>
<a href=https://github.com/armbian/os><img alt="Artifacts generation" src="https://img.shields.io/github/actions/workflow/status/armbian/os/complete-artifact-matrix-all.yml?logo=dependabot&label=CI%20Build&style=for-the-badge&branch=main&logoColor=white"></a>
<a href=https://github.com/armbian/build/commits/main><img alt="GitHub last commit (branch)" src="https://img.shields.io/github/last-commit/armbian/build/main?logo=github&label=Last%20commit&style=for-the-badge&branch=main&logoColor=white"></a>
</p>

## What does this project do?

- Builds custom **kernel**, **image** or a **distribution** optimized for low-resource hardware,
- Include filesystem generation, low-level control software, kernel image and **bootloader** compilation,
- Provides a **consistent user experience** by keeping system standards across different platforms.

## Getting started

### Requirements for self hosted

- x86_64 / aarch64 machine
- at least 2GB of memory and ~35GB of disk space for VM, container or bare metal installation
- [Armbian / Ubuntu Jammy 22.04.x](https://github.com/armbian/sdk) for native building or any Docker capable Linux for containerised
- Windows 10/11 with WSL2 subsystem running Ubuntu Jammy 22.04.x
- Superuser rights (configured sudo or root access).
- Make sure your system is up-to-date! Outdated Docker binaries, for example, can cause trouble.

For stable branch use `--branch=v24.11`

```bash
apt-get -y install git
git clone --depth=1 --branch=main https://github.com/armbian/build
cd build
./compile.sh
```

<a href="#how-to-build-an-image-or-a-kernel"><img src=".github/README.gif" alt="Armbian logo" width="100%"></a>

- Interactive graphical interface.
- Prepares the workspace by installing the necessary dependencies and sources.
- It guides the entire process and creates a kernel package or a ready-to-use SD card image.

### Build parameter examples

Show work-in-progress areas in interactive mode:

```bash
./compile.sh EXPERT="yes"
```

Build minimal CLI Armbian Jammy for Bananapi M5 with LTS kernel:

```bash
./compile.sh \
BOARD=bananapim5 \
BRANCH=current \
RELEASE=jammy \
BUILD_MINIMAL=yes \
BUILD_DESKTOP=no \
KERNEL_CONFIGURE=no
```

Build with GitHub actions: ([advanced version](https://github.com/armbian/os/blob/main/.github/workflows/complete-artifact-one-by-one.yml))

```
name: "Build Armbian"
on:
  workflow_dispatch:
jobs:
  build-armbian:
    runs-on: ubuntu-latest
    steps:
      - uses: armbian/build@main
        with:
          armbian_token:     "${{ secrets.GITHUB_TOKEN }}"  # GitHub token
          armbian_release:   "jammy"                        # userspace
          armbian_target:    "build"                        # build=image, kernel=kernel
          armbian_board:     "bananapim5"                   # build target
```
Generated image will be uploaded to your repository release. Note: GitHub upload file limit is 2Gb.

## More information:

- [Building Armbian](https://docs.armbian.com/Developer-Guide_Build-Preparation/) (how to start)
- [Build commands](https://docs.armbian.com/Developer-Guide_Build-Commands/) and [switches](https://docs.armbian.com/Developer-Guide_Build-Switches/) (build options)
- [User configuration](https://docs.armbian.com/Developer-Guide_User-Configurations/) (how to add packages, patches, and override sources config)
- [System config](https://docs.armbian.com/User-Guide_Armbian-Config/) (menu driven utility to setup OS and HW features)

## Download prebuilt images releases

### Point

- [manually released **standard supported** builds](https://www.armbian.com/download/?device_support=Standard%20support) (quarterly)

### Rolling

- [automatically released **staging and standard supported** builds](https://github.com/armbian/os/releases/latest) (daily)
- [automatically released **community maintained** builds](https://github.com/armbian/community/releases/latest) (weekly)

## Compared with industry standards

<details><summary>Expand</summary>
Check similarities, advantages and disadvantages compared with leading industry standard build software.

Function | Armbian | Yocto | Buildroot |
|:--|:--|:--|:--|
| Target | general purpose | embedded | embedded / IOT |
| U-boot and kernel | compiled from sources | compiled from sources | compiled from sources |
| Board support maintenance &nbsp; | complete | outside | outside |
| Root file system | Debian or Ubuntu based| custom | custom |
| Package manager | APT | any | none |
| Configurability | limited | large | large |
| Initramfs support | yes | yes | yes |
| Getting started | quick | very slow | slow |
| Cross compilation | yes | yes | yes |
</details>

## Project structure

<details><summary>Expand</summary>

```text
├── cache                                Work / cache directory
│   ├── aptcache                         Packages
│   ├── ccache                           C/C++ compiler
│   ├── docker                           Docker last pull
│   ├── git-bare                         Minimal Git
│   ├── git-bundles                      Full Git
│   ├── initrd                           Ram disk
│   ├── memoize                          Git status
│   ├── patch                            Kernel drivers patch
│   ├── pip                              Python
│   ├── rootfs                           Compressed userspaces
│   ├── sources                          Kernel, u-boot and other sources
│   ├── tools                            Additional tools like ORAS
│   └── utility
├── config                               Packages repository configurations
│   ├── targets.conf                     Board build target configuration
│   ├── boards                           Board configurations
│   ├── bootenv                          Initial boot loaders environments per family
│   ├── bootscripts                      Initial Boot loaders scripts per family
│   ├── cli                              CLI packages configurations per distribution
│   ├── desktop                          Desktop packages configurations per distribution
│   ├── distributions                    Distributions settings
│   ├── kernel                           Kernel build configurations per family
│   ├── sources                          Kernel and u-boot sources locations and scripts
│   ├── templates                        User configuration templates which populate userpatches
│   └── torrents                         External compiler and rootfs cache torrents
├── extensions                           Extend build system with specific functionality
├── lib                                  Main build framework libraries
│   ├── functions
│   │   ├── artifacts
│   │   ├── bsp
│   │   ├── cli
│   │   ├── compilation
│   │   ├── configuration
│   │   ├── general
│   │   ├── host
│   │   ├── image
│   │   ├── logging
│   │   ├── main
│   │   └── rootfs
│   └── tools
├── output                               Build artifact
│   └── deb                              Deb packages
│   └── images                           Bootable images - RAW or compressed
│   └── debug                            Patch and build logs
│   └── config                           Kernel configuration export location
│   └── patch                            Created patches location
├── packages                             Support scripts, binary blobs, packages
│   ├── blobs                            Wallpapers, various configs, closed source bootloaders
│   ├── bsp-cli                          Automatically added to armbian-bsp-cli package
│   ├── bsp-desktop                      Automatically added to armbian-bsp-desktopo package
│   ├── bsp                              Scripts and configs overlay for rootfs
│   └── extras-buildpkgs                 Optional compilation and packaging engine
├── patch                                Collection of patches
│   ├── atf                              ARM trusted firmware
│   ├── kernel                           Linux kernel patches
|   |   └── family-branch                Per kernel family and branch
│   ├── misc                             Linux kernel packaging patches
│   └── u-boot                           Universal boot loader patches
|       ├── u-boot-board                 For specific board
|       └── u-boot-family                For entire kernel family
├── tools                                Tools for dealing with kernel patches and configs
└── userpatches                          User: configuration patching area
    ├── lib.config                       User: framework common config/override file
    ├── config-default.conf              User: default user config file
    ├── customize-image.sh               User: script will execute just before closing the image
    ├── atf                              User: ARM trusted firmware
    ├── kernel                           User: Linux kernel per kernel family
    ├── misc                             User: various
    └── u-boot                           User: universal boot loader patches
```
</details>

## Contribution

### Want to help?

We always need those volunteering positions:

- [Code reviewer](https://forum.armbian.com/staffapplications/application/23-code-reviewer/)
- [Build framework maintainer](https://forum.armbian.com/staffapplications/application/9-build-framework-maintainer/)
- [Test Automation Engineer](https://forum.armbian.com/staffapplications/application/19-test-automation-engineer/)

Just apply and follow!

## Support

For commercial or prioritized assistance:
 - Book an hour of [professional consultation](https://calendly.com/armbian/consultation)
 - Consider becoming a [project partner](https://forum.armbian.com/subscriptions/)
 - [Contact us](https://armbian.com/contact)!

Free support:

 Find free support via [general project search engine](https://www.armbian.com/search), [documentation](https://docs.armbian.com), [community forums](https://forum.armbian.com/) or [IRC/Discord](https://docs.armbian.com/Community_IRC/). Remember that our awesome community members mainly provide this in a **best-effort** manner, so there are no guaranteed solutions.

## Contact

- [Forums](https://forum.armbian.com) for Participate in Armbian
- IRC: `#armbian` on Libera.chat / oftc.net
- Matrix: [https://forum.armbian.com/topic/40413-enter-the-matrix/](https://forum.armbian.com/topic/40413-enter-the-matrix/)
- Discord: [https://discord.gg/armbian](https://discord.gg/armbian)
- Follow [@armbian](https://twitter.com/armbian) on 𝕏 (formerly known as Twitter), <a rel="me" href="https://fosstodon.org/@armbian">Mastodon</a> or [LinkedIn](https://www.linkedin.com/company/armbian).
- Bugs: [issues](https://github.com/armbian/build/issues) / [JIRA](https://armbian.atlassian.net/jira/dashboards/10000)
- Office hours: [Wednesday, 12 midday, 18 afternoon, CET](https://calendly.com/armbian/office-hours)

## Contributors

Thank you to all the people who already contributed to Armbian!

<a href="https://github.com/armbian/build/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=armbian/build" />
</a>

### Also

- [Current and past contributors](https://github.com/armbian/build/graphs/contributors), our families and friends.
- [Support staff](https://forum.armbian.com/members/2-moderators/) that keeps forums usable.
- [Friends and individuals](https://armbian.com/authors) who support us with resources and their time.
- [The Armbian Community](https://forum.armbian.com/) helps with their ideas, reports and [donations](https://www.armbian.com/donate).

## Armbian Partners

Armbian's partnership program helps to support Armbian and the Armbian community! Please take a moment to familiarize yourself with our Partners:

- [Click here to visit our Partners page!](https://armbian.com/partners)
- [How can I become a Partner?](https://forum.armbian.com/subscriptions)

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=armbian/build&type=Date)](https://star-history.com/#armbian/build&Date)

## License

This software is published under the GPL-2.0 License license.
