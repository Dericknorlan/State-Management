# Lab Activity 2 - State Management

Repositori ini berisi dua proyek Flutter untuk mempelajari dan membandingkan dua pendekatan manajemen state:
1. **Ephemeral State Management** menggunakan `StatefulWidget`.
2. **App State Management** menggunakan paket `scoped_model`.

---

## Tujuan
- Memahami cara kerja **Ephemeral State Management** untuk mengelola state lokal di dalam widget tertentu.
- Mempelajari **App State Management** untuk mengelola state di seluruh aplikasi.
- Mengetahui perbedaan, kelebihan, dan kapan menggunakan masing-masing pendekatan.

---

## Struktur Proyek
Repositori ini terdiri dari dua folder utama:
- **ephemeral_state_codelab**: Contoh manajemen state lokal menggunakan `StatefulWidget`.
- **app_state_codelab**: Contoh manajemen state aplikasi menggunakan `scoped_model`.

---

## Penjelasan Proyek

### 1. **Ephemeral State Management (ephemeral_state_codelab)**
Proyek ini menunjukkan bagaimana menggunakan `StatefulWidget` untuk mengelola state lokal dalam aplikasi sederhana. Pendekatan ini cocok untuk widget yang tidak berbagi state dengan bagian lain dari aplikasi.

#### **Fitur Utama**:
- Menggunakan `StatefulWidget` untuk memanipulasi state lokal.
- Proyek sederhana, cocok untuk kebutuhan state yang terbatas.

#### **Kode Utama**:
```dart
class CounterWidget extends StatefulWidget {
  @override
  _CounterWidgetState createState() => _CounterWidgetState();
}

class _CounterWidgetState extends State<CounterWidget> {
  int _counter = 0;

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: <Widget>[
          Text('Counter Value: $_counter'),
          ElevatedButton(
            onPressed: () {
              setState(() {
                _counter++;
              });
            },
            child: Text('Increment'),
          ),
        ],
      ),
    );
  }
}
```
Untuk menjalankan aplikasi **Ephemeral State Management**, ikuti langkah-langkah berikut:
1. Jalankan aplikasi dengan perintah:
  ```bash
  flutter run
  ```

### 2. **App State Management (Scoped Model)**

Proyek ini menunjukkan bagaimana menggunakan paket `scoped_model` untuk mengelola state di seluruh aplikasi. Pendekatan ini lebih cocok untuk aplikasi kompleks yang membutuhkan state yang dapat diakses dari banyak widget atau layar.

---

## Fitur Utama
1. **Manajemen State Global**: Menggunakan `scoped_model` untuk menyimpan state aplikasi secara global.
2. **Akses State yang Mudah**: State dapat diakses dan dimodifikasi dari banyak widget tanpa perlu mempassing data secara manual.
3. **Notifikasi Perubahan State Otomatis**: UI diperbarui secara otomatis saat state berubah.

---

## Kode Utama
```dart
import 'package:flutter/material.dart';
import 'package:scoped_model/scoped_model.dart';

class CounterModel extends Model {
  int _counter = 0;

  int get counter => _counter;

  void increment() {
    _counter++;
    notifyListeners();
  }

  void decrement() {
    _counter--;
    notifyListeners();
  }
}

class MyApp extends StatelessWidget {
  final CounterModel model = CounterModel();

  @override
  Widget build(BuildContext context) {
    return ScopedModel<CounterModel>(
      model: model,
      child: MaterialApp(
        home: Scaffold(
          appBar: AppBar(title: Text('Scoped Model Example')),
          body: CounterWidget(),
        ),
      ),
    );
  }
}

class CounterWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ScopedModelDescendant<CounterModel>(
      builder: (context, child, model) => Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text('Counter Value: ${model.counter}'),
            ElevatedButton(
              onPressed: () {
                model.increment();
              },
              child: Text('Increment'),
            ),
            ElevatedButton(
              onPressed: () {
                model.decrement();
              },
              child: Text('Decrement'),
            ),
          ],
        ),
      ),
    );
  }
}
```
Untuk menjalankan aplikasi **App State Management**, ikuti langkah-langkah berikut:
1. Pastikan dependensi diunduh dengan menjalankan:
   ```bash
   flutter pub get
   ```
2. Jalankan aplikasi dengan perintah:
   ```bash
    flutter run
   ```

**Perbandingan: Ephemeral vs App State Management**
| **Fitur**              | **Ephemeral State**                          | **App State Management**                    |
|------------------------|---------------------------------------------|---------------------------------------------|
| **Ruang Lingkup**       | Terbatas pada widget tertentu               | Dapat diakses di seluruh aplikasi          |
| **Kompleksitas**        | Sederhana                                   | Lebih kompleks                             |
| **Kegunaan**            | State widget tunggal                        | State aplikasi global                      |
| **Skalabilitas**        | Rendah                                      | Tinggi                                      |
| **Kemudahan Pengujian**| Dasar                                       | Lebih mudah diuji                          |

Ephemeral State hanya berlaku dalam lingkup satu widget dan akan kembali ke kondisi awal setiap kali widget tersebut di-rebuild. Sebaliknya, Scoped Model memungkinkan state diakses oleh banyak widget sekaligus tanpa ter-reset, memberikan fleksibilitas lebih dalam mengelola data aplikasi. Ketika model diperbarui, semua children yang bergantung pada model tersebut akan secara otomatis di-rebuild. Hal ini menjadikan Scoped Model sangat cocok untuk kasus seperti autentikasi pengguna dan keranjang belanja, di mana data perlu disimpan dan diakses dengan mudah di berbagai bagian aplikasi. Selain itu, Scoped Model memisahkan logika pengelolaan state dari elemen UI, sehingga memudahkan proses pengembangan, pemeliharaan, dan penyesuaian struktur aplikasi.
