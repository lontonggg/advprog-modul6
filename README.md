## Reyhan Zada Virgiwibowo
## 2206081723
## Advanced Programming - C


### Commit 1 Reflection Notes

Program yang berada pada fungsi `main` berfungsi untuk membaca setiap TCPStream yang masuk dari TCP menggunakan TCPListener. Kemudian untuk setiap stream yang diterima oleh TCPListener akan diolah menggunakan function `handle_connection`. Function `handle_connection` berfungsi untuk mengekstrak header dari TCPStream yang berupa HTTP request dari browser. Lalu, stream akan dibaca melalui `io::BufReader` yang berfungsi untuk membaca setiap line dari TCPStream. Kemudian, method `lines()` digunakan untuk memisahkan setiap lines dari TCPStream. Kemudian, method `map()` dan method `unwrap()` berfungsi untuk mengubah setiap result menjadi bentuk `String`. Selanjutnya, method `take_while()` akan mengambil setiap line hingga baris kosong ditemukan yang menandakan akhir dari TCPStream. Terakhir, method `collect()` digunakan untuk mengumpulkan setiap line yang telah diproses menjadi vektor. Setelah menjalankan semua method diatas, program akan memberikan response yang menunjukkan detail request melalui konsol terminal.


### Commit 2 Reflection Notes

Pada milestone 2, terdapat perubahan pada function `handle_connection` yaitu :

```rust
    let status_line = "HTTP/1.1 200 OK"; 
    let contents = fs::read_to_string("hello.html").unwrap(); 
    let length = contents.len();
    
    let response =
        format!("{status_line}\r\nContent-Length:
{length}\r\n\r\n{contents}");
    
    stream.write_all(response.as_bytes()).unwrap();
```

Perubahan diatas berfungsi untuk memberikan response kepada browser dengan me-return HTML. `status_line` berfungsi untuk memberikan status bahwa Request HTTP berhasil dengan response kode 200. `contents` akan membaca isi dari file `hello.html` dan diunwrap kedalam bentuk String. Kemudian, `response` akan menyimpan string HTML pada `contents` yang telah diformat kedalam bentuk response, yang kemudian akan ditulis dalam bentuk bytes ke stream melalui method `write_all()`.

Screenshot : 
![Commit 2 screen capture](/assets/images/commit2.png)


### Commit 3 Reflection Notes

Pada milestone 3, terdapat tambahan pada function `handle_connection` yaitu :

```rust
    let request_line = buf_reader.lines().next().unwrap().unwrap();

    if request_line == "GET / HTTP/1.1" {
        let status_line = "HTTP/1.1 200 OK";
        let contents = fs::read_to_string("hello.html").unwrap();
        let length = contents.len();

        let response = format!(
            "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
        );

        stream.write_all(response.as_bytes()).unwrap();
    } else {
        let status_line = "HTTP/1.1 404 NOT FOUND";
        let contents = fs::read_to_string("404.html").unwrap();
        let length = contents.len();

        let response = format!(
            "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
        );

        stream.write_all(response.as_bytes()).unwrap();
    }
```

Tambahan kode diatas berfungsi untuk menghandle ketika browser melakukan request ke page yang tidak ada. Hal ini dilakukan dengan menambahkan conditionals yang akan mengecek status dari request. 

`let request_line = buf_reader.lines().next().unwrap().unwrap();` berfungsi untuk membaca path yang direquest oleh browser dimana `next()` berfungsi mengambil line pertama yaitu path yang direquest user, `unwrap()` yang pertama untuk mendapatkan result dari option, dan `unwrap()` selanjutnya untuk mendapatkan String dari result. 

Jika `request_line` memiliki status `GET` ke path `/`, maka server akan memberikan response dengan me-return `hello.html`. Jika request tersebut memiliki status `404 NOT FOUND`, maka server akan memberikan response dengan me-return `404.html`.

Namun, kode tersebut mengandung repetisi pada blok `if` dan `else` dimana kedua blok conditionals tersebut sama-sama membaca stream dan mengembalikan response, hanya berbeda pada file HTML yang di return. Hal ini tentunya melanggar prinsip DRY (Don't Repeat Yourself). Kode tersebut dapat di-refactor menjadi :

```rust
    let (status_line, filename) = if request_line == "GET / HTTP/1.1" {
        ("HTTP/1.1 200 OK", "hello.html")
    } else {
        ("HTTP/1.1 404 NOT FOUND", "404.html")
    };

    let contents = fs::read_to_string(filename).unwrap();
    let length = contents.len();

    let response =
        format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");

    stream.write_all(response.as_bytes()).unwrap();
```

Screenshot :
![Commit 3 screen capture](/assets/images/commit3.png)


### Commit 4 Reflection Notes

Pada milestone 4, terdapat perubahan pada function `handle_connection` tepatnya pada perubahan di blok conditionals yang awalnya menggunakan if else menjadi menggunakan match dimana match akan mencari string yang cocok didalam curly bracketsnya dan menjalankan program yang sesuai dengannya. Lalu, terdapat juga pembuatan path request baru yaitu `GET /sleep HTTP/1.1` yang memungkinkan user untuk membuka page `/sleep`. Namun, ketika terjadi request ke page `/sleep`, maka akan terjadi delay karena adanya kode `thread::sleep(Duration::from_secs(10)); ("HTTP/1.1 200 OK", "hello.html");` yang akan memberhentikan program selama 10 detik sebelum kemudian dijalankan kembali. Dengan demikian, ketika ada user yang melakukan request ke page `/sleep`, user yang membuka page `/` akan mengalami delay jika proses pemberhentian program selama 10 detik yang disebabkan oleh request ke `/sleep` belum selesai.


### Commit 5 Reflection Notes

Pada milestone 5, program yang awalnya `single-threaded` akan diubah menjadi `multi-threaded` agar membuat proses handling request dari banyak user menjadi lebih efisien. Dalam proses ini, `Worker` perlu dibuat yang akan mengerjakan job atau task yang sesuai untuk setiap request. Lalu, kita juga perlu membuat `sender` pada `ThreadPool` dan `receiver` pada setiap worker. Dengan demikian, setiap ada request `execute` pada ThreadPool, `sender` akan mengirimkan sinyal ke setiap receiver yang telah diassign pada worker untuk melakukan jobnya. Kemudian, setiap worker memiliki 1 thread yang akan menunggu data. Ketika data masuk, worker akan me-lock receivernya dan memproses data (melakukan job). Setelah job selesai, worker akan melepas lock tersebut agar worker dapat kembali menerima informasi dari receiver.


### Commit Bonus Reflection Notes

Pada bagian bonus, fuction `ThreadPool::new` akan diganti dengan function `ThreadPool::build`. Hal ini disebabkan oleh function `ThreadPool::new` yang akan selalu berjalan dengan mengasumsikan bahwa size parameter selalu valid, jika size parameter tidak valid maka akan terjadi `panic` dan function tidak dapat menghandle error. Oleh karena itu, function `ThreadPool::build` lebih disarankan dalam instansiasi ThreadPool karena bisa menghandle error jika size parameter yang diberikan tidak valid (size <= 0). Function build ini diawali dengan signature `pub fn build(size: usize) -> Result<ThreadPool, PoolCreationError>`. Function tersebut akan mereturn `Result`, jika berhasil akan mereturn `Ok` yang berisi instance dari `ThreadPool` dan jika gagal akan mereturn `Err` yang berisi `PoolCreationError`.