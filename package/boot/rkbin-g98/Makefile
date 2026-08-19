# SPDX-License-Identifier: GPL-2.0-only

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk
include $(INCLUDE_DIR)/package.mk

# G98's verified U-Boot 2024.10 build uses these exact Rockchip blobs.
# Keep them isolated from the rolling rkbin package used by other boards.
PKG_NAME:=rkbin-g98
PKG_VERSION:=2024.10
PKG_RELEASE:=1

PKG_LICENSE:=Proprietary
PKG_MAINTAINER:=BDY G98 maintainers
PKG_FLAGS:=nonshared

RKbin_COMMIT:=a2a0b89b6c8c612dca5ed9ed8a68db8a07f68bc0
RKbin_URL:=https://raw.githubusercontent.com/rockchip-linux/rkbin/$(RKbin_COMMIT)/bin/rk35
BL31:=rk3588_bl31_v1.45.elf
TPL:=rk3588_ddr_lp4_2112MHz_lp5_2400MHz_v1.16.bin

define Download/g98-bl31
  FILE:=$(BL31)
  URL:=$(RKbin_URL)
  HASH:=8c404d9cf05591127542adec0eb92b066cf19d3c6aaffd097f7112e17e8b3044
endef
$(eval $(call Download,g98-bl31))

define Download/g98-tpl
  FILE:=$(TPL)
  URL:=$(RKbin_URL)
  HASH:=5aa6271195e422a2c30a8e801baa64e4a10cbc6a5f1963f9a29e6dc85096c7bf
endef
$(eval $(call Download,g98-tpl))

define Package/rkbin-g98
  SECTION:=boot
  CATEGORY:=Boot Loaders
  TITLE:=Verified RK3588 boot blobs for BDY G98
  DEPENDS:=@TARGET_rockchip
  HIDDEN:=1
endef

define Build/Prepare
	rm -rf $(PKG_BUILD_DIR)
	mkdir -p $(PKG_BUILD_DIR)/bin/rk35
	$(INSTALL_DATA) $(DL_DIR)/$(BL31) $(PKG_BUILD_DIR)/bin/rk35/
	$(INSTALL_DATA) $(DL_DIR)/$(TPL) $(PKG_BUILD_DIR)/bin/rk35/
endef

define Build/Compile
	true
endef

define Package/rkbin-g98/install
	$(INSTALL_DIR) $(STAGING_DIR_IMAGE)
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/bin/rk35/$(BL31) $(STAGING_DIR_IMAGE)/
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/bin/rk35/$(TPL) $(STAGING_DIR_IMAGE)/
endef

$(eval $(call BuildPackage,rkbin-g98))
