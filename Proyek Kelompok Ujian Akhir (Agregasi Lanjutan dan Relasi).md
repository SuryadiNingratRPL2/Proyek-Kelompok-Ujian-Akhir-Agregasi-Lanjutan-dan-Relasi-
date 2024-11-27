# soal 1
### **1. Struktur Database**

#### **Tujuan**

Database yang dirancang terdiri dari empat tabel utama yang saling berhubungan untuk mendukung sistem jadwal pelajaran. Keempat tabel tersebut adalah:

- **Tabel `kelas`**:  
    Menyimpan informasi tentang tingkat kelas dan nama kelas.
- **Tabel `mata_pelajaran`**:  
    Berisi data mengenai mata pelajaran beserta deskripsinya.
- **Tabel `guru`**:  
    Menyimpan data pribadi guru seperti NIP, nama, email, nomor telepon, dan alamat.
- **Tabel `jadwal`**:  
    Merupakan tabel penghubung yang berisi informasi jadwal pelajaran dan menghubungkan data guru serta mata pelajaran.

#### **Kelebihan**

- Struktur database ini mengikuti prinsip normalisasi untuk meminimalisir redundansi data.
- Menggunakan _foreign key_ untuk menjaga hubungan antar tabel dan memastikan data yang konsisten.
- Penggunaan constraint pada domain data, seperti email yang harus unik, membantu menghindari entri duplikat.

#### **Kelemahan**

- Jika ada perubahan besar pada kurikulum atau pembagian kelas, struktur tabel `jadwal` mungkin perlu disesuaikan lagi.

---

### **2. Tabel `kelas`**

#### **Struktur**

```sql
CREATE TABLE kelas (
    id_kelas INT AUTO_INCREMENT PRIMARY KEY,
    tingkat VARCHAR(10) NOT NULL,
    nama_kelas VARCHAR(50) NOT NULL
);
```

#### **Gambar**
![](asset/1.PNG)

#### **Analisis**

- **Kolom `id_kelas`** digunakan sebagai primary key untuk memastikan setiap kelas memiliki identifikasi yang unik.
- **Kolom `tingkat` dan `nama_kelas`** berfungsi untuk mencatat informasi jenjang pendidikan, misalnya kelas 10, 11, dan 12.

#### **Potensi Perluasan**

- Bisa ditambahkan atribut lain, seperti kapasitas kelas atau nama wali kelas.

---

### **3. Tabel `mata_pelajaran`**

#### **Struktur**

```sql
CREATE TABLE mata_pelajaran (
    id_mapel INT AUTO_INCREMENT PRIMARY KEY,
    nama_mapel VARCHAR(100) NOT NULL,
    deskripsi TEXT
);
```

#### **Gambar**
![](asset/2.PNG)
#### **Analisis**

- **Kolom `id_mapel`** berfungsi sebagai primary key yang memberikan identifikasi unik untuk setiap mata pelajaran.
- **Kolom `deskripsi`** memberikan ruang untuk informasi tambahan mengenai mata pelajaran tersebut.

#### **Potensi Perluasan**

- Bisa ditambahkan atribut seperti jumlah SKS atau kategori mata pelajaran (misalnya wajib atau pilihan).

---

### **4. Tabel `guru`**

#### **Struktur**

```sql
CREATE TABLE guru (
    nip VARCHAR(20) PRIMARY KEY,
    nama_guru VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    no_telpon VARCHAR(15),
    alamat TEXT
);
```

#### **Gambar**
![](asset/3.PNG)
#### **Analisis**

- **Kolom `nip`** sebagai primary key menjamin bahwa setiap guru memiliki identifikasi yang unik sesuai dengan standar yang berlaku.
- **Kolom `email`** memiliki constraint **UNIQUE** untuk mencegah penggunaan email yang sama oleh dua guru.
- **Kolom `no_telpon` dan `alamat`** memberikan informasi tambahan yang diperlukan untuk kontak.

#### **Kelebihan**

- Tabel ini sudah cukup lengkap dengan data penting yang diperlukan untuk menghubungkan guru dengan jadwal pelajaran.

#### **Kelemahan**

- Format NIP yang mungkin mengalami perubahan standar dari waktu ke waktu bisa berisiko mengganggu keutuhan data.

---

### **5. Tabel `jadwal`**

#### **Struktur**

```sql
CREATE TABLE jadwal (
    id_jadwal INT AUTO_INCREMENT PRIMARY KEY,
    hari VARCHAR(15) NOT NULL,
    jam_masuk TIME NOT NULL,
    jam_keluar TIME NOT NULL,
    id_guru VARCHAR(20) NOT NULL,
    id_mapel INT NOT NULL,
    FOREIGN KEY (id_guru) REFERENCES guru (nip) ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY (id_mapel) REFERENCES mata_pelajaran (id_mapel) ON DELETE CASCADE ON UPDATE CASCADE
);
```

#### **Gambar**
![](asset/4.PNG)

#### **Analisis**

- **Kolom `id_jadwal`** berfungsi sebagai primary key yang menjamin setiap jadwal memiliki identifikasi unik.
- **Relasi dengan tabel `guru` dan `mata_pelajaran`** memastikan bahwa setiap jadwal terkait dengan guru dan mata pelajaran yang tepat.
- **Constraint `ON DELETE CASCADE`** memastikan bahwa jika guru atau mata pelajaran dihapus, data jadwal terkait juga akan terhapus secara otomatis.

#### **Kelemahan**

- Tabel ini belum menyertakan atribut tambahan seperti lokasi kelas atau perangkat pembelajaran yang digunakan (misalnya proyektor).

---

### **Kesimpulan**

1. **Keunggulan**
    
    - Database ini dirancang dengan efisien, menjaga konsistensi antar tabel.
    - Relasi antar tabel sangat mendukung sistem jadwal pelajaran yang diinginkan.
    - Penggunaan constraint seperti **UNIQUE** dan **FOREIGN KEY** membantu menjaga integritas data secara otomatis.
2. **Potensi Perbaikan**
    
    - Menambahkan informasi tentang lokasi kelas atau perangkat pendukung pada tabel `jadwal` untuk keperluan lebih lanjut.
    - Menambahkan kolom seperti kategori atau jumlah SKS pada tabel `mata_pelajaran` untuk mendukung analisis lebih mendalam terkait kurikulum.

Database yang kami buat diharapkan bisa digunakan dengan baik untuk sistem pengelolaan jadwal pelajaran yang lebih efisien di sekolah-sekolah di Makassar. Semoga dapat memberikan manfaat yang optimal bagi proses pembelajaran.

# soal 2
### **Analisis Contoh 1**

```sql
SELECT g.nama_guru, COUNT(j.id_mapel) AS jumlah_mapel
FROM guru g
JOIN jadwal j ON g.nip = j.id_guru
GROUP BY g.nama_guru
HAVING COUNT(j.id_mapel) > 0;
```

**Penjelasan:**

- **SELECT**: Untuk memilih kolom mana saja yang ingin ditampilkan pada hasil query.
- **g.nama_guru**: Nama kolom yang diambil dari tabel `guru` dan akan ditampilkan.
- **COUNT(j.id_mapel)**: Menghitung jumlah baris pada tabel `jadwal` yang memiliki data pada kolom `id_mapel`. Ini menunjukkan jumlah mata pelajaran yang diajarkan oleh masing-masing guru.
- **AS jumlah_mapel**: Memberikan nama sementara untuk hasil perhitungan `COUNT(j.id_mapel)`. Nama ini digunakan sebagai alias, sehingga hasil akan ditampilkan sebagai kolom `jumlah_mapel`.
- **FROM guru g**: Memilih tabel `guru` sebagai sumber data utama.
- **JOIN jadwal j ON g.nip = j.id_guru**: Menghubungkan tabel `guru` dengan tabel `jadwal` berdasarkan kolom `nip` di tabel `guru` yang sesuai dengan kolom `id_guru` di tabel `jadwal`.
- **GROUP BY g.nama_guru**: Mengelompokkan data berdasarkan nama guru, sehingga setiap baris hasil merupakan ringkasan data untuk setiap guru.
- **HAVING COUNT(j.id_mapel) > 0**: Menyaring hasil kelompok data sehingga hanya menampilkan guru yang mengajar setidaknya satu mata pelajaran.

**Hasil Query**:
![](asset/5.PNG)
- Akan menampilkan nama-nama guru beserta jumlah mata pelajaran yang mereka ajarkan.
- Guru yang tidak mengajar mata pelajaran apa pun tidak akan ditampilkan dalam hasil.

---

### **Analisis Contoh 2**

```sql
SELECT 
    j.hari, 
    j.jam_masuk, 
    j.jam_keluar, 
    g.nama_guru, 
    m.nama_mapel
FROM jadwal j
JOIN guru g ON j.id_guru = g.nip
JOIN mata_pelajaran m ON j.id_mapel = m.id_mapel;
```

**Penjelasan:**

- **SELECT**: Untuk memilih kolom-kolom yang ingin ditampilkan pada hasil query.
- **j.hari, j.jam_masuk, j.jam_keluar**: Kolom yang diambil dari tabel `jadwal`, berisi informasi tentang hari, jam masuk, dan jam keluar mata pelajaran.
- **g.nama_guru**: Kolom dari tabel `guru` yang akan ditampilkan, berisi nama guru yang bertugas.
- **m.nama_mapel**: Kolom dari tabel `mata_pelajaran`, berisi nama mata pelajaran yang diajarkan.
- **FROM jadwal j**: Memilih tabel `jadwal` sebagai sumber data utama.
- **JOIN guru g ON j.id_guru = g.nip**: Menghubungkan tabel `jadwal` dengan tabel `guru` berdasarkan kolom `id_guru` di tabel `jadwal` yang sesuai dengan kolom `nip` di tabel `guru`.
- **JOIN mata_pelajaran m ON j.id_mapel = m.id_mapel**: Menghubungkan tabel `jadwal` dengan tabel `mata_pelajaran` berdasarkan kolom `id_mapel` di kedua tabel.

**Hasil Query**:
![](asset/6.PNG)
- Menampilkan daftar jadwal lengkap, termasuk informasi hari, jam masuk, jam keluar, nama guru yang mengajar, dan nama mata pelajaran.
- Setiap baris hasil merepresentasikan satu entri jadwal dengan informasi terhubung dari tabel `guru` dan `mata_pelajaran`.

---

# Tabel_Keaktifan

![![KELOMPOK 6/#^Table2]]

