include $(TOPDIR)/rules.mk

PKG_NAME:=spotifyd-ng
PKG_VERSION:=0.4.1
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/Spotifyd/spotifyd.git
PKG_SOURCE_VERSION:=v0.4.1
PKG_MIRROR_HASH:=skip

PKG_BUILD_DEPENDS:=rust/host

include $(INCLUDE_DIR)/package.mk

# Manual check for Rust package - jangan bergantung pada CONFIG_PACKAGE_RUST variable
define CheckRust
ifeq ($(shell grep -c "CONFIG_PACKAGE_rust=y" $(TOPDIR)/.config),1)
$(info ✅ Rust package enabled in .config)
include $(TOPDIR)/feeds/packages/lang/rust/cargo.mk
else
$(info === RUST CONFIGURATION ERROR ===)
$(info Checking .config file directly...)
$(info Rust config in .config: $(shell grep "CONFIG_PACKAGE_rust" $(TOPDIR)/.config 2>/dev/null || echo "NOT FOUND"))
$(info Please ensure:)
$(info 1. Run: ./scripts/feeds install rust)
$(info 2. Enable in .config: echo "CONFIG_PACKAGE_rust=y" >> .config)
$(info 3. Run: make defconfig)
$(error Build cannot continue - Rust not properly configured)
endif
endef

$(eval $(call CheckRust))

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
	$(call Build/Compile/Cargo,release)
endef

define Package/spotifyd-ng/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/target/release/spotifyd $(1)/usr/bin/
endef

CARGO_ARGS += --no-default-features --features alsa_backend

$(eval $(call BuildPackage,spotifyd-ng))