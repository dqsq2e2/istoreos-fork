#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# G98 deliberately has its own U-Boot package.  The board has a verified
# U-Boot 2024.10 tree and must not inherit the rolling rk358x package.
# Verified source commit: f919c3a889f0ec7d63a48b5d0ed064386b0980bd.

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_VERSION:=2024.10
PKG_RELEASE:=8
PKG_HASH:=b28daf4ac17e43156363078bf510297584137f6df50fced9b12df34f61a92fb0

PKG_MAINTAINER:=BDY G98 maintainers

# These are the exact artifacts used by the hardware-verified v21 image.
G98_VERIFIED_IDBLOADER_SHA256:=aaa9f37a945e7e0d4c4f18eab68431e32c5a78ccbe49672f55c058d61e2f275d
G98_VERIFIED_ITB_SHA256:=ba0380db961498ce1311c2e84bb082151c5a66fa7ed82e669d94882dbc510033

UBOOT_USE_BINMAN:=1
UBOOT_USE_INTREE_DTC:=1

include $(INCLUDE_DIR)/u-boot.mk
include $(INCLUDE_DIR)/package.mk

define U-Boot/Default
  BUILD_TARGET:=rockchip
  UENV:=default
  HIDDEN:=1
endef

define U-Boot/rk3588-g98/Default
  BUILD_SUBTARGET:=armv8
  DEPENDS:=+PACKAGE_u-boot-$(1):rkbin-g98
  ATF:=rk3588_bl31_v1.45.elf
  TPL:=rk3588_ddr_lp4_2112MHz_lp5_2400MHz_v1.16.bin
endef

define U-Boot/bdy-g98-rk3588-2410
  $(U-Boot/rk3588-g98/Default)
  NAME:=BDY G98 (verified U-Boot 2024.10)
  UBOOT_CONFIG:=bdy-g98-rk3588
  BUILD_DEVICES:= \
    bdy_g98
endef

UBOOT_TARGETS:=bdy-g98-rk3588-2410

UBOOT_CONFIGURE_VARS += USE_PRIVATE_LIBGCC=yes

UBOOT_CUSTOMIZE_CONFIG := \
	--disable TOOLS_MKEFICAPSULE \
	--set-str MKIMAGE_DTC_PATH $(PKG_BUILD_DIR)/scripts/dtc/dtc

UBOOT_MAKE_FLAGS += \
  BL31=$(STAGING_DIR_IMAGE)/$(ATF) \
  $(if $(TPL),ROCKCHIP_TPL=$(STAGING_DIR_IMAGE)/$(TPL))

# The G98 boot chain is hardware-verified as two separate artifacts: the
# original iStoreOS loader and the v21 U-Boot FIT.  Keep the source tree and
# its patch chain in the package for auditability, but do not replace the
# verified FIT with a toolchain-dependent rebuild during an image build.
define Build/Configure/U-Boot
	true
endef

define Build/Compile/U-Boot
	true
endef

define Build/InstallDev
	$(INSTALL_DIR) $(STAGING_DIR_IMAGE)
	# The G98 board has only validated the original loader plus the v21 FIT.
	# Do not silently replace that loader with a newly generated combined image.
	$(MKHASH) sha256 $(TOPDIR)/package/boot/uboot-rockchip-g98/files/bdy-g98-istoreos-2024.10-idbloader.img | grep -q "$(G98_VERIFIED_IDBLOADER_SHA256)"
	$(MKHASH) sha256 $(TOPDIR)/package/boot/uboot-rockchip-g98/files/BDY-G98-u-boot-2024.10-full-v21.itb | grep -q "$(G98_VERIFIED_ITB_SHA256)"
	$(CP) $(TOPDIR)/package/boot/uboot-rockchip-g98/files/bdy-g98-istoreos-2024.10-idbloader.img \
		$(STAGING_DIR_IMAGE)/$(BUILD_VARIANT)-idbloader.img
	$(CP) $(TOPDIR)/package/boot/uboot-rockchip-g98/files/BDY-G98-u-boot-2024.10-full-v21.itb \
		$(STAGING_DIR_IMAGE)/$(BUILD_VARIANT)-u-boot.itb
	# A source-built combined image is intentionally not installed here.
endef

define Package/u-boot/install/default
endef

$(eval $(call BuildPackage/U-Boot))
