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
	# Apply librespot patch dengan approach yang lebih aman
	if grep -q "\[patch.crates-io\]" $(PKG_BUILD_DIR)/Cargo.toml; then \
		$(SED) '/^\[patch.crates-io\]/,/^$$/d' $(PKG_BUILD_DIR)/Cargo.toml; \
	fi
	echo '[patch.crates-io]' >> $(PKG_BUILD_DIR)/Cargo.toml
	echo 'librespot = { git = "https://github.com/librespot-org/librespot.git", rev = "be37402" }' >> $(PKG_BUILD_DIR)/Cargo.toml
endef

define Build/Compile
	# Gunakan system cargo langsung dengan static linking untuk musl
	cd $(PKG_BUILD_DIR) && \
	CARGO_HOME="$(CARGO_HOME)" \
	RUSTFLAGS="-C target-feature=+crt-static -C linker=$(CC) -C ar=$(AR)" \
	CARGO_TARGET_DIR="$(PKG_BUILD_DIR)/target" \
	CC="$(CC)" \
	AR="$(AR)" \
	PKG_CONFIG_ALLOW_CROSS=1 \
	PKG_CONFIG_PATH="$(STAGING_DIR)/usr/lib/pkgconfig" \
	PKG_CONFIG_SYSROOT_DIR="$(STAGING_DIR)" \
	PKG_CONFIG_LIBDIR="$(STAGING_DIR)/usr/lib/pkgconfig" \
	ALSA_LIB_DIR="$(STAGING_DIR)/usr/lib" \
	ALSA_INCLUDE_DIR="$(STAGING_DIR)/usr/include" \
	cargo build \
		--release \
		--no-default-features \
		--features alsa_backend \
		--target $(CARGO_TARGET) \
		--config target.$(CARGO_TARGET).linker='$(CC)' \
		--config target.$(CARGO_TARGET).ar='$(AR)'
endef

define Package/spotifyd-ng/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/target/$(CARGO_TARGET)/release/spotifyd $(1)/usr/bin/
endef

$(eval $(call BuildPackage,spotifyd-ng))