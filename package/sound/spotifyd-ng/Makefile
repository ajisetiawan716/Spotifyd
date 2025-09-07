include $(TOPDIR)/rules.mk

PKG_NAME:=spotifyd-ng
PKG_VERSION:=0.4.1
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/Spotifyd/spotifyd.git
PKG_SOURCE_VERSION:=v0.4.1
PKG_MIRROR_HASH:=skip

PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz

CARGO_BIN_NAME:=spotifyd

include $(INCLUDE_DIR)/package.mk
include $(TOPDIR)/feeds/packages/lang/rust/cargo.mk

define Package/spotifyd-ng
SECTION:=sound
CATEGORY:=Sound
TITLE:=Spotify daemon (spotifyd) for OpenWrt
URL:=https://github.com/Spotifyd/spotifyd
DEPENDS:=+libstdcpp +libpthread +alsa-lib
endef

define Package/spotifyd-ng/description
Spotifyd headless Spotify client (daemon).
endef

# Tambah patch ke librespot agar menghindari bug playback
define Build/Configure
	# tambahkan patch ke Cargo.toml
	echo "" >> $(PKG_BUILD_DIR)/Cargo.toml
	echo "[patch.crates-io]" >> $(PKG_BUILD_DIR)/Cargo.toml
	echo 'librespot = { git = "https://github.com/librespot-org/librespot.git", rev = "be37402" }' >> $(PKG_BUILD_DIR)/Cargo.toml

	# set toolchain cargo target dari SDK
	mkdir -p $(PKG_BUILD_DIR)/.cargo
ifneq (,$(findstring x86_64,$(ARCH)))
	echo '[target.x86_64-unknown-linux-musl]' >  $(PKG_BUILD_DIR)/.cargo/config.toml
	echo 'linker = "x86_64-openwrt-linux-musl-gcc"' >> $(PKG_BUILD_DIR)/.cargo/config.toml
	echo 'ar = "x86_64-openwrt-linux-musl-gcc-ar"'  >> $(PKG_BUILD_DIR)/.cargo/config.toml
else
	echo '[target.aarch64-unknown-linux-musl]' >  $(PKG_BUILD_DIR)/.cargo/config.toml
	echo 'linker = "aarch64-openwrt-linux-musl-gcc"' >> $(PKG_BUILD_DIR)/.cargo/config.toml
	echo 'ar = "aarch64-openwrt-linux-musl-gcc-ar"'  >> $(PKG_BUILD_DIR)/.cargo/config.toml
endif
endef

# Build cuma backend ALSA
CARGO_ARGS += --no-default-features --features alsa_backend

define Build/Compile
	$(call Build/Compile/Cargo,release)
endef

define Package/spotifyd-ng/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/target/release/spotifyd $(1)/usr/bin/spotifyd
endef

$(eval $(call BuildPackage,spotifyd-ng))
