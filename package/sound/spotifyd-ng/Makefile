include $(TOPDIR)/rules.mk

PKG_NAME:=spotifyd-ng
PKG_RELEASE:=1

# Pin ke commit stabil, JANGAN HEAD biar reproducible
PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/Spotifyd/spotifyd.git
PKG_SOURCE_VERSION:=0a9fe5a1e9a2e9ee0f2c51f0d1f0b5b2a5b0a8b6  # ganti jika perlu
PKG_MIRROR_HASH:=skip

PKG_LICENSE:=GPL-3.0-only
PKG_MAINTAINER:=you <you@example.com>

include $(INCLUDE_DIR)/package.mk

define Package/spotifyd-ng
  SECTION:=sound
  CATEGORY:=Sound
  TITLE:=Spotify daemon (librespot-based)
  URL:=https://github.com/Spotifyd/spotifyd
  DEPENDS:=+alsa-lib +libopenssl
endef

define Package/spotifyd-ng/description
A lightweight Spotify Connect daemon (compiled with ALSA backend).
endef

# Deteksi triple target musl untuk cargo
# (OpenWrt SDK x86_64 dan aarch64/filogic yang kita target di workflow)
ifeq ($(CONFIG_x86_64),y)
  RUST_TARGET:=x86_64-unknown-linux-musl
  OPENWRT_GCC:=x86_64-openwrt-linux-musl-gcc
  OPENWRT_AR:=x86_64-openwrt-linux-musl-gcc-ar
else ifeq ($(CONFIG_aarch64),y)
  RUST_TARGET:=aarch64-unknown-linux-musl
  OPENWRT_GCC:=aarch64-openwrt-linux-musl-gcc
  OPENWRT_AR:=aarch64-openwrt-linux-musl-gcc-ar
else
  $(error Unsupported arch — add mapping in package Makefile)
endif

# Direktori build cargo
CARGO_BUILD_DIR:=$(PKG_BUILD_DIR)/target/$(RUST_TARGET)/release
CARGO_BIN:=$(CARGO_BUILD_DIR)/spotifyd

# === optional: patch ke commit librespot tertentu jika perlu ===
define Build/Prepare
	$(call Build/Prepare/Default)
	# tulis config cargo supaya pakai toolchain SDK
	mkdir -p $(PKG_BUILD_DIR)/.cargo
	( \
	  echo '[target.$(RUST_TARGET)]'; \
	  echo 'linker = "$(OPENWRT_GCC)"'; \
	  echo 'ar = "$(OPENWRT_AR)"'; \
	) > $(PKG_BUILD_DIR)/.cargo/config.toml

	# Patch crate librespot jika kamu memang perlu pin/patch (opsional)
	# echo '' >> $(PKG_BUILD_DIR)/Cargo.toml
	# echo '[patch.crates-io]' >> $(PKG_BUILD_DIR)/Cargo.toml
	# echo 'librespot = { git = "https://github.com/librespot-org/librespot.git", rev = "be37402" }' >> $(PKG_BUILD_DIR)/Cargo.toml
endef

# Build via cargo langsung, tanpa cargo.mk dari feeds
define Build/Compile
	( \
	  cd $(PKG_BUILD_DIR); \
	  export PKG_CONFIG_ALLOW_CROSS=1; \
	  export OPENSSL_NO_VENDOR=1; \
	  export OPENSSL_DIR="$(STAGING_DIR)/usr"; \
	  export OPENSSL_LIB_DIR="$(STAGING_DIR)/usr/lib"; \
	  export OPENSSL_INCLUDE_DIR="$(STAGING_DIR)/usr/include"; \
	  export RUSTFLAGS="-C debuginfo=0"; \
	  # Fitur default = ALSA backend (lihat Cargo.toml upstream)
	  cargo build --release --locked --target $(RUST_TARGET); \
	)
	# pastikan biner ada
	[ -x "$(CARGO_BIN)" ] || { echo "spotifyd binary missing"; exit 1; }
endef

define Package/spotifyd-ng/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(CARGO_BIN) $(1)/usr/bin/spotifyd
	# contoh initscript sederhana (opsional):
	# $(INSTALL_DIR) $(1)/etc/init.d
	# $(INSTALL_BIN) ./files/spotifyd.init $(1)/etc/init.d/spotifyd
endef

$(eval $(call BuildPackage,spotifyd-ng))
