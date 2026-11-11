---
name: video-motion-html
description: Membuat video motion graphic vertikal (1080×1920, 60 fps) sebagai satu berkas HTML yang setiap frame-nya fungsi dari waktu t, lalu dirender ke MP4 lewat konverter HTML ke MP4. Pakai setiap kali diminta membuat, merevisi, atau menyiapkan render video motion graphic, explainer, atau opener di proyek ini.
---

# Video motion graphic dari HTML

Kamu membuat video sebagai halaman web: satu `index.html` yang menggambar frame mana pun bila diberi waktu `t` (detik). Konverter membuka halaman itu di Chromium, memanggil `seekFrame(t)` untuk tiap frame, memotret, lalu menyatukannya dengan audio menjadi MP4. Kamu tidak merender MP4 sendiri.

Hasil kerjamu adalah folder video berisi naskah, storyboard, `index.html`, dan audio. Semua teks untuk pengguna ditulis dalam bahasa Indonesia.

## Aturan tetap

- **Rangkum dulu, kerjakan kemudian.** Tulis ulang permintaan dalam dua atau tiga kalimat dan tunggu jelas sebelum mulai. Bila ada yang menentukan hasil dan belum disebut (topik, durasi, penonton, ada VO atau tidak), tanyakan.
- **Jangan render MP4 final** sebelum pengguna menyetujui preview.
- **Jangan `git commit` atau `git push`** tanpa izin eksplisit. Jangan membuat branch baru.
- Format akhir: **1080×1920, 60 fps, MP4 H.264 `yuv420p`**. Konverter sudah memakai nilai ini; tugasmu memastikan panggung HTML berukuran 1080×1920.

## Visual yang dilarang

- Hujan kode ala Matrix.
- Otak bercahaya atau otak neon sebagai simbol "AI" atau "pintar".
- Gerak mengambang ala screensaver: elemen melayang pelan tanpa tujuan, naik-turun sinus tanpa henti, partikel acak. Setiap gerakan harus punya alasan: masuk, keluar, menekankan, atau menghubungkan.

## Alur kerja (berurutan, satu tahap selesai dulu)

| No | Tahap | Hasil |
|----|-------|-------|
| 00 | Siapkan | folder video bernomor, mis. `013-judul-singkat/` |
| 01 | Aturan | baca berkas ini dan aturan seri bila ada; tulis `01-style-brief.md` |
| 02 | Naskah | `02-naskah.md`, lengkap, disetujui pengguna |
| 03 | Storyboard | `03-storyboard.md`, per detik |
| 04 | Kode | `index.html` |
| 05 | Suara | `audio/vo.mp3`, dibangkitkan sekali dari naskah final dengan TTS gratis |

Isi folder video:

```
013-judul-singkat/
  01-style-brief.md
  02-naskah.md
  03-storyboard.md
  index.html
  tts.py
  audio/vo.txt
  audio/vo.mp3
```

Jangan menulis kode sebelum naskah dan storyboard disetujui. Urutan praktis untuk 04 dan 05: tulis kode dengan waktu perkiraan dari storyboard, bangkitkan suara (lihat "Suara"), lalu ganti waktu perkiraan dengan waktu asli dari rekaman (lihat "Menyamakan gambar dengan suara").

### 01 Style brief

Satu halaman, berisi keputusan yang mengikat seluruh video:

- tema dan rasa (tiga kata sifat);
- palet: 1 warna utama, 1 latar, 1 aksen, 2–3 pendukung, semua dengan kode hex;
- font: satu keluarga, paling banyak dua ketebalan;
- benang merah: satu benda atau motif yang muncul di setiap panggung;
- tanda tangan gerak: satu pola gerak yang diulang (mis. benda melengkung masuk lalu memantul sekali);
- cara berganti panggung dan apa pemicunya (pintu, tirai, langkah tokoh), bukan fade tanpa sebab;
- satu momen istimewa di sekitar dua pertiga video.

### 02 Naskah

- Satu paragraf bernomor = satu panggung.
- Hitung durasi: suara narasi bahasa Indonesia yang santai berjalan sekitar 2 sampai 2,5 kata per detik, ditambah jeda antar paragraf (1–1,5 detik). Video 60 detik muat kira-kira 75 kata dengan 7 jeda.
- Kalimat pendek, satu gagasan per kalimat. Tulis seperti orang berbicara.
- Naskah berakhir dengan ajakan yang diminta pengguna (subscribe, like), dan durasinya sudah termasuk ajakan itu.

### 03 Storyboard

Satu tabel per panggung. Tiap baris mencakup 1–3 detik dan menjawab empat hal:

```markdown
## P2 Ruang makan (8–14), masuk dengan cut

| Detik | Di layar | Kamera | VO |
|---|---|---|---|
| 8–10 | Meja makan; Tata naik ke kursi; mangkuk beruap | lebar, zoom 1,25 | "Sarapan dulu" |
| 10–13 | Tata menyuap tiga kali, pipi menggembung | maju ke Tata, zoom 1,6 | "supaya kuat belajar" |
```

"Di layar" harus cukup rinci untuk digambar tanpa menebak: siapa, di mana, sedang apa, benda apa yang bergerak dan ke mana.

## Kontrak halaman (wajib, ini yang dibaca konverter)

```js
window.OPENER = {
  W: 1080, H: 1920,   // ukuran panggung dalam piksel
  DURATION: 60,       // panjang video dalam detik
  seekFrame(t) {},    // gambar frame pada detik t; boleh mengembalikan Promise
  ready: false,       // ubah jadi true setelah font dan aset siap
};
```

Cara konverter memakainya:

1. Membuka `index.html?clean=1` dengan jendela tepat 1080×1920.
2. Menunggu font (`document.fonts.ready`), lalu menunggu `OPENER.ready === true` (paling lama 10 detik).
3. Untuk frame ke-n memanggil `seekFrame(n / 60)`, menunggu satu frame browser, lalu memotret.
4. Suara MP4 hanya berasal dari berkas audio yang diunggah di form. Tag `<audio>` di HTML diabaikan; gunanya hanya untuk preview.

Akibatnya:

- Dengan `?clean=1` halaman **tidak boleh** memutar sendiri, tidak menampilkan seekbar, petunjuk, kursor, atau tombol apa pun.
- Latar di luar panggung tidak ikut terekam, karena jendela pas dengan panggung.
- Font dan skrip dari CDN diunduh saat render, jadi server perlu internet. Bila ragu, taruh berkas font di folder video dan unggah sebagai ZIP.

## Aturan kode: frame = f(t)

Fungsi `render(t)` harus menghasilkan gambar yang sama persis setiap kali dipanggil dengan `t` yang sama, tidak peduli frame mana yang digambar sebelumnya.

**Lakukan:**

- Buat semua elemen sekali saat halaman dimuat. `render(t)` hanya mengatur atribut (posisi, skala, opasitas, `display`).
- Atur **setiap** properti yang berubah di **setiap** frame, termasuk mengembalikannya ke nilai awal. Tulis `el.style.opacity = seg(t, 2, 2.4)`, bukan `if (t > 2) el.style.opacity = 1`.
- Turunkan semua nilai dari `t` lewat fungsi murni (`seg`, easing, `kf` di templat).
- Butuh "acak" (posisi daun, urutan bintang)? Pakai rumus dari indeks, mis. `(i * 37 % 11) / 11`.
- Gerak berulang (kedip, uap, daun) ditulis sebagai fungsi `t`: `(t % 3.5) < 0.13`.

**Jangan:**

- `Math.random()`, `Date.now()`, `performance.now()` di dalam `render(t)` atau fungsi yang dipanggilnya.
- Animasi CSS (`@keyframes`, `transition`) dan `<animate>` SVG: semuanya berjalan menurut jam dinding, bukan `t`, sehingga hasil render kacau.
- `setTimeout` atau `setInterval` untuk koreografi.
- Menambah nilai dari frame sebelumnya (`x += 2`), atau membuat dan menghapus elemen di dalam `render(t)`.
- `<video>` atau GIF di panggung.
- Pustaka animasi yang memutar sendiri. GSAP boleh hanya sebagai timeline yang di-`pause` dan digeser dengan `tl.time(t)`; templat di bawah tidak membutuhkannya.

Jam dinding hanya boleh dipakai di bagian "kontrol preview" untuk menentukan `t` yang sedang diputar.

## Templat `index.html`

Salin utuh, lalu ganti bagian 1 (tabel waktu), 3 (panggung), dan 4 (jadwal dan transisi loop). Bagian 2, 5, dan 6 jangan diubah kecuali perlu.

```html
<!doctype html>
<html lang="id">
<head>
<meta charset="utf-8">
<title>Judul video</title>
<style>
html,body{margin:0;height:100%;background:#17333F;overflow:hidden}
#stage{position:absolute;left:50%;top:50%;width:1080px;height:1920px;overflow:hidden;background:#FFF3D9}
#bar{position:fixed;left:50%;bottom:16px;transform:translateX(-50%);display:none;gap:12px;align-items:center;width:min(560px,calc(100vw - 56px));padding:10px 16px;border-radius:12px;background:rgba(14,16,22,.9);color:#fff;font:14px system-ui;z-index:9}
#bar input{flex:1;min-width:0}#bar button{width:64px;padding:6px 0;border:0;border-radius:8px;background:#fff;color:#111;font:inherit;cursor:pointer}
#bar span{min-width:11ch;text-align:right;font-variant-numeric:tabular-nums}
#hint{position:fixed;left:50%;top:16px;transform:translateX(-50%);display:none;padding:10px 18px;border-radius:999px;background:#FFC93C;color:#111;font:600 15px system-ui;z-index:9}
</style>
</head>
<body>
<div id="stage"><svg id="scene" width="1080" height="1920" viewBox="0 0 1080 1920" font-family="system-ui,sans-serif"></svg></div>
<div id="bar"><button id="playBtn" type="button">Jeda</button><input id="scrub" type="range" min="0" max="1000" value="0" aria-label="Posisi waktu"><span id="clock"></span></div>
<div id="hint">Klik di mana saja untuk memutar dengan suara</div>
<audio id="vo" src="audio/vo.mp3" preload="auto"></audio>
<script>
const W=1080,H=1920,NS='http://www.w3.org/2000/svg';
const $=s=>document.querySelector(s);

/* ---------- 1. TABEL WAKTU: satu-satunya tempat angka detik ditulis ---------- */
const B={halo:0.4,bola:1.4,p2:3.0,kata:3.4,END:6};
const DUR=B.END;

/* ---------- 2. ALAT WAKTU (fungsi murni) ---------- */
const cl=(v,a=0,b=1)=>Math.min(b,Math.max(a,v));
const seg=(t,a,b)=>cl((t-a)/(b-a));              // 0 sebelum a, 1 sesudah b
const lerp=(a,b,q)=>a+(b-a)*q;
const io=x=>x*x*(3-2*x);                          // pelan-cepat-pelan: kamera, perpindahan
const out3=x=>1-Math.pow(1-x,3);                  // cepat lalu mendarat: elemen masuk
const in2=x=>x*x;                                 // makin cepat: elemen keluar, jatuh
const back=x=>{const c=1.9;return 1+(c+1)*Math.pow(x-1,3)+c*Math.pow(x-1,2);}; // lewat sedikit lalu kembali
const bump=q=>q>0&&q<1?Math.sin(q*Math.PI):0;     // naik lalu turun: pantulan, penekanan
/* kf(t,[[detik,nilai],...]) : nilai di antara kunci waktu */
function kf(t,k,ease=io){if(t<=k[0][0])return k[0][1];for(let i=1;i<k.length;i++)if(t<=k[i][0])return lerp(k[i-1][1],k[i][1],ease((t-k[i-1][0])/(k[i][0]-k[i-1][0])));return k[k.length-1][1];}

/* ---------- 3. PANGGUNG: elemen dibuat sekali, draw(t) hanya mengatur atribut ---------- */
const S=$('#scene');
function N(tag,attrs={},parent=S){const n=document.createElementNS(NS,tag);for(const k in attrs)n.setAttribute(k,attrs[k]);parent.appendChild(n);return n;}
const scenes={};
/* build(g,cam) menggambar isi panggung ke dalam g dan mengembalikan draw(t).
   cam = titik yang dilihat kamera (x,y) dan zoom z; atur di dalam draw(t). */
function scene(name,build){const g=N('g');g.style.display='none';const cam={x:W/2,y:H/2,z:1};scenes[name]={g,cam,draw:build(g,cam)};}

scene('buka',(g,cam)=>{
  N('rect',{width:W,height:H,fill:'#FFF3D9'},g);
  N('rect',{y:1400,width:W,height:520,fill:'#F4D9A8'},g);
  const title=N('text',{'font-size':150,'font-weight':700,fill:'#2E4B6E','text-anchor':'middle'},g);title.textContent='Halo!';
  const shadow=N('ellipse',{cx:540,cy:1400,rx:90,ry:16,fill:'#2E4B6E'},g);
  const ball=N('circle',{r:90,fill:'#FF7A66'},g);
  return t=>{
    const a=seg(t,B.halo,B.halo+.5);                         // judul masuk, sedikit melewati ukuran akhir
    title.setAttribute('transform',`translate(540,620) scale(${back(a)})`);
    title.setAttribute('opacity',seg(t,B.halo,B.halo+.15));
    const fall=in2(seg(t,B.bola,B.bola+.45));                // bola jatuh makin cepat
    const hop=bump(seg(t,B.bola+.45,B.bola+.95));            // memantul sekali
    const squash=bump(seg(t,B.bola+.38,B.bola+.58));         // gepeng saat mendarat
    const y=lerp(-120,1310,fall)-170*hop;
    ball.setAttribute('transform',`translate(540,${y}) scale(${1+.22*squash},${1-.22*squash})`);
    shadow.setAttribute('opacity',.18*fall*(1-.6*hop));
    cam.y=kf(t,[[B.bola,960],[B.bola+1.2,1090]]);            // kamera mengikuti bola turun
    cam.z=kf(t,[[B.bola,1],[B.bola+1.2,1.18]]);
  };
});

scene('tutup',(g,cam)=>{
  N('rect',{width:W,height:H,fill:'#2E4B6E'},g);
  const bar=N('rect',{x:140,y:1010,height:14,rx:7,fill:'#FFC93C'},g);
  const letters=[...'Selesai'].map((ch,i)=>{const n=N('text',{'font-size':170,'font-weight':700,fill:'#FFF3D9','text-anchor':'middle'},g);n.textContent=ch;n._x=240+i*100;return n;});
  return t=>{
    letters.forEach((n,i)=>{const a=seg(t,B.kata+i*.07,B.kata+i*.07+.4);   // huruf masuk beruntun, jarak 0,07 dtk
      n.setAttribute('transform',`translate(${n._x},${lerp(1060,960,out3(a))})`);n.setAttribute('opacity',a);});
    bar.setAttribute('width',800*out3(seg(t,B.kata+.5,B.kata+1.1)));       // garis menegaskan kata
    cam.z=kf(t,[[B.p2,1.12],[B.p2+.6,1]],out3);                            // mendarat dari zoom setelah cut
  };
});

/* ---------- 4. JADWAL PANGGUNG: [detik mulai, nama] ---------- */
const CUTS=[[0,'buka'],[B.p2,'tutup']];
/* Loop mulus: pada LOOP detik terakhir, panggung pertama (dalam keadaan t=0) mendorong panggung terakhir
   ke atas. Di t=DUR layar sama persis dengan t=0, jadi pemutaran berulang tidak terasa putus. */
const LOOP=0.6;

/* ---------- 5. RENDER: frame = f(t) ---------- */
function place(sc,dy){const c=sc.cam;sc.g.setAttribute('transform',`translate(${W/2-c.x*c.z},${H/2-c.y*c.z+dy}) scale(${c.z})`);}
function render(t){
  let name=CUTS[0][1];for(const c of CUTS)if(t>=c[0])name=c[1];
  const sc=scenes[name],first=scenes[CUTS[0][1]];
  const q=first===sc?0:io(seg(t,DUR-LOOP,DUR));          // 0 = biasa, 1 = sudah kembali ke frame pertama
  for(const k in scenes)scenes[k].g.style.display=scenes[k]===sc||(q>0&&scenes[k]===first)?'block':'none';
  sc.draw(t);place(sc,-H*q);
  if(q>0){first.draw(0);place(first,H*(1-q));}
}

/* ---------- 6. KONTROL PREVIEW + KONTRAK KONVERTER (jam dinding hanya dipakai di sini) ---------- */
function fit(){const s=Math.min(innerWidth/W,innerHeight/H);$('#stage').style.transform=`translate(-50%,-50%) scale(${s})`;}
addEventListener('resize',fit);fit();
const clean=new URLSearchParams(location.search).has('clean'),vo=$('#vo');
let now=0,playing=false,t0=0,audioOn=false;
function seek(t){now=cl(t,0,DUR);render(now);if(!clean){$('#scrub').value=now/DUR*1000;$('#clock').textContent=now.toFixed(2)+' / '+DUR.toFixed(1)+' s';}}
function setPlaying(p){playing=p;t0=performance.now()-now*1000;if(!p)vo.pause();$('#playBtn').textContent=p?'Jeda':'Putar';}
function loop(ms){
  if(playing){
    let t=(ms-t0)/1000;if(t>=DUR){t=0;t0=ms;}          // sampai akhir: ulang dari awal
    seek(t);
    if(audioOn){                                        // suara mengikuti waktu gambar
      if(t<vo.duration-.1){if(Math.abs(vo.currentTime-t)>.15)vo.currentTime=t;if(vo.paused)vo.play().catch(()=>{});}
      else if(!vo.paused)vo.pause();
    }
  }
  requestAnimationFrame(loop);
}
window.OPENER={W,H,DURATION:DUR,ready:false,seekFrame(t){playing=false;seek(t);}};
document.fonts.ready.then(()=>{
  seek(0);window.OPENER.ready=true;
  if(clean)return;                                // mode render: diam, tanpa seekbar, menunggu seekFrame
  $('#bar').style.display='flex';setPlaying(true);requestAnimationFrame(loop);
  /* Langsung coba bersuara. Bila browser menolak karena belum ada klik, tampilkan petunjuk;
     klik atau tombol pertama mengulang dari awal dengan suara. */
  const startSound=()=>vo.play().then(()=>{audioOn=true;}).catch(e=>{
    if(e.name!=='NotAllowedError')return;         // berkas suara belum ada: preview tanpa suara
    $('#hint').style.display='block';
    const unlock=()=>{removeEventListener('pointerdown',unlock);removeEventListener('keydown',unlock);$('#hint').style.display='none';
      vo.play().then(()=>{audioOn=true;seek(0);setPlaying(true);}).catch(()=>{});};
    addEventListener('pointerdown',unlock);addEventListener('keydown',unlock);
  });
  /* Suara dimuat ke memori dulu: server lokal sederhana tidak mendukung lompat posisi pada berkas audio,
     sehingga tanpa ini suara kembali ke awal setiap kali seekbar digeser. */
  fetch(vo.getAttribute('src')).then(r=>r.ok?r.blob():Promise.reject()).then(b=>{vo.src=URL.createObjectURL(b);}).catch(()=>{}).then(startSound);
  $('#playBtn').addEventListener('click',()=>setPlaying(!playing));
  $('#scrub').addEventListener('input',e=>{seek(e.target.value/1000*DUR);t0=performance.now()-now*1000;});
  addEventListener('keydown',e=>{if(e.code==='Space'&&e.target===document.body){e.preventDefault();setPlaying(!playing);}});
});
</script>
</body>
</html>
```

Cara membuka:

| Alamat | Guna |
|--------|------|
| `index.html` | preview berulang dengan seekbar: tombol Putar/Jeda (atau spasi), penggeser waktu, penunjuk detik. Langsung bersuara bila browser mengizinkan; bila tidak, muncul petunjuk dan klik pertama mengulang dari awal dengan suara. |
| `index.html?clean=1` | yang dilihat konverter: diam di frame 0, tanpa seekbar |

Buka lewat server lokal (mis. `python -m http.server` atau `php -S 127.0.0.1:8000` di folder video), bukan dengan klik ganda, supaya font dan audio termuat seperti di konverter.

### Cara menambah isi

- **Panggung baru:** panggil `scene('nama', (g, cam) => { ...buat elemen...; return t => { ...atur atribut... }; })`, lalu daftarkan di `CUTS`.
- **Waktu:** jangan menulis angka detik di dalam `draw`. Tambah kunci di `B`, lalu rujuk (`B.bola + .45`). Saat suara direkam, cukup `B` yang diganti.
- **Elemen masuk:** `seg(t, mulai, mulai + 0.4)` dilewatkan ke `out3` atau `back`.
- **Elemen keluar:** `in2`, lebih singkat dari masuknya (0,2–0,3 detik).
- **Kamera:** atur `cam.x`, `cam.y`, `cam.z` dengan `kf`. Kamera selalu punya tujuan: mengikuti tokoh, mendekat ke benda yang sedang dibicarakan, atau mundur untuk memperlihatkan hasil.
- **Pergantian panggung:** cut langsung lewat `CUTS`. Bila perlu geser, gerakkan `cam.x` panggung lama keluar pada 0,25 detik terakhirnya dan panggung baru masuk pada 0,4 detik pertamanya.
- **Gambar:** bentuk SVG datar (rect, circle, ellipse, path) lebih ringan dan tajam daripada gambar bitmap. Kelompokkan bagian yang berputar bersama dalam `<g>` dan putar lewat `transform`, dengan titik putar di titik asal grup.
- **HTML/CSS biasa** boleh menggantikan SVG untuk teks dan kotak, asalkan gayanya diatur dari `render(t)` dan tanpa `transition`.

## Loop mulus: frame terakhir menyambung ke frame pertama

Video pendek diputar berulang otomatis, jadi akhir video harus mengalir kembali ke awalnya. Aturannya satu: **gambar pada `t = DURATION` harus sama persis dengan gambar pada `t = 0`.** Frame terakhir yang direkam adalah `DURATION − 1/60`, sehingga frame sesudahnya (frame 0 pada putaran berikut) terasa sebagai lanjutan gerak yang sama.

Templat sudah mengerjakannya: pada `LOOP` detik terakhir, panggung pertama dalam keadaan `t = 0` masuk dari bawah dan mendorong panggung terakhir ke atas. Yang perlu kamu jaga:

- **Frame 0 adalah keadaan diam panggung pertama:** latar dan benda tetapnya sudah ada, elemen yang "masuk" baru mulai bergerak sesudah `t = 0` (di templat: judul masuk pada `B.halo = 0,4`). Jangan memulai video dengan layar kosong atau dengan gerak yang sudah setengah jalan.
- **Panggung terakhir sudah tenang** sebelum `DURATION − LOOP`: semua gerak dan teks penutup selesai dan sempat terbaca.
- **Suara selesai sebelum transisi loop.** Isi `END` = akhir suara + `LOOP` + sedikitnya 0,3 detik, supaya kalimat terakhir tidak tertimpa perpindahan.
- **Tulis transisi ini di storyboard** sebagai baris terakhir, mis. "59,4–60: panggung pembuka mendorong masuk dari bawah (loop)".
- **Ganti bentuk transisinya bila brief meminta**, asalkan aturan `t = DURATION` sama dengan `t = 0` tetap terpenuhi. Pilihan yang lebih bercerita: tokoh berjalan kembali ke posisi awal, kamera mundur ke bingkai pembuka, atau benda benang merah menutup layar lalu membukanya ke panggung pertama. Ubah `place(...)` di `render` atau gambar sendiri di panggung terakhir dengan memanggil keadaan `t = 0` panggung pertama.
- **Video satu panggung** tidak memakai dorongan ini (templat mematikannya sendiri). Di sana setiap nilai harus kembali ke nilai awalnya pada `t = DURATION`, dan gerak berulang memakai periode yang membagi habis durasi, mis. `Math.sin(2 * Math.PI * t / DUR * 4)`.

## Supaya terasa video, bukan slide

- Satu gagasan per panggung. Elemen muncul saat disebut, tidak semuanya sekaligus.
- Gerakan mendarat tepat pada kata yang diucapkan: mulai 0,15–0,3 detik sebelum kata itu, selesai saat kata terdengar.
- Masuk 0,3–0,6 detik dengan `out3` atau `back`; jangan linear. Benda berat lebih lambat dari benda ringan.
- Beri ancang-ancang dan akibat: jongkok sebelum lompat, gepeng saat mendarat, benda di dekatnya ikut bergetar sekali.
- Elemen sejenis masuk beruntun dengan jarak 0,06–0,12 detik, bukan bersamaan.
- Setelah sebuah gerakan selesai, tahan diam cukup lama untuk dibaca (teks: sekitar 0,3 detik per kata, minimal 1 detik).
- Ganti sudut pandang atau jarak kamera tiap 2–4 detik, selalu dengan alasan dari cerita.
- Teks di layar: paling kecil 56 px, kontras tinggi dengan latar. Jauhkan isi penting dari tepi: 220 px dari atas, 340 px dari bawah, 80 px dari kiri dan kanan, karena aplikasi video pendek menutupi area itu dengan tombol dan keterangan.

## Suara (voice over)

Pakai TTS gratis sebagai jalur utama. Layanan berbayar hanya dipakai bila pengguna memintanya dan sudah punya kuncinya; jangan menyarankan berlangganan.

| Pilihan | Biaya | Suara Indonesia | Stempel waktu | Kapan dipakai |
|---|---|---|---|---|
| **Microsoft Edge TTS** (`edge-tts`) | gratis, tanpa kunci | `id-ID-GadisNeural` (perempuan), `id-ID-ArdiNeural` (laki-laki) | ada, per kata | bawaan |
| **Google TTS** (`gTTS`) | gratis, tanpa kunci | satu suara (`id`) | tidak ada | cadangan bila Edge TTS tidak bisa dijangkau |
| ElevenLabs dan sejenisnya | berbayar, perlu kunci API | banyak, lebih ekspresif | ada | opsional, atas permintaan pengguna |

Keduanya butuh Python dan internet. Simpan teks yang akan dibacakan (hanya isi naskah, tanpa nomor, tabel, atau catatan) ke `audio/vo.txt`.

### Microsoft Edge TTS (bawaan)

```bash
pip install edge-tts
```

Simpan skrip ini sebagai `tts.py` di folder video. Ia menghasilkan `audio/vo.mp3` dan mencetak detik mulai tiap kata:

```python
"""Suara gratis (Microsoft Edge TTS) dari audio/vo.txt -> audio/vo.mp3 + audio/vo.words.json.
Pakai:  python tts.py [suara] [kecepatan]   mis.  python tts.py id-ID-GadisNeural -10%
"""
import asyncio, json, sys
import edge_tts

voice = sys.argv[1] if len(sys.argv) > 1 else "id-ID-GadisNeural"
rate = sys.argv[2] if len(sys.argv) > 2 else "+0%"

async def main():
    text = " ".join(open("audio/vo.txt", encoding="utf-8").read().split())
    words = []
    with open("audio/vo.mp3", "wb") as mp3:
        async for c in edge_tts.Communicate(text, voice, rate=rate, boundary="WordBoundary").stream():
            if c["type"] == "audio":
                mp3.write(c["data"])
            elif c["type"] == "WordBoundary":  # offset dalam satuan 100 nanodetik
                words.append({"kata": c["text"], "mulai": round(c["offset"] / 1e7, 2)})
    json.dump(words, open("audio/vo.words.json", "w", encoding="utf-8"), ensure_ascii=False, indent=1)
    for w in words:
        print(f'{w["mulai"]:7.2f}  {w["kata"]}')

asyncio.run(main())
```

```bash
python tts.py id-ID-GadisNeural -10%
```

Keluarannya seperti ini; angka di kiri langsung dipakai untuk tabel `B`:

```
   0.10  Selamat
   0.78  pagi
   2.42  Matahari
```

- Kecepatan: `-10%` sampai `-20%` untuk penonton anak, `+0%` untuk umum.
- Jeda antar kalimat sekitar 1 detik dan tidak bisa diatur dari teks. Untuk jeda lebih panjang, sisipkan hening sesudahnya (lihat di bawah).
- Tanpa skrip pun bisa, dengan stempel waktu per kalimat di berkas `.srt`:
  ```bash
  edge-tts -f audio/vo.txt -v id-ID-GadisNeural --rate=-10% --write-media audio/vo.mp3 --write-subtitles audio/vo.srt
  ```

### Google TTS (cadangan)

```bash
pip install gTTS
```

```bash
gtts-cli -f audio/vo.txt -l id -o audio/vo.mp3
```

gTTS tidak memberi stempel waktu dan hanya punya satu suara. Ambil detik mulai tiap frasa dari jeda di rekamannya; tiap baris `silence_end` adalah saat sebuah frasa mulai diucapkan:

```bash
ffmpeg -hide_banner -i audio/vo.mp3 -af silencedetect=noise=-35dB:d=0.25 -f null - 2>&1 | grep silence_end
```

Cara ini juga berlaku untuk rekaman suara sendiri.

### Berbayar (opsional)

Bila pengguna meminta ElevenLabs atau layanan lain, pakai endpoint yang mengembalikan stempel waktu, minta bahasa Indonesia secara eksplisit, dan baca kunci API dari berkas lingkungan (mis. `.env.local`). Jangan menulis kunci ke dalam kode, naskah, atau jawaban. Langkah sesudahnya sama.

## Menyamakan gambar dengan suara

1. Bangkitkan suara **sekali** dari naskah final, simpan sebagai `audio/vo.mp3`. Jangan membangkitkan per kalimat lalu menyambungnya: nada antar kalimat jadi tidak nyambung.
2. **Lekatkan suara ke `index.html`.** Pastikan baris ini ada dan `src`-nya menunjuk ke berkas yang baru dibuat, dengan jalur relatif terhadap `index.html`:
   ```html
   <audio id="vo" src="audio/vo.mp3" preload="auto"></audio>
   ```
   Sejak itu membuka `index.html` harus menghasilkan suara yang seirama dengan gambar, dan menggeser seekbar ikut menggeser suara. Browser hanya mengizinkan suara otomatis setelah ada interaksi; templat menangani ini dengan petunjuk "Klik di mana saja untuk memutar dengan suara". Jangan menanam audio sebagai base64 di dalam HTML: berkasnya membengkak lebih dari 1 MB dan tidak bisa lagi dibaca atau disunting dengan nyaman.
3. Ambil detik mulai tiap frasa penting dari keluaran `tts.py`, dari `.srt`, atau dari `silencedetect`.
4. Tulis angka itu ke tabel `B`, satu kunci per frasa (`s1a`, `s1b`, … atau nama yang bermakna), lalu `END` = akhir suara + `LOOP` + sedikitnya 0,3 detik (lihat "Loop mulus").
5. Putar `index.html` dengan suara dan periksa dengan seekbar bahwa tiap gerakan jatuh pada katanya.

Bila suara lebih pendek dari durasi target, sisipkan hening di antara paragraf pada rekaman yang sama (ffmpeg atau penyunting audio). Hening sepanjang `d` detik yang disisipkan pada detik `p` menggeser semua stempel waktu sesudah `p` sebesar `d`; perbarui tabel `B` dengan hitungan itu atau ukur ulang dengan `silencedetect`. Jangan membangkitkan ulang hanya untuk mengubah panjang.

## Periksa sebelum menyerahkan

1. **Tidak ada galat** di konsol browser pada `index.html` dan `index.html?clean=1`. Satu-satunya yang boleh: 404 untuk `audio/vo.mp3` selama suara belum direkam.
2. **Kontrak ada:** di konsol, `OPENER.W`, `OPENER.H`, `OPENER.DURATION` benar dan `OPENER.ready` bernilai `true`.
3. **Deterministik.** Jalankan di konsol; hasilnya harus `true`:
   ```js
   const snap=t=>{OPENER.seekFrame(t);return [...document.querySelectorAll('#scene > g')].find(g=>g.style.display!=='none').outerHTML};
   const a=snap(12.5);snap(40);snap(3);console.log(a===snap(12.5));
   ```
   Perintah ini membandingkan panggung yang sedang tampil saja; panggung tersembunyi wajar menyimpan sisa frame terakhirnya. Ulangi untuk dua atau tiga nilai `t` lain, termasuk tepat di pergantian panggung. Bila `false`, ada properti yang tidak diatur ulang di setiap frame.
4. **Suara terpasang:** membuka `index.html` lalu klik sekali menghasilkan suara dari awal; di tab Network `audio/vo.mp3` berstatus 200, bukan 404.
5. **Mode bersih diam:** `?clean=1` tidak bergerak, tidak bersuara, dan tidak menampilkan seekbar atau petunjuk.
6. **Tiap baris storyboard** dicek dengan seekbar: yang tertulis memang terlihat pada detiknya, tidak ada elemen terpotong tepi atau bertumpuk.
7. **Loop mulus:** geser seekbar ke ujung kanan lalu ke ujung kiri; gambar tidak boleh berubah. Biarkan preview berputar melewati akhir dua kali; tidak boleh ada lompatan. Di konsol, perintah ini harus mencetak `true`:
   ```js
   const first=document.querySelector('#scene > g');
   OPENER.seekFrame(0);const a=first.outerHTML;OPENER.seekFrame(OPENER.DURATION);console.log(a===first.outerHTML);
   ```
   Frame 0 juga harus layak jadi gambar diam: panggung pembuka sudah tampil, bukan layar kosong.
8. **Larangan visual** dan aturan kode di atas dipatuhi; cari `Math.random`, `Date.now`, `transition`, `@keyframes`, `setTimeout` di berkas dan pastikan tidak ada di jalur `render`.

Laporkan hasil pemeriksaan apa adanya, termasuk yang belum dicek.

## Render ke MP4 (setelah disetujui)

1. Buka konverter HTML ke MP4.
2. Unggah `index.html`. Bila halaman memakai berkas lokal (font, gambar, modul JS), unggah folder video sebagai `.zip`; `index.html` yang paling dekat ke akar ZIP dipakai.
3. Unggah `audio/vo.mp3` di kolom audio. Tanpa ini MP4 tidak bersuara.
4. Biarkan 60 fps dan durasi kosong (diambil dari `OPENER.DURATION`).
5. Video 60 detik berarti 3.600 frame dan butuh beberapa menit. Satu render berjalan dalam satu waktu; unggahan lain antre.

Pesan gagal yang umum:

| Pesan | Sebab |
|-------|-------|
| Halaman tidak menyediakan `window.OPENER` dengan `seekFrame(t)` | galat JavaScript sebelum `OPENER` dibuat, atau kontrak tidak dipasang |
| Durasi tidak diketahui | `OPENER.DURATION` kosong atau 0 |
| Font berbeda dari preview | server tidak bisa mengunduh font dari CDN; sertakan berkas font di ZIP |
| Gerak patah atau melompat di MP4 | ada animasi CSS, `setTimeout`, atau nilai yang bergantung pada frame sebelumnya |
