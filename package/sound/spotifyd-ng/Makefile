include $(TOPDIR)/rules.mk

PKG_NAME:=spotifyd-ng
PKG_VERSION:=0.4.1
PKG_RELEASE:=1

PKG_MAINTAINER:=Your Name <your@email.com>
PKG_LICENSE:=GPL-2.0-or-later
PKG_LICENSE_FILES:=LICENSE

include $(INCLUDE_DIR)/package.mk

define Package/spotifyd-ng
  SECTION:=sound
  CATEGORY:=Sound
  TITLE:=Spotify daemon
  URL:=https://github.com/Spotifyd/spotifyd
  DEPENDS:=+alsa-lib +libstdcpp +libpthread
endef

define Package/spotifyd-ng/description
  A spotify playing daemon.
endef

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
	cp $(DL_DIR)/spotifyd-$(PKG_VERSION) $(PKG_BUILD_DIR)/ || true
	$(Build/Patch)
endef

define Build/Configure
	# No configuration needed
endef

define Build/Compile
	# Binary sudah di-build sebelumnya, tinggal copy
endef

define Package/spotifyd-ng/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(DL_DIR)/spotifyd-$(PKG_VERSION) $(1)/usr/bin/spotifyd
endef

$(eval $(call BuildPackage,spotifyd-ng))