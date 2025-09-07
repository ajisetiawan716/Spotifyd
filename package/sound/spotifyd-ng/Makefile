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

# Check if Rust is available with better error handling
ifeq ($(CONFIG_PACKAGE_RUST),y)
include $(TOPDIR)/feeds/packages/lang/rust/cargo.mk
else
$(info === RUST PACKAGE CONFIGURATION ERROR ===)
$(info Current CONFIG_PACKAGE_RUST value: $(CONFIG_PACKAGE_RUST))
$(info Please enable Rust package in OpenWrt configuration:)
$(info 1. Run 'make menuconfig' in SDK directory)
$(info 2. Navigate to 'Languages' -> 'Rust')
$(info 3. Select 'Rust' and save configuration)
$(info 4. Run 'make defconfig' to update configuration)
$(error Build cannot continue without Rust package)
endif

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

# Build only with ALSA backend
CARGO_ARGS += --no-default-features --features alsa_backend

$(eval $(call BuildPackage,spotifyd-ng))