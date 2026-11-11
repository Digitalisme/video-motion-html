# video-motion-html

Skill untuk asisten AI: membuat video motion graphic vertikal (1080×1920, 60 fps) sebagai satu berkas `index.html`, lengkap dengan naskah, storyboard, dan suara narasi gratis.

Skill ini satu berkas Markdown, [`video-motion-html/SKILL.md`](video-motion-html/SKILL.md). Isinya aturan kerja, templat HTML yang sudah teruji, dan daftar periksa. Bisa dipakai di Claude, ChatGPT, Gemini, dan alat lain yang bisa membaca berkas teks.

## Yang dihasilkan

Untuk tiap video, model membuat satu folder:

```
013-judul-singkat/
  01-style-brief.md    palet, font, gaya gerak
  02-naskah.md         teks narasi
  03-storyboard.md     isi layar per detik
  index.html           videonya: setiap frame dihitung dari waktu t
  tts.py               pembuat suara (Microsoft Edge TTS, gratis)
  audio/vo.mp3         narasi
```

`index.html` bisa langsung dibuka di browser: bersuara, punya seekbar, dan berputar ulang tanpa terasa putus (frame terakhir menyambung ke frame pertama).

Skill ini **tidak** membuat MP4. Untuk itu `index.html` perlu dirender oleh konverter yang membaca kontrak `window.OPENER` (dijelaskan di SKILL.md): program yang membuka halaman di Chromium, memanggil `seekFrame(t)` untuk tiap frame, memotretnya, lalu menyatukannya dengan ffmpeg.

## Yang dibutuhkan

| Kebutuhan | Untuk apa | Wajib? |
|---|---|---|
| Browser (Chrome, Edge, Firefox) | melihat preview | ya |
| Python 3 dan `pip install edge-tts` | suara narasi gratis | ya, bila video bernarasi |
| ffmpeg | mengatur jeda suara, mengukur waktu jeda | opsional |
| Konverter HTML ke MP4 | render akhir | ya, bila butuh MP4 |
| Kunci API ElevenLabs atau sejenisnya | suara berbayar | tidak |

## Dua cara memakai

**Agen dengan akses berkas** (Claude Code, Codex, Gemini CLI, Cursor, dan sejenisnya) bisa mengerjakan semuanya: menulis berkas, menjalankan `tts.py`, membuka preview, dan memeriksa hasilnya sendiri. Ini cara yang disarankan.

**Chat biasa** (claude.ai, ChatGPT, aplikasi Gemini) menghasilkan isi berkasnya, lalu Anda yang menyimpannya ke folder, menjalankan `tts.py`, dan menempel keluaran waktunya kembali ke chat supaya gambar disamakan dengan suara.

## Pemasangan

Menu aplikasi sering berpindah. Langkah di bawah diperiksa pada November 2026; bila tampilan Anda berbeda, ikuti tautan dokumentasi resmi di tiap bagian.

### Claude Code (terminal, aplikasi desktop, ekstensi IDE)

Salin folder `video-motion-html` ke salah satu lokasi ini:

| Lokasi | Berlaku untuk |
|---|---|
| `~/.claude/skills/video-motion-html/` | semua proyek Anda |
| `<proyek>/.claude/skills/video-motion-html/` | proyek itu saja |

```bash
git clone https://github.com/NAMA-ANDA/NAMA-REPO.git
```

```bash
cp -r NAMA-REPO/video-motion-html ~/.claude/skills/
```

Mulai sesi baru, lalu ketik `/video-motion-html` atau cukup minta dibuatkan video; Claude memuat skill ini sendiri bila permintaannya cocok.

### Claude (claude.ai dan aplikasi Claude)

1. Aktifkan **Code execution and file creation** di Settings > Capabilities.
2. Buat ZIP dari folder `video-motion-html`. Nama folder harus sama dengan nama skill, dan `SKILL.md` harus berada di dalam folder itu.
3. Buka **Customize > Skills**, klik **+**, pilih **Create skill**, lalu **Upload a skill**, dan unggah ZIP-nya.
4. Pastikan skill-nya menyala di daftar.

Di Windows, ZIP bisa dibuat dengan:

```powershell
Compress-Archive -Path video-motion-html -DestinationPath video-motion-html.zip
```

Dokumentasi resmi: [Using Skills in Claude](https://support.claude.com/en/articles/12512180-using-skills-in-claude).

Tanpa fitur Skills pun bisa: buat sebuah Project, unggah `SKILL.md` ke pengetahuan proyek, dan tulis di instruksi proyek "Untuk setiap permintaan video, ikuti SKILL.md."

### ChatGPT dan Codex

**Codex (CLI dan ekstensi IDE)** membaca skill dari folder `.agents/skills`:

| Lokasi | Berlaku untuk |
|---|---|
| `~/.agents/skills/video-motion-html/` | semua proyek Anda |
| `<repo>/.agents/skills/video-motion-html/` | repo itu saja |

**Aplikasi desktop ChatGPT** menampilkan skill di menu **Skills** pada bilah samping; ketik `@` di kolom chat untuk memilihnya.

**ChatGPT di web atau ponsel:** buat sebuah Project (atau GPT), unggah `SKILL.md` sebagai berkas, dan isi instruksinya dengan "Untuk setiap permintaan video, baca dan ikuti SKILL.md yang terlampir." Jangan menempel seluruh isi skill ke kolom instruksi; panjangnya melebihi batas kolom itu.

Dokumentasi resmi: [Build skills](https://learn.chatgpt.com/docs/build-skills), [Agent Skills di Codex](https://developers.openai.com/codex/skills).

### Gemini

**Gemini CLI** menemukan skill sendiri di folder ini:

| Lokasi | Berlaku untuk |
|---|---|
| `~/.gemini/skills/video-motion-html/` | semua proyek Anda |
| `<proyek>/.gemini/skills/video-motion-html/` | proyek itu saja |

Jalankan `/skills list` untuk memastikan skill terbaca. Gemini akan meminta izin sebelum mengaktifkannya.

Bila versi Gemini CLI Anda belum mendukung skill, impor berkasnya dari `GEMINI.md` di akar proyek:

```markdown
@./video-motion-html/SKILL.md
```

**Aplikasi Gemini (web dan ponsel):** buat sebuah Gem, unggah `SKILL.md` sebagai berkas pengetahuan, dan isi instruksinya dengan "Untuk setiap permintaan video, baca dan ikuti SKILL.md yang terlampir."

Dokumentasi resmi: [Agent Skills di Gemini CLI](https://geminicli.com/docs/cli/skills/).

### Alat lain (Cursor, GitHub Copilot, Windsurf, dan sejenisnya)

Kebanyakan agen pengodean membaca `AGENTS.md` di akar proyek. Salin folder `video-motion-html` ke proyek Anda, lalu tambahkan baris ini ke `AGENTS.md`:

```markdown
Untuk setiap permintaan membuat atau merevisi video motion graphic, baca dan ikuti `video-motion-html/SKILL.md` sebelum mulai.
```

Bila alat Anda punya berkas aturan sendiri (mis. `.cursor/rules/` atau `.github/copilot-instructions.md`), taruh baris yang sama di sana.

### Model apa pun, tanpa pemasangan

Lampirkan `SKILL.md` ke percakapan, lalu tulis:

```text
Baca SKILL.md terlampir dan ikuti seluruhnya. Lalu buatkan video 60 detik tentang cara mencuci tangan untuk anak usia 3 sampai 5 tahun.
```

Model yang jendela konteksnya kecil bisa kehilangan sebagian aturan; skill ini sekitar 29.000 karakter.

## Contoh permintaan

```text
Buatkan video 60 detik tentang cara menyeberang jalan untuk anak TK, dengan narasi suara perempuan.
```

```text
Buatkan opener 8 detik untuk kanal saya, tanpa narasi, warna utama biru tua dan kuning.
```

```text
Revisi panggung 3: bolanya masuk dari kiri, dan kamera mendekat saat kata "siap" diucapkan.
```

Model akan merangkum permintaan dan bertanya bila ada yang belum jelas, lalu bekerja berurutan: brief, naskah, storyboard, kode, suara. Naskah dan storyboard ditunjukkan dulu untuk Anda setujui sebelum kode ditulis.

## Melihat preview

Buka folder video lewat server lokal, bukan dengan klik ganda pada berkasnya:

```bash
python -m http.server 8000
```

Lalu buka `http://localhost:8000/index.html`.

| Alamat | Guna |
|---|---|
| `index.html` | preview dengan seekbar dan suara. Bila browser menahan suara, klik sekali di halaman. |
| `index.html?clean=1` | tampilan yang dilihat konverter: diam, tanpa seekbar |

## Menyesuaikan

`SKILL.md` ditulis untuk satu alur kerja tertentu. Sebelum dipakai, periksa bagian-bagian ini:

- **Aturan tetap:** larangan commit tanpa izin dan larangan render sebelum disetujui. Ubah bila alur Anda berbeda.
- **Visual yang dilarang:** daftar selera pembuatnya. Tambah atau kurangi sesuai kebutuhan.
- **Aturan seri:** paragraf terakhir merujuk seri milik pembuat skill. Hapus, atau ganti dengan aturan seri Anda (tokoh, warna, suara).
- **Suara:** bawaan `id-ID-GadisNeural` untuk bahasa Indonesia. Untuk bahasa lain, ganti nama suaranya; daftar suara bisa dilihat dengan `edge-tts --list-voices`.
- **Ukuran dan durasi:** bawaan 1080×1920. Untuk format mendatar, ganti `W` dan `H` di templat dan angka batas aman teks.

## Batasan

- Hasil bergantung pada modelnya. Model kecil sering melanggar aturan "frame = f(t)"; jalankan daftar periksa di akhir SKILL.md.
- Microsoft Edge TTS dan Google TTS adalah layanan daring gratis tanpa jaminan ketersediaan. Periksa syarat pemakaian masing-masing sebelum dipakai untuk karya komersial.
- Suara otomatis saat halaman dibuka diatur oleh browser, bukan oleh halaman; klik pertama selalu menyalakannya.
