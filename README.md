
# Perbandingan Ephemeral State Management dan App State Management di Flutter

Pada proyek ini, kita akan membandingkan dua pendekatan dalam mengelola **state** di Flutter, yaitu **Ephemeral State Management** dan **App State Management** menggunakan paket `scoped_model`. Kami akan menunjukkan perbedaan antara keduanya dalam contoh pengelolaan counter sederhana dan membahas keuntungan menggunakan App State Management, terutama pada aplikasi yang lebih besar.

## 1. **Ephemeral State Management dengan StatefulWidget**

Ephemeral state mengacu pada **state sementara** yang hanya berlaku dalam konteks widget yang sedang aktif. Dalam pendekatan ini, state dikelola di dalam **StatefulWidget** dan akan hilang ketika widget dihancurkan atau aplikasi di-restart.

### Kode:
```dart
import 'package:flutter/material.dart';

void main() => runApp(MyEphemeralApp());

class MyEphemeralApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(title: Text('Ephemeral State Example')),
        body: CounterWidget(),
      ),
    );
  }
}

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
          SizedBox(height: 10),
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

**Kelemahan**:
- State akan hilang jika widget dihancurkan, misalnya saat Hot Reload atau Hot Restart.
- Tidak cocok untuk aplikasi dengan **state** yang perlu dipertahankan di seluruh aplikasi.

## 2. **App State Management dengan scoped_model**

App state mengacu pada **state yang perlu dipertahankan di seluruh aplikasi**. Dalam pendekatan ini, kita menggunakan paket `scoped_model` untuk mengelola state di level aplikasi. Model ini bisa digunakan di berbagai widget, dan state-nya tetap ada meskipun ada perubahan di UI atau widget.

### Kode:
```dart
import 'package:flutter/material.dart';
import 'package:scoped_model/scoped_model.dart';

void main() => runApp(MyApp());

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
            SizedBox(height: 10),
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

**Keuntungan**:
- `scoped_model` memungkinkan pengelolaan state yang terpusat dan **tersinkronisasi** di seluruh aplikasi.
- Menggunakan `ScopedModelDescendant`, kita bisa mengakses state model di widget mana pun dalam pohon widget.
- Dapat bertahan meskipun terjadi perubahan widget atau di-reload.

## 3. **Perbandingan Antara Ephemeral State dan App State Management**

### Ephemeral State (State Sementara)
- **Pengelolaan State Terbatas**: Hanya berlaku di dalam **StatefulWidget** yang bersangkutan.
- **Tidak Bertahan**: State hilang saat widget dihancurkan atau aplikasi direstart.
- **Kelemahan**: Tidak cocok untuk pengelolaan state yang harus bertahan di seluruh aplikasi.

### App State Management (Scoped Model)
- **Pengelolaan State Global**: `scoped_model` memungkinkan pengelolaan state yang dapat diakses oleh seluruh aplikasi.
- **State Bertahan**: Meskipun ada perubahan UI, state tetap ada di seluruh aplikasi.
- **Cocok untuk Aplikasi Lebih Besar**: Ideal untuk mengelola **authentication**, **keranjang belanja**, atau data aplikasi yang bersifat global.

## 4. **Keuntungan Menggunakan App State Management dalam Aplikasi Flutter yang Lebih Besar**

### 4.1 **Pengelolaan User Authentication**
Dalam aplikasi yang memerlukan otentikasi pengguna (misalnya login), menggunakan **App State Management** memungkinkan Anda untuk menyimpan data pengguna (misalnya, token otentikasi) di seluruh aplikasi. Dengan menggunakan `scoped_model`, data otentikasi dapat diakses oleh seluruh widget tanpa perlu melewatkan parameter di setiap widget.

### 4.2 **Mengelola Shopping Cart**
Untuk aplikasi e-commerce, menggunakan App State Management memungkinkan Anda untuk menyimpan status keranjang belanja di seluruh aplikasi. Anda bisa menambahkan, menghapus, atau mengubah jumlah item dalam keranjang tanpa perlu mengelola status di setiap halaman.

### 4.3 **Keuntungan Lainnya**
- **Modularitas**: Dengan memisahkan logika pengelolaan state ke dalam model yang terpisah, kode menjadi lebih modular dan mudah untuk dipelihara.
- **Efisiensi**: Dengan menggunakan `scoped_model`, pembaruan state dapat disebarkan hanya ke widget yang memerlukan pembaruan, yang mengurangi beban render ulang pada UI.

## 5. **Kesimpulan**

Baik **Ephemeral State** maupun **App State Management** memiliki kegunaan masing-masing. Namun, untuk aplikasi Flutter yang lebih besar, seperti yang memerlukan otentikasi pengguna atau pengelolaan data yang lebih kompleks, menggunakan **App State Management** dengan paket seperti `scoped_model` sangat disarankan. Pendekatan ini tidak hanya mempermudah pengelolaan state di seluruh aplikasi, tetapi juga memberikan cara yang lebih efisien dan terorganisir untuk menangani data global.

## 6. **Instalasi dan Cara Menjalankan Proyek**
1. Clone repository ini:
   ```
   git clone https://github.com/KingPublic/State_Management_Lab_activity2_Andreyhs
   ```
2. Instal dependensi dengan menjalankan:
   ```
   flutter pub get
   ```
3. Jalankan aplikasi:
   ```
   flutter run
   ```

---

Semoga penjelasan ini membantu dalam memahami perbedaan antara **Ephemeral State** dan **App State Management** serta mengapa **App State Management** lebih efektif dalam aplikasi besar. Jika ada pertanyaan lebih lanjut, jangan ragu untuk membuka issue atau menghubungi KingPublic.

```

---

Dengan `README.md` ini, saya memberikan penjelasan yang jelas mengenai perbedaan antara kedua pendekatan dalam pengelolaan state di Flutter dan alasan mengapa menggunakan **App State Management** dengan `scoped_model` lebih efisien untuk aplikasi yang lebih besar.
