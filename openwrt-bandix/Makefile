include $(TOPDIR)/rules.mk

PKG_NAME:=bandix
PKG_VERSION:=0.12.11
PKG_RELEASE:=1

PKG_LICENSE:=Apache-2.0
PKG_MAINTAINER:=timsaya

include $(INCLUDE_DIR)/package.mk

# 使用OpenWrt的Rust映射机制
include $(TOPDIR)/feeds/packages/lang/rust/rust-values.mk

# 二进制文件的文件名和URL
RUST_BANDIX_VERSION:=0.12.11
RUST_BINARY_FILENAME:=bandix-$(RUST_BANDIX_VERSION)-$(RUSTC_TARGET_ARCH).tar.gz


# 修正下载地址定义方式
PKG_SOURCE:=$(RUST_BINARY_FILENAME)
PKG_SOURCE_URL:=https://github.com/Coolkids/bandix/releases/download/v$(RUST_BANDIX_VERSION)

# 修正二进制文件校验值
ifeq ($(ARCH),aarch64)
	PKG_HASH:=b744628ceed97acf74829a72d337903755bc274abac4be5e350e2c5f6280bfd3
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
		PKG_HASH:=019f7c804fa8963e3a92034cfad180e0c900ea9cfd2d42a513f2040d742cf4c1
	else ifeq ($(ARM_CPU_TYPE),arm-armhf)
		PKG_HASH:=3e0542b6f006b11b6017b80ac85a43ab57d81d4bf468fba2f83c057ef3cf28cc
	else ifeq ($(ARM_CPU_TYPE),armv5te-armel)
		PKG_HASH:=4249b3d6a61fe79d87ed46b1d054a76b4969880f4a21ece670463e98f9bb79c4
	else ifeq ($(ARM_CPU_TYPE),armv7-armel)
		PKG_HASH:=bd2c46265c660c40fb13e03caabe13ad42971146686d3dbfc379c039c3671650
	else ifeq ($(ARM_CPU_TYPE),armv7-armhf)
		PKG_HASH:=ee03691476f3782c7ba634a1e231e326c896fc8ed80d0ef8aca76b9f69e4b2ac
	else
		PKG_HASH:=dummy
	endif
else ifeq ($(ARCH),mips)
	PKG_HASH:=89165e01922318ca793bf67e8cdb2adcebbd388fdbc84fb8d425403728162dcb
else ifeq ($(ARCH),mipsel)
	PKG_HASH:=67503743282333ec2c7a73c07082fcc7c552917611e0ea85aede0978466bc4df
else ifeq ($(ARCH),powerpc64)
	PKG_HASH:=96241b7b59163e1395a132b7c556ce834c6e9fa1e19a3adc8e612b74e9778b00
else ifeq ($(ARCH),riscv64)
	PKG_HASH:=f9a5487b41cec42ec47c2af57b067589291c48e4bb58d4246cb726196f80e07b
else ifeq ($(ARCH),x86_64)
	PKG_HASH:=dd2d9240da48f37f578410986880f36eb6042f89632f81656f22e72761e4e730
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
