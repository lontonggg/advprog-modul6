## Reyhan Zada Virgiwibowo
## 2206081723
## Advanced Programming - C

### Commit 1 Reflection Notes

Program yang berada pada fungsi `main` berfungsi untuk membaca setiap TCPStream yang masuk dari TCP menggunakan TCPListener. Kemudian untuk setiap stream yang diterima oleh TCPListener akan diolah menggunakan function `handle_connection`. Function `handle_connection` berfungsi untuk mengekstrak header dari TCPStream yang berupa HTTP request dari browser. Lalu, stream akan dibaca melalui `io::BufReader` yang berfungsi untuk membaca setiap line dari TCPStream. Kemudian, method `lines()` digunakan untuk memisahkan setiap lines dari TCPStream. Kemudian, method `map()` dan method `unwrap()` berfungsi untuk mengubah setiap result menjadi bentuk `String`. Selanjutnya, method `take_while()` akan mengambil setiap line hingga baris kosong ditemukan yang menandakan akhir dari TCPStream. Terakhir, method `collect()` digunakan untuk mengumpulkan setiap line yang telah diproses menjadi vektor. Setelah menjalankan semua method diatas, program akan memberikan response yang menunjukkan detail request melalui konsol terminal.

