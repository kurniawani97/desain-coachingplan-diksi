# Struktur Data — Coaching Plan Platform DIKSI
**Bank Indonesia · DSDM**
Dokumen ini mendeskripsikan seluruh entitas data yang digunakan dalam aplikasi Coaching Plan Platform, beserta field, tipe data, relasi antar tabel, dan nilai enum yang valid. Dokumen ini ditujukan sebagai acuan migrasi ke Power Apps / Dataverse / SharePoint.

---

## Daftar Tabel

| No | Nama Tabel | Keterangan |
|----|-----------|-----------|
| 1 | `Users` | Master data pengguna sistem |
| 2 | `Programs` | Master program coaching DSDM |
| 3 | `Batches` | Periode / angkatan program |
| 4 | `Mentors` | Pool mentor yang dikelola admin |
| 5 | `Participants` | Peserta per batch |
| 6 | `CoachingSessions` | Jadwal & hasil sesi 1-on-1 |
| 7 | `StageProgress` | Progress per tahap per peserta |
| 8 | `QnaThreads` | Thread pertanyaan peserta → admin |
| 9 | `QnaMessages` | Pesan dalam thread QNA |
| 10 | `Notifications` | Notifikasi per role |
| 11 | `AuditLog` | Log aktivitas sistem |

---

## 1. Tabel: `Users`

Master data pengguna yang dapat login ke sistem (sumber: Azure AD / BI HRIS).

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `id` | Text | ✓ | Primary key, format: `u001`, `u002`, dst |
| `nama` | Text | ✓ | Nama lengkap beserta gelar |
| `nip` | Text | ✓ | Nomor Induk Pegawai (18 digit) |
| `jabatan` | Text | ✓ | Jabatan struktural |
| `satker` | Text | ✓ | Nama satuan kerja lengkap |
| `short` | Text(2) | ✓ | Inisial 2 huruf untuk avatar |
| `roles` | Text (multi) | ✓ | Peran: `admin`, `mentor`, `peserta`, `board` |
| `programs` | Text (multi) | ✓ | Program yang dapat diakses: `diksi`, `mentoringKpp`, `coachingLm`, `groupSupport` |

**Nilai enum `roles`:**
| Nilai | Label | Keterangan |
|-------|-------|-----------|
| `admin` | Admin DSDM | Administrator Departemen SDM |
| `mentor` | Mentor / Coach | Mendampingi peserta 1-on-1 |
| `peserta` | Peserta | Peserta Program Coaching |
| `board` | Pimpinan | Executive / Board Viewer |

**Data Seed:**
| id | Nama | NIP | Jabatan | Satker | Role |
|----|------|-----|---------|--------|------|
| u001 | Dr. Ratna Kusumaningrum | 197504221999032001 | Kepala Divisi Pengembangan SDM | DSDM | admin |
| u002 | Yanuar Pradipta, M.Psi | 198209152010031002 | Deputi Direktur DKEM | DKEM | mentor |
| u003 | Dr. Hendra Wijaya | 196812041995031001 | Direktur DKMP | DKMP | mentor |
| u004 | Arif Budiman Pratama | 199203142018031001 | Manajer | DKEM | peserta |
| u005 | Ir. Soedarjono, MSc | 196403181988031001 | Deputi Gubernur BI | Dewan Gubernur | board |

---

## 2. Tabel: `Programs`

Master program coaching yang dikelola DSDM.

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `id` | Text | ✓ | Primary key: `diksi`, `mentoringKpp`, `coachingLm`, `groupSupport` |
| `nama` | Text | ✓ | Nama program |
| `kode` | Text | ✓ | Kode singkat program |
| `deskripsi` | Text | ✓ | Deskripsi singkat |
| `status` | Text | ✓ | `active` atau `coming_soon` |

**Data Seed:**
| id | Nama | Kode | Status |
|----|------|------|--------|
| diksi | DIKSI | DIKSI | active |
| mentoringKpp | Mentoring PCMP | PCMP | coming_soon |
| coachingLm | Coaching for LM | LM | coming_soon |
| groupSupport | Group Support | GS | coming_soon |

---

## 3. Tabel: `Batches`

Periode / angkatan program coaching. Satu program dapat memiliki banyak batch.

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `id` | Text | ✓ | Primary key, format: `B2026S1` |
| `kode` | Text | ✓ | Kode unik, format: `DIKSI-2026-01` |
| `nama` | Text | ✓ | Nama tampil batch |
| `programId` | Text | ✓ | FK → `Programs.id` |
| `tanggalMulai` | Date | ✓ | Tanggal mulai (YYYY-MM-DD) |
| `tanggalSelesai` | Date | ✓ | Tanggal selesai (YYYY-MM-DD) |
| `status` | Text | ✓ | `active`, `completed`, `draft` |
| `pesertaIds` | Text (multi) | — | FK → `Participants.id` (relasi 1:N) |

**Nilai enum `status`:**
| Nilai | Keterangan |
|-------|-----------|
| `draft` | Batch dibuat, belum aktif |
| `active` | Sedang berjalan |
| `completed` | Sudah selesai |

**Data Seed:**
| id | Kode | Nama | Mulai | Selesai | Status |
|----|------|------|-------|---------|--------|
| B2026S1 | DIKSI-2026-01 | DIKSI Batch 1 · 2026 | 2026-01-15 | 2026-06-30 | active |
| B2025S2 | DIKSI-2025-02 | DIKSI Batch 2 · 2025 | 2025-07-15 | 2025-12-15 | completed |
| B2025S1 | DIKSI-2025-01 | DIKSI Batch 1 · 2025 | 2025-01-15 | 2025-06-30 | completed |

---

## 4. Tabel: `Mentors`

Pool mentor yang dikelola oleh Admin DSDM.

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `id` | Text | ✓ | Primary key, format: `M001` |
| `nip` | Text | ✓ | NIP pegawai (18 digit) |
| `nama` | Text | ✓ | Nama lengkap |
| `jabatan` | Text | ✓ | Jabatan struktural |
| `satker` | Text | ✓ | Kode satker |
| `email` | Text | ✓ | Email BI (`@bi.go.id`) |
| `kompetensi` | Text | ✓ | Area keahlian (free text) |
| `tahunMenjabat` | Number | ✓ | Tahun mulai menjabat posisi saat ini |
| `aktif` | Boolean | ✓ | `true` = aktif sebagai mentor |
| `userId` | Text | — | FK → `Users.id` (nullable jika belum ada akun) |

**Data Seed:**
| id | Nama | Jabatan | Satker | Aktif |
|----|------|---------|--------|-------|
| M001 | Yanuar Pradipta, M.Psi | Deputi Direktur | DKEM | Ya |
| M002 | Dr. Hendra Wijaya | Direktur | DKMP | Ya |
| M003 | Bambang Suryanto, MBA | Deputi Direktur | DSPR | Ya |
| M004 | Lisa Anindita, MM | Asisten Direktur | DKOM | Ya |
| M005 | Ir. Rudi Hermawan, M.Sc | Deputi Direktur | DSta | Tidak |

---

## 5. Tabel: `Participants`

Peserta yang terdaftar pada suatu batch program.

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `id` | Text | ✓ | Primary key, format: `P001` |
| `nip` | Text | ✓ | NIP pegawai |
| `nama` | Text | ✓ | Nama lengkap |
| `satker` | Text | ✓ | Kode / nama satker |
| `tipeSatker` | Text | ✓ | `KP` (Kantor Pusat) atau `KPW` (Kantor Perwakilan) |
| `jobFamily` | Text | ✓ | Job family pegawai |
| `pangkat` | Text | ✓ | Pangkat / grade jabatan |
| `statusPegawai` | Text | ✓ | `KPP` atau `Non-KPP` |
| `statusNK` | Text | ✓ | `Upper` atau `Lower` |
| `smdTahun` | Number | ✓ | Sisa Masa Dinas dalam tahun |
| `batchId` | Text | ✓ | FK → `Batches.id` |
| `mentorUserId` | Text | — | FK → `Users.id` (mentor yang ditugaskan) |
| `konfirmasiHadir` | Text | ✓ | `pending`, `confirmed`, `declined` |
| `stage` | Number | ✓ | Tahap aktif saat ini (1–7) |
| `status` | Text | ✓ | `active`, `completed`, `dropout` |

**Nilai enum `jobFamily`:**
`Ekonom` · `Pengawas Bank` · `Sistem Pembayaran` · `Komunikasi` · `Hukum` · `Statistik` · `Manajemen Strategis`

**Nilai enum `satker` (pilihan umum):**
`DKEM` · `DKMP` · `DSPR` · `DKOM` · `DHK` · `DSDM` · `DKMK` · `DSTA` · `KPwBI [Kota]`

**Data Seed:**
| id | Nama | Satker | Pangkat | Stage | Status Konfirmasi |
|----|------|--------|---------|-------|-------------------|
| P001 | Arif Budiman Pratama | DKEM | Manajer | 3 | confirmed |
| P002 | Siti Rahmawati Dewi | DKMP | Manajer | 7 | confirmed |
| P003 | Bagus Kurniawan Saputra | KPwBI Lampung | Asisten Manajer | 2 | confirmed |
| P004 | Dewi Kusuma Ningrum | DSPR | Deputi Direktur | 3 | confirmed |
| P005 | Fajar Maulana Rizki | DKMP | Asisten Manajer | 1 | pending |
| P006 | Nadia Alya Salsabila | DKOM | Asisten Manajer | 1 | pending |

---

## 6. Tabel: `CoachingSessions`

Jadwal dan hasil sesi coaching 1-on-1 antara peserta dan mentor.

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `id` | Text | ✓ | Primary key, format: `CS001` |
| `participantId` | Text | ✓ | FK → `Participants.id` |
| `tanggal` | Date | ✓ | Tanggal sesi (YYYY-MM-DD) |
| `waktu` | Text | ✓ | Jam mulai (HH:MM) |
| `durasi` | Number | ✓ | Durasi dalam menit |
| `lokasi` | Text | ✓ | Deskripsi lokasi / link |
| `topik` | Text | ✓ | Topik / agenda sesi |
| `status` | Text | ✓ | Status sesi (lihat enum di bawah) |
| `proposedBy` | Text | ✓ | `mentor` atau `peserta` |
| `proposedAt` | DateTime | ✓ | Waktu sesi diusulkan |
| `acceptedAt` | DateTime | — | Waktu peserta menerima undangan |
| `completedAt` | DateTime | — | Waktu sesi dinyatakan selesai |
| `teamsLink` | Text | — | Link Microsoft Teams (jika online) |
| `notes` | Text | — | Catatan pengantar dari pengusul |

**Nilai enum `status`:**
| Nilai | Keterangan |
|-------|-----------|
| `proposed` | Diusulkan, menunggu konfirmasi peserta |
| `accepted` | Diterima peserta, belum dilaksanakan |
| `rejected` | Ditolak peserta |
| `completed` | Sesi sudah dilaksanakan |

---

## 7. Tabel: `StageProgress`

Rekam jejak progress setiap peserta per tahap program (1–7).

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `id` | Text | ✓ | Primary key (auto) |
| `participantId` | Text | ✓ | FK → `Participants.id` |
| `stageKey` | Text | ✓ | Kunci tahap (lihat enum di bawah) |
| `status` | Text | ✓ | `pending`, `in_progress`, `completed` |
| `completedAt` | Date | — | Tanggal diselesaikan |

**Nilai enum `stageKey` (7 tahap DSDM):**
| Key | Nama | Tipe | Aktor |
|-----|------|------|-------|
| `registrasi` | Registrasi | KUANTITATIF | Peserta |
| `sosialisasi` | Sosialisasi | KUANTITATIF | Peserta |
| `coaching` | Coaching | KUALITATIF | Peserta + Mentor |
| `monitoring1` | Monitoring 1 | KUALITATIF | TBD |
| `monitoring2` | Monitoring 2 | KUALITATIF | TBD |
| `monitoring3` | Monitoring 3 | KUANTITATIF | TBD |
| `evaluasi` | Evaluasi | 3-TIER | TBD |

### Sub-tabel: `StageData_Registrasi`
| Field | Tipe | Keterangan |
|-------|------|-----------|
| `participantId` | Text | FK → Participants |
| `registeredAt` | Date | Tanggal daftar |
| `checkInAt` | DateTime | Waktu check-in kegiatan |

### Sub-tabel: `StageData_Sosialisasi`
| Field | Tipe | Keterangan |
|-------|------|-----------|
| `participantId` | Text | FK → Participants |
| `pretestScore` | Number | Nilai pretest (0–100) |
| `posttestScore` | Number | Nilai posttest (0–100), nullable |
| `pretestVisible` | Boolean | Apakah nilai pretest ditampilkan ke peserta |
| `posttestVisible` | Boolean | Apakah nilai posttest ditampilkan ke peserta |
| `completedAt` | Date | Tanggal posttest selesai |

### Sub-tabel: `StageData_Coaching`
| Field | Tipe | Keterangan |
|-------|------|-----------|
| `participantId` | Text | FK → Participants |
| `sesi` | Number | Nomor sesi coaching aktif |
| `mentorStrengths` | Text | Evaluasi mentor: kekuatan peserta |
| `mentorDevelopment` | Text | Evaluasi mentor: area pengembangan |
| `mentorRecommendation` | Text | Rekomendasi mentor |
| `mentorEvaluatedAt` | DateTime | Waktu evaluasi mentor disubmit |
| `surveyMentorEffectiveness` | Number | Rating efektivitas mentor (1–10) |
| `surveySessionQuality` | Number | Rating kualitas sesi (1–10) |
| `surveyComment` | Text | Komentar peserta |
| `surveySubmittedAt` | DateTime | Waktu survey peserta disubmit |

### Sub-tabel: `StageData_Monitoring1`
| Field | Tipe | Keterangan |
|-------|------|-----------|
| `participantId` | Text | FK → Participants |
| `tindakLanjut` | Text | Tindak lanjut yang sudah dilakukan |
| `kendala` | Text | Kendala yang dihadapi |
| `dukungan` | Text | Dukungan yang dibutuhkan |
| `pelatihan` | Text | Pelatihan yang diikuti |
| `sertifikasi` | Text | Sertifikasi yang sedang/telah ditempuh |
| `kepanitiaan` | Text | Keterlibatan kepanitiaan |
| `lainnya` | Text | Catatan lain |
| `submittedAt` | Date | Tanggal submit |

### Sub-tabel: `StageData_Monitoring2`
| Field | Tipe | Keterangan |
|-------|------|-----------|
| `participantId` | Text | FK → Participants |
| `manfaat` | Number | Rating manfaat program (1–10) |
| `bertumbuh` | Number | Rating pertumbuhan diri (1–10) |
| `rekomendasi` | Number | Rating untuk direkomendasikan (1–10) |
| `saran` | Text | Saran perbaikan |
| `bersedia_fasilitator` | Text | `Ya` / `Tidak` |
| `bersedia_pembekalan` | Text | `Ya` / `Tidak` |
| `submittedAt` | Date | Tanggal submit |

### Sub-tabel: `StageData_Monitoring3`
| Field | Tipe | Keterangan |
|-------|------|-----------|
| `participantId` | Text | FK → Participants |
| `kategori_perubahan` | Text | `transformatif`, `signifikan`, `moderat`, `minimal` |
| `elaborasi_perubahan` | Text | Narasi perubahan diri |
| `kategori_pencapaian` | Text | `melampaui`, `sesuai`, `sebagian`, `belum` |
| `elaborasi_pencapaian` | Text | Narasi pencapaian |
| `kategori_pembelajaran` | Text | `sangat_mendalam`, `mendalam`, `cukup`, `kurang` |
| `elaborasi_pembelajaran` | Text | Narasi pembelajaran utama |
| `kategori_kedepan` | Text | `sangat_jelas`, `jelas`, `masih_meraba`, `belum_jelas` |
| `elaborasi_kedepan` | Text | Narasi rencana ke depan |
| `submittedAt` | Date | Tanggal submit |

### Sub-tabel: `StageData_Evaluasi`
| Field | Tipe | Keterangan |
|-------|------|-----------|
| `participantId` | Text | FK → Participants |
| `skor_program` | Number | Skor program (1–10) |
| `skor_mentor` | Number | Skor mentor (1–10) |
| `kebermanfaatan` | Number | Kebermanfaatan (1–10) |
| `feedback_program` | Text | Feedback narasi untuk program |
| `feedback_mentor` | Text | Feedback narasi untuk mentor |
| `rekomendasi_perbaikan` | Text | Saran perbaikan |
| `submittedAt` | Date | Tanggal submit |

---

## 8. Tabel: `QnaThreads`

Thread pertanyaan peserta kepada Admin DSDM.

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `id` | Text | ✓ | Primary key, format: `QT001` |
| `pesertaId` | Text | ✓ | FK → `Participants.id` |
| `subject` | Text | ✓ | Judul/topik pertanyaan |
| `createdAt` | Date | ✓ | Tanggal thread dibuat |
| `updatedAt` | DateTime | ✓ | Waktu pesan terakhir |
| `status` | Text | ✓ | `pending` atau `answered` |
| `adminUnread` | Boolean | ✓ | `true` jika admin belum membaca |

---

## 9. Tabel: `QnaMessages`

Pesan individual dalam satu thread QNA (bisa bolak-balik).

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `id` | Text | ✓ | Primary key, format: `m1` |
| `threadId` | Text | ✓ | FK → `QnaThreads.id` |
| `from` | Text | ✓ | `peserta` atau `admin` |
| `author` | Text | ✓ | Nama pengirim |
| `body` | Text | ✓ | Isi pesan |
| `at` | DateTime | ✓ | Waktu kirim (YYYY-MM-DD HH:MM) |

---

## 10. Tabel: `Notifications`

Notifikasi sistem per role pengguna.

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `id` | Text | ✓ | Primary key, format: `n1` |
| `targetRole` | Text | ✓ | Role penerima: `admin`, `mentor`, `peserta`, `board` |
| `type` | Text | ✓ | `info`, `success`, `warning`, `danger` |
| `title` | Text | ✓ | Judul notifikasi |
| `desc` | Text | — | Deskripsi singkat |
| `time` | Text | ✓ | Label waktu tampil (e.g., "2 menit lalu") |
| `read` | Boolean | ✓ | Status sudah dibaca |

---

## 11. Tabel: `AuditLog`

Rekam jejak aktivitas penting di sistem untuk keperluan audit.

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|-----------|
| `id` | Text | ✓ | Primary key |
| `time` | Text | ✓ | Jam aktivitas (HH:MM) |
| `date` | Text | ✓ | Label tanggal tampil |
| `actor` | Text | ✓ | Nama pelaku aksi |
| `action` | Text | ✓ | Deskripsi aksi |
| `target` | Text | — | Objek yang dikenai aksi (nullable) |

---

## Diagram Relasi Antar Tabel

```
Users ─────────────────┬──── Mentors (userId FK)
  │                    │
  │              Participants (mentorUserId FK)
  │                    │
  │                    ├──── Batches (batchId FK)
  │                    │
  │                    ├──── StageProgress (participantId FK)
  │                    │         └── StageData_* (participantId FK)
  │                    │
  │                    ├──── CoachingSessions (participantId FK)
  │                    │
  │                    └──── QnaThreads (pesertaId FK)
  │                               └── QnaMessages (threadId FK)
  │
  └──── Notifications (targetRole)
```

---

## Rekomendasi Implementasi di Power Apps

### Opsi 1: SharePoint Lists (paling mudah)
Buat satu SharePoint List per tabel di atas. Gunakan kolom **Lookup** untuk relasi antar list. Cocok untuk data di bawah 5.000 baris per list.

> Catatan: Field bertipe "multi-value" (seperti `roles` dan `programs` di tabel Users) perlu disimpan sebagai kolom **Choice (multi-select)** di SharePoint.

### Opsi 2: Dataverse (rekomendasi untuk skala enterprise)
Buat satu **Dataverse Table** per entitas. Gunakan relasi **One-to-Many** dan **Many-to-Many** native. Mendukung role-based security (row-level security) yang lebih granular.

### Sub-tabel `StageData_*`
Karena setiap tahap memiliki field yang berbeda-beda, ada dua pendekatan:
- **Opsi A** — Buat tabel terpisah per tahap (7 tabel, lebih bersih, mudah query)
- **Opsi B** — Satu tabel `StageData` dengan kolom JSON blob untuk field spesifik tahap (lebih ringkas, tapi sulit difilter di Power Apps)

**Rekomendasi: Opsi A** untuk kemudahan query dan form di Power Apps.

---

*Dokumen dibuat otomatis dari source code `src/app.tsx` — Coaching Plan Platform v2 · Bank Indonesia DSDM*
