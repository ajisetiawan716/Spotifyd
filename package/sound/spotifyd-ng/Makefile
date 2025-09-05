include $(TOPDIR)/rules.mk

PKG_NAME:=spotifyd-ng
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/Spotifyd/spotifyd.git
PKG_SOURCE_VERSION:=master
PKG_MIRROR_HASH:=skip

PKG_LICENSE:=GPL-3.0-or-later
PKG_MAINTAINER:=you <you@example.com>

# JANGAN build rust host dari feeds (biang timeout 6 jam)
# PKG_BUILD_DEPENDS:=rust/host cargo/host

include $(INCLUDE_DIR)/package.mk

# Pakai cargo/rustc dari runner (rustup), bukan dari staging_dir host
CARGO:=cargo
RUSTC:=rustc
include $(TOPDIR)/feeds/packages/lang/rust/cargo.mk

# Fitur minimum yang aman untuk ALSA + daemon
# (hapus/ubah kalau perlu)
CARGO_FEATURES += alsa_backend daemon
CARGO_BUILD_FLAGS += --bin spotifyd --locked --no-default-features --features "$(CARGO_FEATURES)"

# (Opsional) Pin/patch librespot biar stabil (ganti commit bila perlu)
LIBRESPOT_GIT:=https://github.com/librespot-org/librespot.git
LIBRESPOT_COMMIT:=be37402

define Package/spotifyd-ng
  SECTION:=sound
  CATEGORY:=Sound
  TITLE:=Spotify Connect daemon (patched)
  URL:=https://github.com/Spotifyd/spotifyd
  DEPENDS:=+alsa-lib +libopenssl +libpthread +libstdcpp
  PROVIDES:=spotifyd
  CONFLICTS:=spotifyd
endef

define Package/spotifyd-ng/description
Spotify Connect daemon (spotifyd) dibangun cepat via rustup host, link ke toolchain OpenWrt SDK.
endef

define Build/Prepare
	$(call Build/Prepare/Default)
	# Patch librespot ke commit tertentu (opsional)
	echo "" >> $(PKG_BUILD_DIR)/Cargo.toml
	echo "[patch.crates-io]" >> $(PKG_BUILD_DIR)/Cargo.toml
	echo "librespot = { git = \"$(LIBRESPOT_GIT)\", rev = \"$(LIBRESPOT_COMMIT)\" }" >> $(PKG_BUILD_DIR)/Cargo.toml
endef

# cargo.mk mendefinisikan:
#  - $(CARGO_BUILD_DIR) = $(PKG_BUILD_DIR)/target/$(RUSTC_TARGET)/release
# jadi binary-nya ada di $(CARGO_BUILD_DIR)/spotifyd

define Package/spotifyd-ng/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(CARGO_BUILD_DIR)/spotifyd $(1)/usr/bin/spotifyd

	# (opsional) init script kalau kamu punya
ifneq ("$(wildcard ./files/spotifyd.init)","")
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/spotifyd.init $(1)/etc/init.d/spotifyd
endif
endef

$(eval $(call BuildPackage,spotifyd-ng))
