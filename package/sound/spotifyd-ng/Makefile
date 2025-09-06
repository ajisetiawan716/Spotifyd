include $(TOPDIR)/rules.mk

PKG_NAME:=spotifyd-ng
PKG_RELEASE:=1

# --- Sumber: git langsung ---
PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/Spotifyd/spotifyd.git
PKG_SOURCE_VERSION:=HEAD
PKG_MIRROR_HASH:=skip

# (opsional) kalau mau nama dir build konsisten:
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_SOURCE_VERSION)
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_SOURCE_SUBDIR)

PKG_LICENSE:=GPL-3.0-or-later
PKG_MAINTAINER:=you <you@example.com>

# ❌ JANGAN build rust/host (biang timeout)
# PKG_BUILD_DEPENDS:=rust/host cargo/host

include $(INCLUDE_DIR)/package.mk

# Tentukan Rust target triple dari ARCH OpenWrt
ifeq ($(ARCH),x86_64)
  RUST_TARGET:=x86_64-unknown-linux-musl
else ifeq ($(ARCH),aarch64)
  RUST_TARGET:=aarch64-unknown-linux-musl
else
  $(error Unsupported ARCH $(ARCH) for spotifyd-ng. Add mapping to RUST_TARGET.)
endif

# Fitur spotifyd minimum
CARGO_FEATURES:=alsa_backend daemon

# (Opsional) pin **librespot** ke commit stabil (hapus jika tak perlu)
LIBRESPOT_GIT:=https://github.com/librespot-org/librespot.git
LIBRESPOT_COMMIT:=be37402

define Package/spotifyd-ng
  SECTION:=sound
  CATEGORY:=Sound
  TITLE:=Spotify Connect daemon (fast SDK build)
  URL:=https://github.com/Spotifyd/spotifyd
  DEPENDS:=+alsa-lib +libopenssl +libpthread +libstdcpp
  PROVIDES:=spotifyd
  CONFLICTS:=spotifyd
endef

define Package/spotifyd-ng/description
Spotifyd dibangun cepat via cargo (rustup host) dan toolchain OpenWrt SDK, tanpa rust/host.
endef

# Siapkan patch & konfigurasi linker cargo
define Build/Prepare
	$(call Build/Prepare/Default)

	# Patch librespot ke commit tertentu (opsional)
	echo "" >> $(PKG_BUILD_DIR)/Cargo.toml
	echo "[patch.crates-io]" >> $(PKG_BUILD_DIR)/Cargo.toml
	echo "librespot = { git = \"$(LIBRESPOT_GIT)\", rev = \"$(LIBRESPOT_COMMIT)\" }" >> $(PKG_BUILD_DIR)/Cargo.toml

	# Konfigurasi cargo agar pakai linker/ar dari SDK
	mkdir -p $(PKG_BUILD_DIR)/.cargo
	( \
	  echo "[target.$(RUST_TARGET)]"; \
	  echo "linker = \"$(TARGET_CC_NOCACHE)\""; \
	  echo "ar = \"$(TARGET_AR)\""; \
	) > $(PKG_BUILD_DIR)/.cargo/config.toml
endef

# Compile dengan cargo host (rustup), link ke sysroot SDK
define Build/Compile
	cd $(PKG_BUILD_DIR) && \
	PKG_CONFIG_ALLOW_CROSS=1 \
	PKG_CONFIG_PATH="$(STAGING_DIR)/usr/lib/pkgconfig:$(STAGING_DIR)/usr/share/pkgconfig" \
	OPENSSL_NO_VENDOR=1 \
	CC_$(RUST_TARGET)="$(TARGET_CC_NOCACHE)" \
	AR_$(RUST_TARGET)="$(TARGET_AR)" \
	RUSTFLAGS="-C debuginfo=0" \
	cargo build --release --target $(RUST_TARGET) --locked \
	  --no-default-features --features "$(CARGO_FEATURES)" --bin spotifyd
endef

define Package/spotifyd-ng/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/target/$(RUST_TARGET)/release/spotifyd $(1)/usr/bin/spotifyd

ifneq ("$(wildcard ./files/spotifyd.init)","")
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/spotifyd.init $(1)/etc/init.d/spotifyd
endif
endef

$(eval $(call BuildPackage,spotifyd-ng))
