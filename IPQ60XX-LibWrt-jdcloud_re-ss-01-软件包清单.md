# IPQ60XX-LibWrt — jdcloud_re-ss-01 固件软件包清单

> 只分析 `jdcloud_re-ss-01`(京东云无线宝 RE-SS-01)在 IPQ60XX-LibWrt 工作流下会安装的软件包。

## 分析范围

- Workflow:`.github/workflows/IPQ60XX-LibWrt.yml`
- 源码:https://github.com/laipeng668/openwrt-6.x (LibWrt),分支 `25.12-nss`,内核 6.12
- 配置文件:`configs/IPQ60XX.config` + `configs/General.config`(由 `Build-OpenWrt.yml` 依次 cat 合并)
- 构建脚本:`scripts/Roc-script.sh`(替换/新增 feeds 包:aria2、nginx、ariang、ddns、frp、upnp、wol、argon、aurora、openlist、lucky、wechatpush、OpenAppFilter、gecoosac、athena-led、passwall、passwall2、openclash)
- 权威依据:发布产物 `IPQ60XX.manifest`(实际写入 rootfs 的包清单,456 项,随每次编译更新)

## 软件包来源(四层叠加)

1. **平台默认包**:`target/linux/qualcommax/Makefile` 的 `DEFAULT_PACKAGES`(全平台)
2. **设备默认包**:`target/linux/qualcommax/image/ipq60xx.mk` 中 `Device/jdcloud_re-ss-01` 的 `DEVICE_PACKAGES`
3. **config 显式包**:`IPQ60XX.config` + `General.config` 中 `=y` 的条目
4. **依赖自动带入**:上述包运行时依赖的库/内核模块

## jdcloud_re-ss-01 设备级差异

| 项目 | 内容 |
| --- | --- |
| 设备定义 | `$(call Device/FitImage)` + `$(call Device/EmmcImage)`,SOC `ipq6000`,emmc 固件 |
| DEVICE_PACKAGES | `ipq-wifi-jdcloud_re-ss-01`(无线固件/校准数据,本机型唯一设备级包) |
| 与同 config 其它机型差异 | 无 `ath11k-firmware-qcn9074`(re-cs-02 才有)、无 `luci-app-athena-led`(re-cs-02 通过 DEVICE_PACKAGES 单独安装) |

`CONFIG_TARGET_DEVICE_PACKAGES_..._jdcloud_re-ss-01` 在 `IPQ60XX.config` 中未定义,故设备包只有 `ipq-wifi-jdcloud_re-ss-01` 一项;其余全部来自平台默认包 + config 显式包。

## 实际安装包分类清单

> 以下按 `IPQ60XX.manifest` 实际内容分类;`kernel` 为 6.12.103。名称不含版本号。

### 内核与 NSS 平台驱动
- `kernel`
- NSS 驱动全家:kmod-qca-nss-drv 及其子模块(bridge-mgr、eogremgr、gre、igs、l2tpv2、lag-mgr、map-t、match、mirror、netlink、pppoe、pptp、qdisc、tun6rd、tunipip6、vlan-mgr、vxlanmgr、wifi-meshmgr)、kmod-qca-nss-dp、kmod-qca-nss-ecm、kmod-qca-nss-crypto、kmod-qca-ssdk、kmod-nss-ifb
- NSS 固件:nss-firmware、nss-firmware-ipq60xx、nss-eip-firmware(对应 `CONFIG_NSS_FIRMWARE_VERSION_11_4`)
- 其它:kmod-dsa、kmod-dsa-qca8k、kmod-phy-aquantia、kmod-phy-qca83xx、kmod-phylib-qcom、kmod-hwmon-core、kmod-thermal、kmod-bonding、kmod-macvlan

### 无线
- ath11k-firmware-ipq6018、kmod-ath11k、kmod-ath11k-ahb、kmod-ath11k-pci
- kmod-ath、kmod-cfg80211、kmod-mac80211、kmod-qcom-qmi-helpers、kmod-qrtr、kmod-qrtr-mhi、kmod-qrtr-smd
- hostapd-common、wpad-openssl、wifi-scripts、wireless-regdb、iw

### 网络与防火墙
- firewall4、nftables-json、kmod-nft-core/fib/fullcone/nat/offload/socket/tproxy、kmod-nf-* 全系
- dnsmasq-full、ppp、ppp-mod-pppoe、kmod-ppp/pppoe/pppol2tp/pppox、odhcp6c、odhcpd-ipv6only
- miniupnpd-nftables(UPnP)、kmod-nat46、kmod-gre/gre6、kmod-l2tp、kmod-pptp、kmod-vxlan
- ip-full、ethtool、6rd、resolveip

### 存储与文件系统
- e2fsprogs、f2fs-tools、f2fsck、mkf2fs、dosfstools、exfat-fsck、exfat-mkfs、ntfs3-mount
- kmod-fs-btrfs/exfat/ext4/f2fs/msdos/ntfs3/vfat、kmod-scsi-core、kmod-usb3、kmod-usb-dwc3、kmod-usb-dwc3-qcom、kmod-usb-storage/extras/uas、kmod-xhci-hcd
- fdisk、cfdisk、parted、lsblk、losetup、tune2fs、fstrim、zram-swap、kmod-zram、ubi-utils、uboot-envtools

### 基础系统
- fstools、procd、procd-seccomp、procd-ujail、ubus、ubusd、uci、ucode 及 ucode-mod-* 全系
- netifd、uhttpd、uhttpd-mod-ubus、rpcd 及 rpcd-mod-file/iwinfo/luci/rrdns/ucode
- dropbear、logd、urandom-seed、urngd、usign、openwrt-keyring、getrandom、mtd、fwtool、jshn、jsonfilter
- busybox(隐含)、default-settings、default-settings-chn、autocore、shellsync、zoneinfo-asia、zoneinfo-core

### LuCI 与主题
- luci、luci-light、luci-base、luci-compat、luci-lua-runtime
- luci-mod-admin-full/network/status/system、luci-lib-base/ip/jsonc/nixio/uqr
- luci-proto-ipv6/ppp、luci-app-firewall、luci-app-package-manager
- luci-theme-aurora(默认主题)、luci-theme-bootstrap
- 全部 luci-i18n-*-zh-cn 简体中文汉化(仅简体;zh-tw 已移除)

### 应用插件(config 显式启用)
- **UPnP/其它**:luci-app-upnp、luci-app-watchcat + watchcat、luci-app-autoreboot、luci-app-cpufreq + cpufreq、luci-app-diskman、luci-app-hd-idle + hd-idle、luci-app-3cat
- **DNS**:luci-app-smartdns + smartdns(UI/dashboard 不编译;刷机后可装官方源 smartdns-ui 预编译包,避免 rust 编译)
- **上网行为管理**:luci-app-oaf(OpenAppFilter,依赖 oaf 核心,源码来自 Roc-script.sh 克隆的 destan19/OpenAppFilter)

### 实用工具
- htop、nano-full、smartmontools、smartmontools-drivedb、hdparm、tcpdump + libpcap、iperf3(带宽测试)
- openssh-sftp-server、unzip、wget-ssl、curl、dmesg、dbus、libavahi-client、libavahi-dbus-support

## 不进入固件的包(注意)

- `IPQ60XX.config` 中 `luci-app-athena-led=m`、`luci-i18n-athena-led-zh-cn=m`:`m` 只编译不安装,re-ss-01 设备不含(只有 re-cs-02 通过 DEVICE_PACKAGES 安装)
- `General.config` 中 `=n` 的包均不安装:luci-theme-argon、luci-app-argon-config、luci-app-banip、luci-app-arpbind、luci-app-usb-printer、luci-app-sqm、luci-app-ttyd、luci-app-wechatpush、luci-app-lucky、luci-app-openclash、luci-app-passwall2、luci-app-wifischedule、luci-app-uhttpd、sqm-scripts-nss,以及本次移除的 **PassWall 全家(luci-app-passwall + xray-core、chinadns-ng、dns2socks、ipt2socks、microsocks、tcping、geoview、socat、3proxy 等依赖)、Samba4(luci-app-samba4 + samba4-server、samba4-libs、wsdd2)、frp(luci-app-frps/frpc + frps、frpc)、DDNS(luci-app-ddns + ddns-scripts、ddns-scripts-services、ddns-scripts-cloudflare)、vlmcsd、luci-app-acme + acme-acmesh-dnsapi、luci-app-wol、luci-proto-wireguard、tmux、coremark**
- 注意:`General.config` 中 `CONFIG_PACKAGE_btop=y`,但该版本 manifest 未见 btop,以对应 Release 的 manifest 为准
- 当前清单为精简后的状态:已移除 PassWall、Samba4、frp、DDNS、vlmcsd、acme、wol、wireguard、tmux、coremark、luci-app-uhttpd 及其依赖;旧 manifest 中的 xray-core、samba4-server、frpc、ddns-scripts 等项不再进入固件

## 备注

- 清单随 `25.12-nss` 分支滚动更新,任何一次编译的最终结果以该次 Release 的 `IPQ60XX.manifest` 为准
- 编译时 `.config` 由 `IPQ60XX.config` + `General.config` 合并生成(完整构建配置见发布产物 `IPQ60XX.config`)
