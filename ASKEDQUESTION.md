# Asked Question

## 1. Mengapa properti interface pada Controller ditempatkan pada parameter Constructor?

### Contoh Kasus
```php
class MyController extends BaseApiController
{
    public function __construct(
        private UserRepositoryInterface $userRepository,
        private MyEntityRepositoryInterface $myEntityRepository,
        private SomeDataQueryInterface $someDataQuery,
    )
    {}
}
```

Pada baris kode di atas terdapat 3 parameter yang dibutuhkan untuk menginisiasi class `MyController` yaitu interface dari repository ataupun query.

### Jawaban

Hal tersebut merupakan *dependency injection*, parameter tersebut digunakan sebagai pintu masuk injeksi dependesi oleh framework.

#### Repository/Query Binding

Binding dilakukan dengan fungsi berikut.

```php
$this->app->bind(QueryOrRepositoryInterface::class, ConcreteQueryOrRepository::class);
```

Sebelumnya kita sudah melakukan binding antara interface dan concrete class, hal ini untuk memberi tahu framework concrete class mana yang akan kita gunakan ketika memiliki dependency sebuah interface (Parameter di dalam Constructor).

#### Tujuan 1

Dengan begitu controller bisa independen dan keseluruhan kode bisa modular. Contohnya pada kasus kita memiliki lebih dari satu concrete class seperti di bawah ini.

```php
if (App::environment('testing'))
{
    $this->app->bind(QueryOrRepositoryInterface::class, MockQueryOrRepository::class);
}

if (App::environment('production'))
{
    $this->app->bind(QueryOrRepositoryInterface::class, ConcreteQueryOrRepository::class);
}
```

Kode di atas membantu kita menentukan concrete class mana yang akan digunakan berdasarkan kondisi env dari sistem yang sedang berjalan.

#### Tujuan 2

Mematuhi prinsip SOLID pada bagian dependency inversion principle.