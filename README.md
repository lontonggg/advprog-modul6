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

perubahan diatas berfungsi untuk memberikan response kepada browser dengan me-return HTML. `status_line` berfungsi untuk memberikan status bahwa Request HTTP berhasil dengan response kode 200. `contents` akan membaca isi dari file `hello.html` dan diunwrap kedalam bentuk String. Kemudian, `response` akan menyimpan string HTML pada `contents` yang telah diformat kedalam bentuk response, yang kemudian akan ditulis dalam bentuk bytes ke stream melalui method `write_all()`.

![Commit 2 screen capture](/assets/images/commit2.png)
