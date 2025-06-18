# 📘 Sistem Informasi Mahasiswa - Fullstack Guide (Backend: CodeIgniter 4 | Frontend: Laravel)

Panduan lengkap untuk membangun sistem informasi mahasiswa berbasis **REST API dengan CodeIgniter 4** dan frontend **Laravel**, dari awal hingga aplikasi CRUD bisa berjalan. Dirancang untuk pemula dan mahasiswa tugas akhir.

## 🧠 Tujuan Proyek

- **Belajar membangun API backend** yang rapi dan terstruktur.
- **Mengonsumsi API di frontend** Laravel dengan cara modern (tanpa database di sisi Laravel).
- Mengimplementasikan **CRUD** (Create, Read, Update, Delete) secara utuh dari frontend hingga backend.
- Dapat dijadikan **template awal** untuk proyek UAS, skripsi, atau aplikasi production ringan.

## 🔧 Teknologi yang Digunakan

| Komponen | Teknologi                        |
| -------- | -------------------------------- |
| Backend  | CodeIgniter 4 (CI4)              |
| Frontend | Laravel                          |
| DBMS     | MySQL via phpMyAdmin             |
| HTTP API | RESTful JSON                     |
| Tools    | Postman, Composer, Laragon/XAMPP |

## 📦 STRUKTUR KODE BACKEND (CI4)

```
app/
├── Controllers/
│   └── Mahasiswa.php      # Semua endpoint API mahasiswa
├── Models/
│   └── MahasiswaModel.php # Abstraksi untuk query DB mahasiswa
├── Config/
│   └── Routes.php         # Routing API
```

### Alasan struktur seperti ini:

- **Controllers** bertugas sebagai pengatur alur data (masuk/keluar).
- **Models** bertanggung jawab pada interaksi database, bukan di controller → mengikuti prinsip _separation of concern_.
- **Routes.php** dipakai agar endpoint REST bisa dikustom.

## 🚀 LANGKAH BACKEND: CODEIGNITER 4

### 1️⃣ Clone Project dan Setup

```bash
git clone https://github.com/nama-kamu/pbf-backend.git
cd pbf-backend
composer install
cp .env.example .env
```

### 2️⃣ Buat Database di phpMyAdmin

- Buka `http://localhost/phpmyadmin`
- Klik "New"
- Buat database: `cuti`

### 3️⃣ Ubah .env → Sesuaikan DB

```dotenv
database.default.hostname = localhost
database.default.database = cuti
database.default.username = root
database.default.password =
```

### 4️⃣ Jalankan Server

```bash
php spark serve
```

Akses: `http://localhost:8080`

## 📮 ENDPOINT API MAHASISWA

| Method | Endpoint        | Deskripsi                  |
| ------ | --------------- | -------------------------- |
| GET    | /mahasiswa      | Ambil semua data           |
| GET    | /mahasiswa/{id} | Ambil satu data            |
| POST   | /mahasiswa      | Tambah data                |
| PUT    | /mahasiswa/{id} | Update data berdasarkan ID |
| DELETE | /mahasiswa/{id} | Hapus data                 |

> Tes pakai Postman atau Thunder Client (VS Code extension).

## ✨ LANGKAH FRONTEND: LARAVEL

### 1️⃣ Buat Proyek Laravel Baru

```bash
composer create-project laravel/laravel frontend-mahasiswa
cd frontend-mahasiswa
php artisan serve
```

### 2️⃣ Install HTTP Client (jika belum)

```bash
composer require guzzlehttp/guzzle
```
### 4️⃣ Buat Layouts
**resources/views/layouts/app.blade.php**
```blade
<!DOCTYPE html>
<html>
<head>
    <title>Sistem Mahasiswa</title>
</head>
<body>
    <div class="container">
        @yield('content')
    </div>
</body>
</html>
```

---
### 3️⃣ Tambah Controller Mahasiswa

```bash
php artisan make:controller MahasiswaController
```

#### Isi Controller:

```php
use Illuminate\Support\Facades\Http;

class MahasiswaController extends Controller
{
    public function index() {
        $data = Http::get('http://localhost:8080/mahasiswa')->json();
        return view('mahasiswa.index', ['mahasiswa' => $data]);
    }

    public function create() {
        return view('mahasiswa.create');
    }

    public function store(Request $request) {
        Http::post('http://localhost:8080/mahasiswa', $request->all());
        return redirect('/mahasiswa');
    }

    public function edit($id) {
        $mhs = Http::get("http://localhost:8080/mahasiswa/$id")->json();
        return view('mahasiswa.edit', ['mhs' => $mhs]);
    }

    public function update(Request $request, $id) {
        Http::put("http://localhost:8080/mahasiswa/$id", $request->all());
        return redirect('/mahasiswa');
    }

    public function destroy($id) {
        Http::delete("http://localhost:8080/mahasiswa/$id");
        return redirect('/mahasiswa');
    }
}
```

### 4️⃣ Routing

```php
use App\Http\Controllers\MahasiswaController;

Route::get('/mahasiswa', [MahasiswaController::class, 'index']);
Route::get('/mahasiswa/create', [MahasiswaController::class, 'create']);
Route::post('/mahasiswa', [MahasiswaController::class, 'store']);
Route::get('/mahasiswa/{id}/edit', [MahasiswaController::class, 'edit']);
Route::put('/mahasiswa/{id}', [MahasiswaController::class, 'update']);
Route::delete('/mahasiswa/{id}', [MahasiswaController::class, 'destroy']);
```

### 5️⃣ View Blade Template

**resources/views/mahasiswa/index.blade.php**

```blade
<h1>Data Mahasiswa</h1>
<a href="/mahasiswa/create">Tambah Mahasiswa</a>
<ul>
@foreach ($mahasiswa as $m)
    <li>
        {{ $m['nama'] }} - {{ $m['nim'] }}
        <a href="/mahasiswa/{{ $m['id'] }}/edit">Edit</a>
        <form action="/mahasiswa/{{ $m['id'] }}" method="POST" style="display:inline;">
            @csrf @method('DELETE')
            <button type="submit">Hapus</button>
        </form>
    </li>
@endforeach
</ul>
```

**resources/views/mahasiswa/create.blade.php**

```blade
<h1>Tambah Mahasiswa</h1>
<form method="POST" action="/mahasiswa">
    @csrf
    <input name="nim" placeholder="NIM"><br>
    <input name="nama" placeholder="Nama"><br>
    <button type="submit">Simpan</button>
</form>
```

**resources/views/mahasiswa/edit.blade.php**

```blade
<h1>Edit Mahasiswa</h1>
<form method="POST" action="/mahasiswa/{{ $mhs['id'] }}">
    @csrf @method('PUT')
    <input name="nim" value="{{ $mhs['nim'] }}"><br>
    <input name="nama" value="{{ $mhs['nama'] }}"><br>
    <button type="submit">Update</button>
</form>
```

## 🔁 Tips Tambahan

- Gunakan `.env` Laravel untuk menyimpan `API_URL=http://localhost:8080`
- Gantilah semua `Http::get(...)` dengan `Http::get(env('API_URL').'/mahasiswa')` agar lebih fleksibel
- Bisa dikembangkan jadi SPA (Single Page App) menggunakan Vue/React

## ✅ Hasil Akhir

- Backend API: `http://localhost:8080`
- Frontend Laravel: `http://localhost:8000`
- CRUD Berjalan: ✅

## 📄 Lisensi

Bebas digunakan untuk pembelajaran dan tugas kuliah.

## 👨‍🎓 Dibuat oleh

- Nama: [Isi namamu]
- NIM: [Isi NIM-mu]
- Untuk: Tugas Akhir / UAS Pemrograman Berbasis Framework
