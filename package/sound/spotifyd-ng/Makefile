include $(TOPDIR)/rules.mk

PKG_NAME:=spotifyd-ng
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/Spotifyd/spotifyd.git
PKG_SOURCE_VERSION:=HEAD
PKG_MIRROR_HASH:=skip

# === PIN librespot ke commit yg sudah mengandung fix (ganti jika perlu) ===
LIBRESPOT_GIT:=https://github.com/librespot-org/librespot.git
LIBRESPOT_COMMIT:=be37402

PKG_LICENSE:=GPL-3.0-or-later
PKG_MAINTAINER:=you <you@example.com>

PKG_BUILD_DEPENDS:=rust/host cargo/host

include $(INCLUDE_DIR)/package.mk
include $(TOPDIR)/feeds/packages/lang/rust/cargo.mk

# Fitur minimum yang umum di OpenWrt
CARGO_FEATURES += alsa_backend daemon
CARGO_BUILD_FLAGS += --bin spotifyd --locked --no-default-features --features "$(CARGO_FEATURES)"

define Package/spotifyd-ng
  SECTION:=sound
  CATEGORY:=Sound
  TITLE:=Spotify Connect daemon (spotifyd) pinned to patched librespot
  URL:=https://github.com/Spotifyd/spotifyd
  DEPENDS:=+libopenssl +libpthread +libstdcpp +alsa-lib
  PROVIDES:=spotifyd
  CONFLICTS:=spotifyd
endef

define Package/spotifyd-ng/description
spotifyd dengan dependensi librespot dipaksa ke commit yang telah memperbaiki
"track not available / 400 / 500" (perubahan API Spotify, Agustus 2025).
endef

# Sisipkan patch agar cargo pakai librespot dari git/commit tsb.
define Build/Prepare
	$(call Build/Prepare/Default)
	echo "" >> $(PKG_BUILD_DIR)/Cargo.toml
	echo "[patch.crates-io]" >> $(PKG_BUILD_DIR)/Cargo.toml
	echo "librespot = { git = \"$(LIBRESPOT_GIT)\", rev = \"$(LIBRESPOT_COMMIT)\" }" >> $(PKG_BUILD_DIR)/Cargo.toml
endef

define Package/spotifyd-ng/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(CARGO_BUILD_DIR)/spotifyd $(1)/usr/bin/spotifyd

ifneq ("$(wildcard ./files/spotifyd.init)","")
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/spotifyd.init $(1)/etc/init.d/spotifyd
endif
endef

$(eval $(call BuildPackage,spotifyd-ng))
