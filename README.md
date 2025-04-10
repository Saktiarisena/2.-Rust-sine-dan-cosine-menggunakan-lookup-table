Berikut adalah panduan lengkap untuk memahami dan menggunakan program Rust yang mengimplementasikan tabel trigonometri dengan lookup table.

## Gambaran Program
Program ini membuat:
1. Lookup table untuk nilai sin dan cos dari 0° sampai 90°
2. Fungsi untuk menghandle semua kuadran (0°-360°)
3. Konversi derajat ke radian
4. Tampilan tabel yang rapi

## Langkah 1: Setup Proyek
1. Buat proyek baru:
```bash
cargo new rust-trigonometry
cd rust-trigonometry
```

2. Salin kode berikut ke `src/main.rs`:

```rust
use std::f64::consts::PI;

struct Trigonometry {
    // Lookup table untuk sudut 0-90 derajat (sin, cos)
    lookup_table: [(f64, f64); 91],
}

impl Trigonometry {
    fn new() -> Self {
        // Isi lookup table (contoh sederhana, bisa diganti dengan nilai lebih presisi)
        let mut table = [(0.0, 0.0); 91];
        for degrees in 0..=90 {
            let rad = (degrees as f64).to_radians();
            table[degrees as usize] = (rad.sin(), rad.cos());
        }
        
        Self { lookup_table: table }
    }

    // ... [implementasi lainnya]
}

fn main() {
    let trig = Trigonometry::new();
    trig.print_table(0.0, 360.0, 15.0);
}
```

## Langkah 2: Memahami Struktur Dasar
1. **Struct Trigonometry**:
   - Menyimpan lookup table untuk sin dan cos
   - Table berisi 91 entri (0° sampai 90°)

2. **Method new()**:
   - Mengisi table dengan nilai sin dan cos yang dihitung menggunakan fungsi standar Rust
   - Nilai disimpan dalam tuple (sin, cos)

## Langkah 3: Implementasi Fungsi Utama
1. **Konversi Derajat ke Radian**:
```rust
fn to_radians(&self, degrees: f64) -> f64 {
    degrees * PI / 180.0
}
```

2. **Fungsi Sinus dengan Handling Kuadran**:
```rust
fn sin(&self, degrees: f64) -> f64 {
    let degrees = degrees % 360.0;
    let degrees = if degrees < 0.0 { degrees + 360.0 } else { degrees };
    
    match degrees {
        d if d <= 90.0 => self.lookup_table[d as usize].0,
        d if d <= 180.0 => self.lookup_table[(180.0 - d) as usize].0,
        d if d <= 270.0 => -self.lookup_table[(d - 180.0) as usize].0,
        _ => -self.lookup_table[(360.0 - degrees) as usize].0,
    }
}
```

3. **Fungsi Cosinus dengan Handling Kuadran**:
```rust
fn cos(&self, degrees: f64) -> f64 {
    let degrees = degrees % 360.0;
    let degrees = if degrees < 0.0 { degrees + 360.0 } else { degrees };
    
    match degrees {
        d if d <= 90.0 => self.lookup_table[d as usize].1,
        d if d <= 180.0 => -self.lookup_table[(180.0 - d) as usize].1,
        d if d <= 270.0 => -self.lookup_table[(d - 180.0) as usize].1,
        _ => self.lookup_table[(360.0 - degrees) as usize].1,
    }
}
```

## Langkah 4: Menampilkan Tabel
```rust
fn print_table(&self, start: f64, end: f64, step: f64) {
    println!("| Degrees | Radians    | sin(x)     | cos(x)     |");
    println!("|---------|------------|------------|------------|");
    
    let mut degrees = start;
    while degrees <= end {
        let radians = self.to_radians(degrees);
        println!("| {:>7.1} | {:>10.6} | {:>10.6} | {:>10.6} |",
            degrees,
            radians,
            self.sin(degrees),
            self.cos(degrees)
        );
        degrees += step;
    }
}
```

## Langkah 5: Menjalankan Program
1. Jalankan program:
```bash
cargo run
```

2. Output contoh:
```
| Degrees | Radians    | sin(x)     | cos(x)     |
|---------|------------|------------|------------|
|     0.0 |  0.000000 |  0.000000 |  1.000000 |
|    15.0 |  0.261799 |  0.258819 |  0.965926 |
|    30.0 |  0.523599 |  0.500000 |  0.866025 |
|    ...  |    ...    |    ...    |    ...    |
|   360.0 |  6.283185 |  0.000000 |  1.000000 |
```

## Optimasi dan Pengembangan
1. **Tingkatkan Presisi**:
   - Gunakan nilai prekomputasi dengan presisi lebih tinggi
   - Atau gunakan crate seperti `libm` untuk perhitungan lebih akurat

2. **Tambahkan Fungsi**:
   - Implementasikan tan, cot, sec, csc
   - Tambahkan inverse trigonometric functions

3. **Benchmarking**:
   - Bandingkan kecepatan dengan fungsi standar Rust
   - Gunakan `#[bench]` untuk pengukuran performa
