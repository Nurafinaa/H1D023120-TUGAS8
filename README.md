# Aplikasi Flutter – Login & CRUD Produk

Aplikasi ini dibuat menggunakan: Flutter, API CodeIgniter 4, dan Metode komunikasi REST API (Login, Registrasi, CRUD Produk)

# 1. Proses Login
a. Form Login
Screenshot:
![Login Page](screenshots/login_page.png)
Penjelasan:
User menginputkan email dan password, lalu menekan tombol Login. Data ini akan dikirimkan ke API CodeIgniter untuk diverifikasi.

Potongan Kode Login
```LoginBloc.login(
  email: _emailTextboxController.text,
  password: _passwordTextboxController.text,
).then((value) async {
  if (value.code == 200) {
    await UserInfo().setToken(value.token.toString());
    await UserInfo().setUserID(int.parse(value.userID.toString()));
    Navigator.pushReplacement(
      context,
      MaterialPageRoute(builder: (context) => const ProdukPage()),
    );
  } else {
    showDialog(
      context: context,
      builder: (context) => const WarningDialog(
        description: "Login gagal, silahkan coba lagi",
      ),
    );
  }
});
```

b. Jika Login Gagal / Berhasil
Screenshot:
![Warning Dialog](screenshots/login_failed.png)
Jika API mengembalikan status gagal, maka aplikasi akan menampilkan dialog peringatan menggunakan WarningDialog.

# 2. Proses Registrasi
a. Halaman Registrasi

Screenshot form registrasi
![Registrasi Page](screenshots/register_page.png)
Penjelasan: User memasukkan nama, email, password, lalu data dikirim ke API.

Kode Registrasi
```RegistrasiBloc.registrasi(
  nama: _namaTextboxController.text,
  email: _emailTextboxController.text,
  password: _passwordTextboxController.text,
).then((value) {
  showDialog(
    context: context,
    builder: (context) => SuccessDialog(
      description: "Registrasi berhasil, silahkan login",
    ),
  );
});
```
# 3. Halaman List Produk

Screenshot halaman produk
![Produk Page](screenshots/produk_page.png)
Penjelasan: Halaman ini menampilkan daftar produk dari API

Diambil menggunakan 
```ProdukBloc.getProduks()

Kode FutureBuilder
body: FutureBuilder<List>(
  future: ProdukBloc.getProduks(),
  builder: (context, snapshot) {
    return snapshot.hasData
        ? ListProduk(list: snapshot.data)
        : const Center(child: CircularProgressIndicator());
  },
),
```
# 4. Proses Tambah Produk
a. Form Tambah Produk

Screenshot form tambah
![Form Produk](screenshots/add_form.png)
Penjelasan: User mengisi kode, nama, harga, lalu menekan tombol Simpan.

Kode Simpan Produk
```Produk createProduk = Produk(id: null);
createProduk.kodeProduk = _kodeProdukTextboxController.text;
createProduk.namaProduk = _namaProdukTextboxController.text;
createProduk.hargaProduk = int.parse(_hargaProdukTextboxController.text);

ProdukBloc.addProduk(produk: createProduk);
```

# 5. Proses Edit Produk

Screenshot halaman edit
![Edit Produk](screenshots/edit_form.png)
Penjelasan: Saat user klik EDIT, aplikasi membuka form yang sudah terisi data lama.

Kode Ubah Produk
```Produk updateProduk = Produk(id: widget.produk!.id!);
updateProduk.kodeProduk = _kodeProdukTextboxController.text;
updateProduk.namaProduk = _namaProdukTextboxController.text;
updateProduk.hargaProduk = int.parse(_hargaProdukTextboxController.text);

ProdukBloc.updateProduk(produk: updateProduk);
```
# 6. Proses Hapus Produk
a. Popup Konfirmasi Hapus

Screenshot popup hapus
![Delete Dialog](screenshots/delete_confirm.png)
Penjelasan: Sebelum hapus, aplikasi minta konfirmasi user.

Kode Hapus
```ProdukBloc.deleteProduk(id: int.parse(widget.produk!.id!)).then((value) {
  Navigator.of(context).push(
    MaterialPageRoute(builder: (context) => const ProdukPage()),
  );
});
```

# 7. Logout
Screenshot drawer logout
→ ![Logout](screenshots/logout.png)

Kode Logout
```await LogoutBloc.logout().then((value) {
  Navigator.of(context).pushAndRemoveUntil(
    MaterialPageRoute(builder: (context) => LoginPage()),
    (route) => false,
  );
});
```
# 8. Setting API URL di Flutter

File: lib/helpers/api_url.dart

```class ApiUrl {
  static const String baseUrl = "http://192.168.1.15:8080";

  static const String login = "$baseUrl/login";
  static const String registrasi = "$baseUrl/registrasi";

  static const String produk = "$baseUrl/produk";
  static String updateProduk(int id) => "$baseUrl/produk/$id";
  static String deleteProduk(int id) => "$baseUrl/produk/$id";
}
```
# 9. Cara Menjalankan
**CI4:<\br>**
php spark serve --host 192.168.1.15

**Flutter (Chrome):<\br>**
flutter run -d chrome --web-browser-flag "--disable-web-security"
