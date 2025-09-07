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
include $(INCLUDE_DIR)/cargo.mk

define Package/spotifyd-ng
  SECTION:=sound
  CATEGORY:=Sound
  TITLE:=Spotify daemon (spotifyd) for OpenWrt
  URL:=https://github.com/Spotifyd/spotifyd
  DEPENDS:=@(x86_64||aarch64) +libstdcpp +libpthread +alsa-lib
endef

define Package/spotifyd-ng/description
  Spotifyd headless Spotify client (daemon).
endef

# Gunakan Rust sistem bukan dari OpenWrt
define Build/Prepare
	$(Build/Prepare/Default)
	# Setup cargo config untuk cross-compilation
	mkdir -p $(PKG_BUILD_DIR)/.cargo
	echo '[target.x86_64-unknown-linux-musl]' > $(PKG_BUILD_DIR)/.cargo/config.toml
	echo 'linker = "x86_64-linux-musl-gcc"' >> $(PKG_BUILD_DIR)/.cargo/config.toml
	echo '[target.aarch64-unknown-linux-musl]' >> $(PKG_BUILD_DIR)/.cargo/config.toml
	echo 'linker = "aarch64-linux-musl-gcc"' >> $(PKG_BUILD_DIR)/.cargo/config.toml
endef

# Build hanya dengan backend ALSA
CARGO_ARGS += --no-default-features --features alsa_backend

define Build/Compile
	$(call Build/Compile/Cargo,---target $(if $(CONFIG_TARGET_x86_64),x86_64-unknown-linux-musl,aarch64-unknown-linux-musl))
endef

define Package/spotifyd-ng/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/target/$(if $(CONFIG_TARGET_x86_64),x86_64-unknown-linux-musl,aarch64-unknown-linux-musl)/release/spotifyd $(1)/usr/bin/
endef

$(eval $(call BuildPackage,spotifyd-ng))