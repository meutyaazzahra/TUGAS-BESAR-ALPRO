<h1 align="center">TUGAS BESAR</h1>
<h2 align="center">ALGORITMA DAN PEMROGRAMAN 2</h2>


<p align="center">
  <img src="telkomuniv.png" alt="Logo Telkom University" width="200">
</p>
<p align="center">
    <strong>Disusun Oleh:</strong><br>
    Meutya Azzahra Efendi / 2311102166<br>
    Lailatur Rahmah / 2311102177<br>
</p>

<p align="center">
    <strong>Dosen Pengampu:</strong><br>
    Abednego Dwi Septiadi
</p>

<p align="center">
    PROGRAM STUDI S1 TEKNIK INFORMATIKA<br>
    FAKULTAS INFORMATIKA<br>
    TELKOM UNIVERSITY PURWOKERTO<br>
    2024
</p>

## STUDI KASUS
### Aplikasi Konsultasi Kesehatan
Deskripsi: Aplikasi ini digunakan oleh pasien dan dokter. Data yang diolah adalah data konsultasi antara pasien dan dokter.

Spesifikasi:

a.	Pengguna bisa mendaftar ke aplikasi sebagai pasien.

b.	Pasien bisa konsultasi dengan posting pertanyaan melalui aplikasi.

c.	Dokter dan Pasien bisa memberikan tanggapan terhadap pertanyaan dari pasien.

d.	Pengguna yang tidak mendaftar hanya bisa melihat forum konsultasi antara pasien dan dokter.

e.	Pertanyaan perlu diberi tag tertentu untuk memudahkan pencarian dan pengurutan.

f.	Pengguna bisa mencari pertanyaan tertentu berdasarkan tag yang dicari.

g.	Dokter bisa menampilkan topik atau tag terurut berdasarkan jumlah pertanyaannya.


### Source Code

```go
package main

import (
	"bufio"
	"fmt"
	"os"
	"sort"
	"strings"
)

// Struktur untuk data pengguna
type Pengguna struct {
	ID    int
	Nama  string
	Peran string // "pasien" atau "dokter"
}

// Struktur untuk data pertanyaan
type Pertanyaan struct {
	ID        int
	Pasien    string
	Isi       string
	Tag       []string
	Tanggapan []string
}

// Struktur utama aplikasi
type Aplikasi struct {
	Pengguna                []Pengguna
	Pertanyaan              []Pertanyaan
	IDPenggunaSelanjutnya   int
	IDPertanyaanSelanjutnya int
}

// Fungsi untuk mendaftarkan pasien
func (aplikasi *Aplikasi) DaftarPasien(nama string) {
	pengguna := Pengguna{
		ID:    aplikasi.IDPenggunaSelanjutnya,
		Nama:  nama,
		Peran: "pasien",
	}
	aplikasi.Pengguna = append(aplikasi.Pengguna, pengguna)
	aplikasi.IDPenggunaSelanjutnya++
	fmt.Println("Pasien berhasil didaftarkan:", nama)
}

// Fungsi untuk menambahkan pertanyaan
func (aplikasi *Aplikasi) TambahPertanyaan(namaPasien, isi string, tag []string) {
	pertanyaan := Pertanyaan{
		ID:     aplikasi.IDPertanyaanSelanjutnya,
		Pasien: namaPasien,
		Isi:    isi,
		Tag:    tag,
	}
	aplikasi.Pertanyaan = append(aplikasi.Pertanyaan, pertanyaan)
	aplikasi.IDPertanyaanSelanjutnya++
	fmt.Println("Pertanyaan berhasil ditambahkan oleh:", namaPasien)
}

// Fungsi untuk menambahkan tanggapan ke pertanyaan
func (aplikasi *Aplikasi) TambahTanggapan(IDPertanyaan int, tanggapan string) {
	for i := range aplikasi.Pertanyaan {
		if aplikasi.Pertanyaan[i].ID == IDPertanyaan {
			aplikasi.Pertanyaan[i].Tanggapan = append(aplikasi.Pertanyaan[i].Tanggapan, tanggapan)
			fmt.Println("Tanggapan berhasil ditambahkan ke pertanyaan ID:", IDPertanyaan)
			return
		}
	}
	fmt.Println("Pertanyaan tidak ditemukan!")
}

// Fungsi untuk mencari pertanyaan berdasarkan kata kunci
func (aplikasi *Aplikasi) CariPertanyaanKataKunci(kataKunci string) {
	fmt.Println("Pertanyaan yang mengandung kata kunci:", kataKunci)
	for _, pertanyaan := range aplikasi.Pertanyaan {
		if strings.Contains(strings.ToLower(pertanyaan.Isi), strings.ToLower(kataKunci)) {
			fmt.Printf("ID: %d | Pasien: %s | Isi: %s\n", pertanyaan.ID, pertanyaan.Pasien, pertanyaan.Isi)
		}
	}
}

// Fungsi untuk mengurutkan pertanyaan berdasarkan jumlah tag
func (aplikasi *Aplikasi) UrutkanPertanyaanJumlahTag() {
	sort.Slice(aplikasi.Pertanyaan, func(i, j int) bool {
		return len(aplikasi.Pertanyaan[i].Tag) > len(aplikasi.Pertanyaan[j].Tag)
	})

	fmt.Println("Pertanyaan diurutkan berdasarkan jumlah tag:")
	for _, pertanyaan := range aplikasi.Pertanyaan {
		fmt.Printf("ID: %d | Pasien: %s | Jumlah Tag: %d | Isi: %s\n", pertanyaan.ID, pertanyaan.Pasien, len(pertanyaan.Tag), pertanyaan.Isi)
	}
}

// Fungsi untuk mencari pertanyaan berdasarkan tag tertentu
func (aplikasi *Aplikasi) CariPertanyaanTag(tag string) {
	fmt.Println("Pertanyaan dengan tag:", tag)
	for _, pertanyaan := range aplikasi.Pertanyaan {
		for _, t := range pertanyaan.Tag {
			if strings.EqualFold(t, tag) {
				fmt.Printf("ID: %d | Pasien: %s | Isi: %s\n", pertanyaan.ID, pertanyaan.Pasien, pertanyaan.Isi)
			}
		}
	}
}

// Fungsi untuk mengurutkan tag berdasarkan popularitas
func (aplikasi *Aplikasi) UrutkanTagPopularitas() {
	hitungTag := make(map[string]int)
	for _, pertanyaan := range aplikasi.Pertanyaan {
		for _, tag := range pertanyaan.Tag {
			hitungTag[tag]++
		}
	}

	// Konversi map ke slice untuk diurutkan
	type TagPopuler struct {
		Tag    string
		Jumlah int
	}
	var daftarTag []TagPopuler
	for tag, jumlah := range hitungTag {
		daftarTag = append(daftarTag, TagPopuler{Tag: tag, Jumlah: jumlah})
	}

	sort.Slice(daftarTag, func(i, j int) bool {
		return daftarTag[i].Jumlah > daftarTag[j].Jumlah
	})

	fmt.Println("Tag diurutkan berdasarkan popularitas:")
	for _, t := range daftarTag {
		fmt.Printf("Tag: %s | Pertanyaan: %d\n", t.Tag, t.Jumlah)
	}
}

// Fungsi untuk menampilkan menu dan menjalankan aplikasi
func main() {
	aplikasi := &Aplikasi{}
	scanner := bufio.NewScanner(os.Stdin)

	for {
		fmt.Println("================================================")
		fmt.Println("\n          Menu Aplikasi Konsultasi          \n")
		fmt.Println("================================================")
		fmt.Println("1. Daftarkan Pasien")
		fmt.Println("2. Tambah Pertanyaan")
		fmt.Println("3. Tambah Tanggapan")
		fmt.Println("4. Cari Pertanyaan berdasarkan Tag")
		fmt.Println("5. Urutkan Tag berdasarkan Popularitas")
		fmt.Println("6. Cari Pertanyaan berdasarkan Kata Kunci")
		fmt.Println("7. Urutkan Pertanyaan berdasarkan Jumlah Tag")
		fmt.Println("8. Keluar")
		fmt.Print("Pilih menu: ")
		scanner.Scan()
		pilihan := scanner.Text()

		switch pilihan {
		case "1":
			fmt.Print("Masukkan nama pasien: ")
			scanner.Scan()
			nama := scanner.Text()
			aplikasi.DaftarPasien(nama)

		case "2":
			fmt.Print("Masukkan nama pasien: ")
			scanner.Scan()
			nama := scanner.Text()
			fmt.Print("Masukkan isi pertanyaan: ")
			scanner.Scan()
			isi := scanner.Text()
			fmt.Print("Masukkan tag (pisahkan dengan koma): ")
			scanner.Scan()
			tag := strings.Split(scanner.Text(), ",")
			for i := range tag {
				tag[i] = strings.TrimSpace(tag[i])
			}
			aplikasi.TambahPertanyaan(nama, isi, tag)

		case "3":
			fmt.Print("Masukkan ID pertanyaan: ")
			scanner.Scan()
			var ID int
			fmt.Sscanf(scanner.Text(), "%d", &ID)
			fmt.Print("Masukkan tanggapan: ")
			scanner.Scan()
			tanggapan := scanner.Text()
			aplikasi.TambahTanggapan(ID, tanggapan)

		case "4":
			fmt.Print("Masukkan tag untuk mencari: ")
			scanner.Scan()
			tag := scanner.Text()
			aplikasi.CariPertanyaanTag(tag)

		case "5":
			aplikasi.UrutkanTagPopularitas()

		case "6":
			fmt.Print("Masukkan kata kunci untuk mencari: ")
			scanner.Scan()
			kataKunci := scanner.Text()
			aplikasi.CariPertanyaanKataKunci(kataKunci)

		case "7":
			aplikasi.UrutkanPertanyaanJumlahTag()

		case "8":
			fmt.Println("Keluar dari aplikasi. Sampai jumpa!")
			return

		default:
			fmt.Println("Pilihan tidak valid. Silakan coba lagi.")
		}
	}
}
```

### Deskripsi Program

