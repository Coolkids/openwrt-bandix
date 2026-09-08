include $(TOPDIR)/rules.mk

PKG_NAME:=bandix
PKG_VERSION:=0.12.10
PKG_RELEASE:=1

PKG_LICENSE:=Apache-2.0
PKG_MAINTAINER:=timsaya

include $(INCLUDE_DIR)/package.mk

# 使用OpenWrt的Rust映射机制
include $(TOPDIR)/feeds/packages/lang/rust/rust-values.mk

# 二进制文件的文件名和URL
RUST_BANDIX_VERSION:=0.12.10
RUST_BINARY_FILENAME:=bandix-$(RUST_BANDIX_VERSION)-$(RUSTC_TARGET_ARCH).tar.gz


# 修正下载地址定义方式
PKG_SOURCE:=$(RUST_BINARY_FILENAME)
PKG_SOURCE_URL:=https://github.com/Coolkids/bandix/releases/download/v$(RUST_BANDIX_VERSION)

# 修正二进制文件校验值
ifeq ($(ARCH),aarch64)
	PKG_HASH:=c097fac85b9ad2ffe1cf0bffc42beca0a59b3628b479be5f5c2791f805317100
else ifeq ($(ARCH),arm) # update.sh skip this tag, do not delete this comment
	# Ref https://github.com/openwrt/packages/blob/master/lang/rust/rust-values.mk

	ARM_CPU_TYPE:=arm-armel
	ifeq ($(CONFIG_arm_v6)$(CONFIG_arm_v7),)
		ARM_CPU_TYPE:=$(subst arm-,armv5te-,$(ARM_CPU_TYPE))
	else ifeq ($(CONFIG_arm_v7),y)
		ARM_CPU_TYPE:=$(subst arm-,armv7-,$(ARM_CPU_TYPE))
	endif

	ifeq ($(CONFIG_HAS_FPU),y)
		ARM_CPU_TYPE:=$(subst -armel,-armhf,$(ARM_CPU_TYPE))
	endif

	ifeq ($(ARM_CPU_TYPE),arm-armel)
		PKG_HASH:=12ebd880d1e678f7a9fb595ccbbaa62140593fba314812c15027988511a93902
	else ifeq ($(ARM_CPU_TYPE),arm-armhf)
		PKG_HASH:=cc5adde57f0a07f6bd4a28ead00df044522084d718790cfcfc5964a3339cb7dd
	else ifeq ($(ARM_CPU_TYPE),armv5te-armel)
		PKG_HASH:=32838d51e89483addd7dc41c138af441a0997706265a9c8abc0ab71fa5f1bc6a
	else ifeq ($(ARM_CPU_TYPE),armv7-armel)
		PKG_HASH:=c6202cbd4f4a75acac2fc94195f1cdde31cf958e8e913cbad518a08a9a0556c1
	else ifeq ($(ARM_CPU_TYPE),armv7-armhf)
		PKG_HASH:=78c826211060e2791b31f9019aa42d392607f7bff70b1542a0348aae2522aa9f
	else
		PKG_HASH:=dummy
	endif
else ifeq ($(ARCH),mips)
	PKG_HASH:=1b0d864be2f56557091623980060aa242497f05021d22646eb97b52d9525bd03
else ifeq ($(ARCH),mipsel)
	PKG_HASH:=dde6c11d91097cac252de0be5efe7052864bea22e6dad81bc9f1680781df09c8
else ifeq ($(ARCH),powerpc64)
	PKG_HASH:=1a76919b0aec2e508eeed201de5920f2d3750ccf252be6c573ec58941ebea2a6
else ifeq ($(ARCH),riscv64)
	PKG_HASH:=93c82788140202761dd565d6a4c4a9782d04878fdbc1c2a00b3e2637146b3f57
else ifeq ($(ARCH),x86_64)
	PKG_HASH:=50662f1c4ae1ee61f8fe74b67a97a76a5ef9c8b22108221be04da6e730f5ca97
	PKG_SOURCE:=bandix-$(RUST_BANDIX_VERSION)-x86_64-unknown-linux-musl.tar.gz
else
	PKG_HASH:=dummy
endif

define Package/$(PKG_NAME)
	SECTION:=net
	CATEGORY:=Network
	TITLE:=Bandix - Network traffic monitoring tool
	DEPENDS:=+zoneinfo-all +conntrack +iw
endef

define Package/$(PKG_NAME)/description
	Bandix core service for network traffic monitoring
endef

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)/bin
	gzip -dc "$(DL_DIR)/$(PKG_SOURCE)" | tar -C $(PKG_BUILD_DIR)/bin --strip-components=1 -xf -
	$(Build/Patch)
endef

define Build/Compile
	# nothing to do
endef

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/bin/bandix $(1)/usr/bin/

	# 添加启动脚本和配置文件
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_BIN) ./files/bandix.init $(1)/etc/init.d/bandix

	# 安装配置文件
	$(INSTALL_DIR) $(1)/usr/share/bandix
	$(INSTALL_CONF) ./files/bandix.config $(1)/etc/config/bandix
	
	# 配置 sysupgrade 保留列表
	$(INSTALL_DIR) $(1)/lib/upgrade/keep.d
	$(INSTALL_DATA) ./files/bandix.keep $(1)/lib/upgrade/keep.d/bandix
endef

define Package/$(PKG_NAME)/conffiles
/etc/config/bandix
endef

$(eval $(call BuildPackage,bandix))
