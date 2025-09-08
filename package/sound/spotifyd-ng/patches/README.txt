Patches for building spotifyd v0.4.1 against librespot 0.7.x
================================================================

Letakkan semua file *.patch ini ke:
  package/sound/spotifyd-ng/patches/

Daftar patch:
  0001-mixer-open-returns-Result.patch
  0002-softmixer-open-unwrap-result.patch
  0003-player-event-repeatchanged-rest.patch
  0004-remove-HomeThing-device-type.patch
  0005-connect-imports-minimal-refactor.patch
  0006-cargo-pin-librespot-0.7.1.patch

Catatan penting:
- Patch #0005 hanya memotong import lama (Spirc) agar kompilasi bisa lanjut,
  tetapi Anda tetap perlu menyesuaikan *inisialisasi Connect* (builder/entrypoint
  publik di librespot_connect 0.7.x) pada kode Anda.
- Patch #0006 mem-pin semua sub-crate librespot ke tag v0.7.1 via [patch.crates-io].
- Karena konteks kode Anda mungkin sedikit berbeda, jika ada hunk yang gagal
  saat apply, sesuaikan offset/konteksnya secara manual.

Workflow:
- Workflow OpenWrt SDK akan mengaplikasikan patch ini pada fase
  `make package/spotifyd-ng/prepare`, lalu melanjutkan kompilasi paket.

Good luck!
