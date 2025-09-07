include $(TOPDIR)/rules.mk

PKG_NAME:=spotifyd-ng
PKG_VERSION:=0.4.1
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/Spotifyd/spotifyd.git
PKG_SOURCE_VERSION:=v0.4.1
PKG_MIRROR_HASH:=skip

# HAPUS rust/host dependency karena kita menggunakan prebuilt Rust
# PKG_BUILD_DEPENDS:=rust/host

include $(INCLUDE_DIR)/package.mk

# HAPUS include cargo.mk karena file tidak ada dan kita gunakan system cargo
# include $(INCLUDE_DIR)/cargo.mk

define Package/spotifyd-ng
  SECTION:=sound
  CATEGORY:=Sound
  TITLE:=Spotify daemon
  URL:=https://github.com/Spotifyd/spotifyd
  DEPENDS:=+alsa-lib +libstdcpp +libpthread
endef

define Package/spotifyd-ng/description
  A spotify playing daemon for OpenWrt.
endef

define Build/Configure
	# Apply librespot patch
	$(SED) '/^\[patch.crates-io\]/,/^$$/d' $(PKG_BUILD_DIR)/Cargo.toml
	echo '[patch.crates-io]' >> $(PKG_BUILD_DIR)/Cargo.toml
	echo 'librespot = { git = "https://github.com/librespot-org/librespot.git", rev = "be37402" }' >> $(PKG_BUILD_DIR)/Cargo.toml
endef

define Build/Compile
	# Gunakan system cargo langsung
	cd $(PKG_BUILD_DIR) && \
	CARGO_HOME="$(CARGO_HOME)" \
	CARGO_TARGET_DIR="$(PKG_BUILD_DIR)/target" \
	cargo build \
		--release \
		--no-default-features \
		--features alsa_backend \
		--target-dir="$(PKG_BUILD_DIR)/target"
endef

define Package/spotifyd-ng/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/target/release/spotifyd $(1)/usr/bin/
endef

$(eval $(call BuildPackage,spotifyd-ng))