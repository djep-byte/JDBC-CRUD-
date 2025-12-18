# JDBC-CRUD

<img width="1128" height="1302" alt="image" src="https://github.com/user-attachments/assets/0dd88dea-782c-41c1-b807-ae1220345560" />

<img width="1264" height="932" alt="image" src="https://github.com/user-attachments/assets/dbbd54ba-c973-4b40-a11f-6e63e0ff2207" />

Code SQL:
```
CREATE DATABASE perpustakaan;
USE perpustakaan;

CREATE TABLE buku (
    id_buku INT AUTO_INCREMENT PRIMARY KEY,
    judul VARCHAR(100),
    penulis VARCHAR(100),
    tahun_terbit INT
);
```
Code Java
```
import java.sql.*;
import java.util.Scanner;

public class ManajemenPerpustakaan {
    private static final String JDBC_DRIVER = "com.mysql.cj.jdbc.Driver";
    private static final String DB_URL = "jdbc:mysql://localhost:3306/perpustakaan";
    private static final String USER = "root";
    private static final String PASS = "";
    private static Connection conn = null;
    private static Scanner input = new Scanner(System.in);

    public static void main(String[] args) {
        try {
            Class.forName(JDBC_DRIVER);
            conn = DriverManager.getConnection(DB_URL, USER, PASS);
            System.out.println("Koneksi ke database berhasil!");

            while (true) {
                tampilkanMenu();
            }

        } catch (Exception e) {
            System.err.println("Terjadi kesalahan: ");
            e.printStackTrace();
        } finally {
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    private static void tampilkanMenu() {
        System.out.println("\n=== APLIKASI MANAJEMEN BUKU PERPUSTAKAAN ===");
        System.out.println("1. Tambah Buku");
        System.out.println("2. Tampilkan Semua Buku");
        System.out.println("3. Ubah Data Buku");
        System.out.println("4. Hapus Buku");
        System.out.println("0. Keluar");
        System.out.println("------------------------------------------");
        System.out.print("Pilih menu > ");
        
        String pilihan = input.nextLine();

        switch (pilihan) {
            case "1":
                tambahBuku();
                break;
            case "2":
                tampilkanBuku();
                break;
            case "3":
                ubahBuku();
                break;
            case "4":
                hapusBuku();
                break;
            case "0":
                System.out.println("Terima kasih telah menggunakan aplikasi. Sampai jumpa!");
                System.exit(0);
                break;
            default:
                System.out.println("Pilihan tidak valid. Silakan coba lagi.");
        }
    }

    private static void tambahBuku() {
        System.out.println("\n--- Tambah Buku Baru ---");
        System.out.print("Judul buku: ");
        String judul = input.nextLine();
        System.out.print("Penulis: ");
        String penulis = input.nextLine();
        System.out.print("Tahun terbit: ");
        int tahun = Integer.parseInt(input.nextLine());

        String sql = "INSERT INTO buku (judul, penulis, tahun_terbit) VALUES (?, ?, ?)";

        try (PreparedStatement ps = conn.prepareStatement(sql)) {
            ps.setString(1, judul);
            ps.setString(2, penulis);
            ps.setInt(3, tahun);
            int rowsAffected = ps.executeUpdate();

            if (rowsAffected > 0) {
                System.out.println("Buku berhasil ditambahkan!");
            }
        } catch (SQLException e) {
            System.err.println("Gagal menambah buku.");
            e.printStackTrace();
        }
    }

    private static void tampilkanBuku() {
        String sql = "SELECT * FROM buku ORDER BY id_buku";

        try (Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {

            System.out.println("\n+----+----------------------------+----------------------------+-------");
            System.out.println("| ID | Judul Buku                 | Penulis                    | Tahun |");
            System.out.println("+----+----------------------------+----------------------------+-------");

            while (rs.next()) {
                int id = rs.getInt("id_buku");
                String judul = rs.getString("judul");
                String penulis = rs.getString("penulis");
                int tahun = rs.getInt("tahun_terbit");

                System.out.printf("| %-2d | %-26s | %-26s | %-5d |%n", id, judul, penulis, tahun);
            }

            System.out.println("+----+----------------------------+----------------------------+-------");
        } catch (SQLException e) {
            System.err.println("Gagal menampilkan data buku.");
            e.printStackTrace();
        }
    }

    private static void ubahBuku() {
        System.out.println("\n--- Ubah Data Buku ---");
        System.out.print("Masukkan ID buku yang akan diubah: ");
        int id = Integer.parseInt(input.nextLine());

        System.out.print("Judul baru (kosongkan jika tidak diubah): ");
        String judul = input.nextLine();
        System.out.print("Penulis baru (kosongkan jika tidak diubah): ");
        String penulis = input.nextLine();
        System.out.print("Tahun terbit baru (kosongkan jika tidak diubah): ");
        String tahunStr = input.nextLine();

        String sql = "UPDATE buku SET judul = COALESCE(?, judul), penulis = COALESCE(?, penulis), tahun_terbit = COALESCE(?, tahun_terbit) WHERE id_buku = ?";

        try (PreparedStatement ps = conn.prepareStatement(sql)) {
            ps.setString(1, judul.isEmpty() ? null : judul);
            ps.setString(2, penulis.isEmpty() ? null : penulis);
            ps.setObject(3, tahunStr.isEmpty() ? null : Integer.parseInt(tahunStr));
            ps.setInt(4, id);

            int rowsAffected = ps.executeUpdate();
            if (rowsAffected > 0) {
                System.out.println("Data buku berhasil diubah!");
            } else {
                System.out.println("Buku dengan ID tersebut tidak ditemukan.");
            }
        } catch (SQLException e) {
            System.err.println("Gagal mengubah data buku.");
            e.printStackTrace();
        }
    }

    private static void hapusBuku() {
        System.out.println("\n--- Hapus Buku ---");
        System.out.print("Masukkan ID buku yang akan dihapus: ");
        int id = Integer.parseInt(input.nextLine());

        String sql = "DELETE FROM buku WHERE id_buku = ?";

        try (PreparedStatement ps = conn.prepareStatement(sql)) {
            ps.setInt(1, id);
            int rowsAffected = ps.executeUpdate();

            if (rowsAffected > 0) {
                System.out.println("Buku berhasil dihapus!");
            } else {
                System.out.println("Buku dengan ID tersebut tidak ditemukan.");
            }
        } catch (SQLException e) {
            System.err.println("Gagal menghapus buku.");
            e.printStackTrace();
        }
    }
}
```
