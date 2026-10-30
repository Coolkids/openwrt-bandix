include $(TOPDIR)/rules.mk

PKG_NAME:=bandix
PKG_VERSION:=0.12.12
PKG_RELEASE:=1

PKG_LICENSE:=Apache-2.0
PKG_MAINTAINER:=timsaya

include $(INCLUDE_DIR)/package.mk

# 使用OpenWrt的Rust映射机制
include $(TOPDIR)/feeds/packages/lang/rust/rust-values.mk

# 二进制文件的文件名和URL
RUST_BANDIX_VERSION:=0.12.12
RUST_BINARY_FILENAME:=bandix-$(RUST_BANDIX_VERSION)-$(RUSTC_TARGET_ARCH).tar.gz


# 修正下载地址定义方式
PKG_SOURCE:=$(RUST_BINARY_FILENAME)
PKG_SOURCE_URL:=https://github.com/Coolkids/bandix/releases/download/v$(RUST_BANDIX_VERSION)

# 修正二进制文件校验值
ifeq ($(ARCH),aarch64)
	PKG_HASH:=3bd23a2a892292d710c56f8a7c895662db04602ccba40ce7c5d32f66370d447b
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
		PKG_HASH:=a60e6207199d0d15bc3a43ec4c1e89f969090fc081e5b6b8bc83e6688b50687a
	else ifeq ($(ARM_CPU_TYPE),arm-armhf)
		PKG_HASH:=6d1d13ad87a1ccd0a3f34d08a95dee5500a8a3da126df58e6c698976e89b5753
	else ifeq ($(ARM_CPU_TYPE),armv5te-armel)
		PKG_HASH:=0e284c87002221754f7ba2894e58fe30a9ab6f67abaa1561ab06b359a5176545
	else ifeq ($(ARM_CPU_TYPE),armv7-armel)
		PKG_HASH:=f79ca41980ad639d4ddb408f0f3f5883a8431a0726631afb0cae4a1080cf06cd
	else ifeq ($(ARM_CPU_TYPE),armv7-armhf)
		PKG_HASH:=7063cafe87560786a1df75db29e0d7733db66bd528786185fdf91d34c3827539
	else
		PKG_HASH:=dummy
	endif
else ifeq ($(ARCH),mips)
	PKG_HASH:=f92f0339290d6922dd685e65a9ea28e4a6a8592c311b709fa4700b82cacccbeb
else ifeq ($(ARCH),mipsel)
	PKG_HASH:=c9328700c4f26a6b6c866211560f81bb19d446242650b8b4f6f1d75be413a93e
else ifeq ($(ARCH),powerpc64)
	PKG_HASH:=8f0010343ba4758168cdc8810a531a7aff50b676118eea4b250b25b747e1597c
else ifeq ($(ARCH),riscv64)
	PKG_HASH:=ae8db50ec0b254db073a4db103b10659a882394092739d71cf82748f12c24f2c
else ifeq ($(ARCH),x86_64)
	PKG_HASH:=51cb6e0543e2b360bd03bc05b94dbdc4160bbc8b7323885698af578d72972574
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
