# Daftar Isi

[Asked Question](ASKEDQUESTION.md)

[Architecture Overview](#architecture-overview)

&emsp;[Modular Monolithic](#modular-monolithic)

&emsp;[Onion Architecture](#onion-architecture)



# Architecture Overview
Pada modul ini menggunakan *modular monolithic* dan *onion architecture*.

## Modular Monolithic
 Sebuah pola arsitektural untuk meningkatkan modularitas modul yang ada dalam aplikasi dengan memisahkan modul ke dalam sebuah package beserta dependensinya.

 ### Keuntungan

    1. Setiap modul bisa memiliki strukturnya sendiri, sehingga bisa mengatur arsitektur yang digunakan dan layeringnya.
    2. Mengurangi merge conflicts, karena tiap modul dienkapsulasi pada sebuah package.
    3. Setiap modul memiliki dependensinya sendiri, sehingga bisa mengatur dependensi tersebut tanpa memberikan efek samping terhadap modul lain.

 ### How
    1. Pada dasarnya ini adalah package untuk memisahkan modul, Sehingga hanya perlu membuat folder untuk modul tersebut.
    2. Namun pada package tersebut juga berisi dependensi yang dibutuhkan modul tersebut. (Contoh: query modul lain, service modul lain).

## Onion Architecture
Sebuah pola arsitektural untuk memisahkan tugas dari tiap layer.

## Keuntungan
    1. Inti dari arsitektur adalah aturan bisnis.
    2. Memberikan abstraksi dan enkapsulasi untuk tiap layer.
    4. Arah dependensi ke inti, sehingga arah dependensi jelas.
    5. Menggunakan Data Transfer Object untuk proses pemindahan data antar layer.
    6. Membantu untuk tidak melanggar prinsip OOP dan SOLID.

## Layers

### Core
    1. Model

        1. Entitas: Merupakan objek yang memiliki identifier tersendiri (Pada dasarnya sesuai dengan tabel yang ada). Berisi properti dan behaviournya.

        2. Value Object: Bentuk enkapsulasi dari properti yang dimiliki entitas, namun memiliki behaviournya sendiri.

    2. Repositories
    Repository Interface: Berisi interface repository, sebagai abstraksi pada kelas konkretnya (Perlu diingat bahwa hanya entity yang memiliki repository).

    3. Service
    Service: Merupakan behaviour entitas yang lebih kompleks (Behaviour bisnis yang memerlukan lebih dari 1 objek).
        
### Application
    1. Command: Menjalankan logic aplikasi, memanggil repository yang dibutuhkan, dan melakukan mapping output.

    2. Query
    
        1. DTO: Merupakan objek yang mendifinisikan properti (kolom) yang menjadi output dari query.

        2. Interface: Berisi interface repository, sebagai abstraksi pada kelas konkretnya.

    4. Service: Jika query membutuhkan logika yang lebih kompleks, bisa dipisahkan di dalam service ini.

### Infrastructure
    1. Persistence: Merupakan Package untuk mengakses basis data atau persistence layer.
        1. Repositories: Merupakan kelas konkret untuk melakukan operasi ke basis data (di dalam 1 tabel atau entitas).

        2. Query: Merupakan kelas konkret untuk melakukan operasi ke basis data (memerlukan lebih dari 1 tabel).

        3. Cache: Merupakan decorator class untuk mengimplementasikan caching.
        
    2. Service
        1. Service: Merupakan kelas untuk menghubungkan dengan infrastructure service di luar aplikasi.

    3. External Service
        1. API Service di luar aplikasi.

### Controller
    Merupakan endpoint API. Tugasnya hanya menerima dan memvalidasi request, memanggil repository atau service dan mengembalikan hasil ke pengguna.

## How

### 1. Mendifinisikan Layer
    Mendefinisikan layer yang akan digunakan dan tugasnya. Layer yang digunakan pada modul ini sesuai dengan yang dijelaskan sebelumnya.

### 2. Membuat Struktur Folder
    Membuat struktur folder berdasarkan layer yang digunakan sebelumnya.

    ├── Application
    │   ├── Command
    │   ├── Query
    │   └── Service
    ├── Controller
    ├── Core
    │   ├── Model
    │   ├── Repositories
    │   └── Service
    └── Infrastructure
        ├── External Service
        ├── Persistence
        │   ├── Cache
        │   ├── Query
        │   └── Repositories
        └── Service


### 3. Mendefinisikan Entitas, Value object dan Constant
#### Entitas
    Entitas yang terdapat pada modul ini antara lain.
    1. Bansos (Keterangan Bansos)
    2. Disabilitas (Agregate dengan IdSemesta)
    3. Individu (Agregate dengan IdSemesta)
    4. JenisDisabilitas (Agreagate dengan IdDisabilitas)
    5. Pekerjaan
    6. RiwayatHamil (Agregate dengan IdSemesta)
    7. RiwayatMeninggal (Agregate dengan IdSemesta)
    8. Sanggahan
    9. SKDTKS

#### Value Object
    Value object yang terdapat pada modul ini antara lain.
    1. IdDisabilitas
    2. IdSemesta
    3. IdWilayah
    4. JenisKelamin
    5. KodeSotk
    6. Status Kawin
    7. VerifyID

#### Constant
    Constant yang terdapat pada modul ini antara lain.
    1. Bulan
    2. JenisBansos

### 4. Mengimplementasikan Core Layer

#### Model
    Model berisi Entitas, Value Object dan Constant berisi Properti dan Behaviour.

    1. Properti
    Mendifinisikan Tipe data, nama variabel dan gettersnya.

    2. Constructor
    Implementasi constructor untuk membuat objek, ditambahkan validasi atau formatting jika diperlukan.

    3. Behaviour
    Implementasi behaviour yang dimiliki oleh Entitas, Value Object dan Constant.

#### Repositories
    Berisi interface repository, sebagai abstraksi pada kelas konkretnya.

    1. Penentuan Repsitory
    Perlu diingat bahwa hanya entitas yang memiliki repository, jika ada query value object dimasukan kedalam entitas yang aggregatnya.

    2. Implementasi Interface
    Mendifinisikan nama method, parameter yang dibutuhkan dan output yang dihasilkan.

#### Service
    Berisi behaviour entitas yang lebih kompleks.

    1. Penentuan Behaviour
    Untuk behaviour yang masuk kedalam service, merupakan behaviour yang melakukan operasi lebih dari satu objek.

    2. Implementasi
    Implementasi sesuai logika bisnis yang ada.

### 5. Mengimplementasikan Application Layer

#### Command
    Menjalankan logic aplikasi, memanggil repository yang dibutuhkan, dan melakukan mapping output.

    1. Membuat Package
    Membuat package dengan nama yang definitif sesuai dengan fungsinya.

    2. Mendefinisikan Request
    Mendefinisikan parameter request (Tipe Data dan Nama Variabel) yang dibutuhkan, request digunakan sebagai data transfer object dari Controller ke Application Layer.

    3. Command
        1. Pada kelas ini berisi logika aplikasi seperti logika validasi input ataupun hal-hal yang perlu di cek sebelum melakukan command.
        
        2. Kemudian menyiapkan data yang akan diinput, lalu memanggil method di dalam repository yang bersangkutan untuk operasi ke basis data. 
        
        3. Kemudian menerima return dari basis data untuk dilakukan pengecekan dan menyiapkan output yang akan dikembalikan ke Controller.

#### Query
    Query pada application layer berisi DTO dan QueryInterface.

    1. Membuat Package
    Membuat package dengan nama yang definitif sesuai dengan fungsinya.

    2. Mendefinisikan DTO
    DTO Merupakan objek yang mendifinisikan properti (kolom) yang menjadi output dari query. Sehingga kita perlu mendfinisikan tipe data dan nama propertinya.

    3. QueryInterface
    Mendifinisikan nama method, parameter yang dibutuhkan dan output yang dihasilkan, untuk tipe data output query merupakan DTO yang telah didefinisikan sebelumnya.

#### Service
Jika query membutuhkan logika yang lebih kompleks, bisa dipisahkan di dalam service ini.

    1. Isi
    Service pada application layer merupakan logika level aplikasi.

    2. Membuat Package
    Membuat package dengan nama yang  sesuai dengan konteks fungsinya secara umum.
    
    3. Ekstraksi fungsi
    Secara praktikal ini merupakan hasil ekstraksi fungsi untuk mengurangi kompleksitas suatu class dan memberikan enkapsulasi

### 6. Mengimplementasikan Infrastructure Layer

#### Persistence
Merupakan Package untuk mengakses basis data atau persistence layer.

##### Repositories
Merupakan kelas konkret untuk melakukan operasi ke basis data (di dalam 1 tabel).

    1. Membuat Kelas Sesuai Dengan Interface
    Membuat kelas yang mengimplementasikan Repository Interface pada Core Layer.

    2. Implementasi Konkrit
    Implementasi method-method yang sudah didefinisikan pada Interface.

    3. Melakukan mapping data sesuai entitas/objeknya.

##### Query
Merupakan kelas konkret untuk melakukan operasi ke basis data (memerlukan lebih dari 1 tabel).

    1. Membuat Kelas Sesuai Dengan Interface
    Membuat kelas yang mengimplementasikan Query Interface pada Application Layer.

    2. Implementasi Konkrit
    Implementasi method-method yang sudah didefinisikan pada Interface.

    3. Melakukan mapping data sesuai Data Transfer Object (DTO).

##### Cache
Merupakan decorator class untuk mengimplementasikan caching.

    1. Membuat Kelas Decorator
    Kelas decorator sesuai dengan nama repository yang di cache.

    2. Membuat Kelas Sesuai Dengan Interface
    Membuat kelas yang mengimplementasikan Repository/Query Interface.

    3. Menambahkan logic cache
    Implementasi logika cache dengan mengambil data pada Repository/Query aslinya.

    4. Melakukan mapping data sesuai entitas/objeknya.

*Binding inteface berbeda dengan Repository/Query Biasa*

#### Service
Merupakan kelas untuk menghubungkan aplikasi dengan infrastructure service di luar aplikasi.

    1. Lokasi Infrastructure Service
    Tempat untuk meletakkan services untuk menghubungkan aplikasi dengan infrastructure service.

    2. Contoh
    Base Connection Service untuk mengakses basis data.
    Base Storage Service untuk mengakses file pada server.

### 7. Mengimplementasikan Controller
Merupakan endpoint API sebagai user interface.

Merupakan endpoint API. Tugasnya hanya menerima dan memvalidasi request, memanggil repository atau service dan mengembalikan hasil ke pengguna.

    1. Validasi Request
    Melakukan validasi Request menggunakan validator.

    2. Menyiapkan Command
    Jika menggunakan command perlu menyiapkan Command (inisiasi dan menyiapkan parameter).

    3. Menyiapkan Service
    Jika menggunakan service perlu menyiapkan service (inisiasi dan menyiapkan parameter).

    4. Menyiapkan Repository/Query
    Jika menggunakan Repository/Query perlu menyiapkan Repository/Query (inisiasi dan menyiapkan parameter).

    5. Mengecek Hasil dan Return
    Melakukan pengecekan hasil Command/Service/Repository/Query. Kemudian mengembalikan hasil sesuai status (HTTP CODE).

### 8. Melakukan Binding Registration
Untuk melakukan Bind Interface dan Concrete Implementation.

    1. Membuat Service Provider
    Membuat Service Provider baru pada /app/providers untuk melakukan binding. Dengan nama DependencyServiceProvider.

    2. Menambahkan Service Provider
    Menambahakan DependencyServiceProvider pada array providers di file config/app.php.

#### Repository/Query Binding
Binding dilakukan dengan fungsi berikut.

```php
$this->app->bind(QueryOrRepositoryInterface::class, ConcreteQueryOrRepository::class);
```

#### Cache Decorator Binding
Binding dilakukan dengan fungsi berikut.

```php
$this->app->bind(QueryOrRepositoryInterface::class, function ($app) {
    return new QueryOrRepositoryCacheDecorator(
        new ConcreteQueryOrRepository
    );
});
```
