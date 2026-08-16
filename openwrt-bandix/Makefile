include $(TOPDIR)/rules.mk

PKG_NAME:=bandix
PKG_VERSION:=0.12.8
PKG_RELEASE:=1

PKG_LICENSE:=Apache-2.0
PKG_MAINTAINER:=timsaya

include $(INCLUDE_DIR)/package.mk

# 使用OpenWrt的Rust映射机制
include $(TOPDIR)/feeds/packages/lang/rust/rust-values.mk

# 二进制文件的文件名和URL
RUST_BANDIX_VERSION:=0.12.8
RUST_BINARY_FILENAME:=bandix-$(RUST_BANDIX_VERSION)-$(RUSTC_TARGET_ARCH).tar.gz


# 修正下载地址定义方式
PKG_SOURCE:=$(RUST_BINARY_FILENAME)
PKG_SOURCE_URL:=https://github.com/timsaya/bandix/releases/download/v$(RUST_BANDIX_VERSION)

# 修正二进制文件校验值
ifeq ($(ARCH),aarch64)
	PKG_HASH:=b6b24d8ad233296ae5250cb10cd8d1eaf27e6b239a09b8946b3da50c055ab623
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
		PKG_HASH:=5e8a55fae269bf48d75c315ca13847e8b8e0367f292de3aad2403607bd88e4a3
	else ifeq ($(ARM_CPU_TYPE),arm-armhf)
		PKG_HASH:=7cc83ae7d0e318504b10ced6d2e1a178a42d0300420772f688baebcec206a3ef
	else ifeq ($(ARM_CPU_TYPE),armv5te-armel)
		PKG_HASH:=9bff38818c2eb97fce790762d08ac6a24ca0c900d281c1a67ce02982134c602d
	else ifeq ($(ARM_CPU_TYPE),armv7-armel)
		PKG_HASH:=f0ec83835203ff874b1bbb7219ba78f02665910983a102dac446e51a8c039dac
	else ifeq ($(ARM_CPU_TYPE),armv7-armhf)
		PKG_HASH:=9098104612f2be22d35d2e5fe6f89b19fd4a186cef0f6a8d762f98a4d3723e3a
	else
		PKG_HASH:=dummy
	endif
else ifeq ($(ARCH),mips)
	PKG_HASH:=8069f71c059c245466087d4bde1f1193a6c77bc75ee145cfbf4c57cefea8d9c6
else ifeq ($(ARCH),mipsel)
	PKG_HASH:=97daaf6bae3315c560c7e84f773b2e07d60bf8f8ea99196977a04f003095095c
else ifeq ($(ARCH),powerpc64)
	PKG_HASH:=452584cb80d0cb019a12a7efd0d733861dae446c7cca2c36a9e72299c2e6e992
else ifeq ($(ARCH),riscv64)
	PKG_HASH:=17988e9e928763dfe60a43ddf9dd338d53635d333129c84d44dc627803d8f0df
else ifeq ($(ARCH),x86_64)
	PKG_HASH:=
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
