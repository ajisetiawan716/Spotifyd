# Ganti fungsi CheckRust dengan yang lebih reliable
define CheckRust
# Tunggu hingga environment SDK sepenuhnya ter-setup
ifeq ($(BUILD_VARIANT),final)
ifeq ($(shell test -f $(STAGING_DIR)/host/bin/rustc && echo 1 || echo 0),1)
$(info ✅ Rust compiler found in staging directory)
include $(INCLUDE_DIR)/cargo.mk
else
$(info === RUST COMPILER MISSING ===)
$(info Checking for rustc: $(shell which rustc 2>/dev/null || echo "not found"))
$(info STAGING_DIR: $(STAGING_DIR))
$(error Rust compiler not found. Please ensure Rust is properly installed in SDK)
endif
endif
endef

# Pindahkan eval setelah semua variabel terdefinisi
# $(eval $(call CheckRust))  # JANGAN eval di sini

# ...

# Eval di akhir setelah semua setup
define Package/spotifyd-ng/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/target/release/spotifyd $(1)/usr/bin/
endef

CARGO_ARGS += --no-default-features --features alsa_backend

# Eval CheckRust di sini, setelah semua variabel terdefinisi
BUILD_VARIANT:=final
$(eval $(call CheckRust))
$(eval $(call BuildPackage,spotifyd-ng))