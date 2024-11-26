Berikut adalah file README dalam Bahasa Indonesia untuk kedua bagian:

---

# Ephemeral State Management dan App State Management dengan Flutter

## **Pendahuluan**
Panduan ini menjelaskan dua pendekatan manajemen state di Flutter:
1. **Ephemeral State Management** menggunakan `StatefulWidget`.
2. **App State Management** menggunakan paket `scoped_model`.

---

## **Bagian 1: Ephemeral State Management dengan `StatefulWidget`**

### **Langkah 1: Membuat Proyek Flutter**
- Buat proyek Flutter baru menggunakan IDE favorit Anda.
- Beri nama proyek **`ephemeral_state_codelab`**.

### **Langkah 2: Implementasi StatefulWidget**
- Buka file `lib/main.dart` dan masukkan kode berikut:
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

### **Observasi**
- State (`_counter`) dikelola secara lokal di dalam widget.
- Ketika tombol **Increment** ditekan, nilai `_counter` meningkat, dan UI diperbarui.
- Namun, state tidak bertahan jika widget dihancurkan atau aplikasi direstart.

### **Kesimpulan**
Ephemeral State cocok untuk:
- State yang hanya digunakan di satu widget.
- State sementara seperti input form atau animasi.

---

## **Bagian 2: App State Management dengan `scoped_model`**

### **Langkah 1: Membuat Proyek Flutter**
- Buat proyek Flutter baru.
- Beri nama proyek **`app_state_codelab`**.

### **Langkah 2: Menambahkan Dependency**
- Tambahkan paket `scoped_model` ke dalam file `pubspec.yaml`:
  ```yaml
  dependencies:
    flutter:
      sdk: flutter
    scoped_model: ^5.0.0
  ```
- Jalankan perintah:
  ```bash
  flutter pub get
  ```

### **Langkah 3: Implementasi Scoped Model**
- Buka file `lib/main.dart` dan masukkan kode berikut:
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

### **Observasi**
- State (`_counter`) dikelola dalam kelas model (`CounterModel`).
- Dengan `ScopedModel`, state dapat diakses dan diperbarui oleh banyak widget dalam aplikasi.
- Komponen UI otomatis diperbarui saat model memanggil `notifyListeners()`.

### **Kesimpulan**
Scoped Model cocok untuk:
- State yang dibagikan di seluruh aplikasi.
- Aplikasi skala besar dengan banyak widget yang membutuhkan state yang sama.

---

## **Langkah 4: Menjalankan Proyek**
1. Jalankan proyek **`ephemeral_state_codelab`**:
   ```bash
   flutter run
   ```
2. Jalankan proyek **`app_state_codelab`** di terminal lain:
   ```bash
   flutter run
   ```

---

## **Perbandingan Kedua Pendekatan**
- **Ephemeral State** cocok untuk manajemen state lokal dan sederhana.
- **Scoped Model** memberikan solusi untuk state yang kompleks, dibagikan, dan membutuhkan sinkronisasi.

Keduanya berguna untuk kebutuhan yang berbeda dalam pengembangan aplikasi Flutter.