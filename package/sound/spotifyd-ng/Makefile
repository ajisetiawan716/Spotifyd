include $(TOPDIR)/rules.mk

PKG_NAME:=spotifyd-ng
PKG_VERSION:=0.4.1
PKG_RELEASE:=1

# Pakai sumber git + commit penuh => stabil, tidak 404
PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/Spotifyd/spotifyd.git
# Commit dari tag v0.4.1 (full SHA):
PKG_SOURCE_VERSION:=8777c67988508d3623d3f6b81c9379fb071ac7dd
# biar nggak minta PKG_HASH untuk mirror tarball
PKG_MIRROR_HASH:=skip

# Subdir & nama source lokal (dl_github_archive.py akan mengemas tar.xz)
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz

CARGO_BIN_NAME:=spotifyd

# Toolchain Rust OpenWrt
include $(INCLUDE_DIR)/package.mk
# cargo.mk dari feeds/packages/lang/rust
include $(TOPDIR)/feeds/packages/lang/rust/cargo.mk

# Opsi build
RUSTC_OPT_FLAGS += -C debuginfo=0

define Package/spotifyd-ng
  SECTION:=sound
  CATEGORY:=Sound
  TITLE:=Spotify daemon (spotifyd) for OpenWrt
  URL:=https://github.com/Spotifyd/spotifyd
  DEPENDS:=+libstdcpp +libpthread +alsa-lib  # gunakan ALSA backend
endef

define Package/spotifyd-ng/description
 Spotifyd headless Spotify client (daemon).
endef

# (Opsional) pin librespot kalau kamu memang butuh workaround bug
# Kalau tidak perlu, hapus seluruh block Build/Configure di bawah.
define Build/Configure
  # Append patch section ke Cargo.toml untuk memaksa librespot ke commit tertentu:
  echo "" >> $(PKG_BUILD_DIR)/Cargo.toml
  echo "[patch.crates-io]" >> $(PKG_BUILD_DIR)/Cargo.toml
  # Commit be37402 contoh; ganti kalau kamu punya commit lain yang diperlukan
  echo 'librespot = { git = "https://github.com/librespot-org/librespot.git", rev = "be37402" }' >> $(PKG_BUILD_DIR)/Cargo.toml

  # Atur cargo target toolchain agar pakai linker/ar dari SDK
  mkdir -p $(PKG_BUILD_DIR)/.cargo
  ifeq ($(ARCH),x86_64)
    echo '[target.x86_64-unknown-linux-musl]' >  $(PKG_BUILD_DIR)/.cargo/config.toml
    echo 'linker = "x86_64-openwrt-linux-musl-gcc"' >> $(PKG_BUILD_DIR)/.cargo/config.toml
    echo 'ar = "x86_64-openwrt-linux-musl-gcc-ar"'  >> $(PKG_BUILD_DIR)/.cargo/config.toml
  else
    # aarch64 (Filogic) → cortex-a53 musl
    echo '[target.aarch64-unknown-linux-musl]' >  $(PKG_BUILD_DIR)/.cargo/config.toml
    echo 'linker = "aarch64-openwrt-linux-musl-gcc"' >> $(PKG_BUILD_DIR)/.cargo/config.toml
    echo 'ar = "aarch64-openwrt-linux-musl-gcc-ar"'  >> $(PKG_BUILD_DIR)/.cargo/config.toml
  endif
endef

# Aktifkan fitur ALSA (default di 0.4.1) – bisa diubah ke pulseaudio/rodio jika perlu
CARGO_ARGS += --no-default-features --features alsa_backend

define Build/Compile
	$(call Build/Compile/Cargo,release)
endef

define Package/spotifyd-ng/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/target/release/spotifyd $(1)/usr/bin/spotifyd
endef

$(eval $(call BuildPackage,spotifyd-ng))
