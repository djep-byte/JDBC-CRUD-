# JDBC-CRUD

<img width="1128" height="1302" alt="image" src="https://github.com/user-attachments/assets/0dd88dea-782c-41c1-b807-ae1220345560" />

<img width="1264" height="932" alt="image" src="https://github.com/user-attachments/assets/dbbd54ba-c973-4b40-a11f-6e63e0ff2207" />

Code SQL:
```CREATE DATABASE perpustakaan;
USE perpustakaan;

CREATE TABLE buku (
    id_buku INT AUTO_INCREMENT PRIMARY KEY,
    judul VARCHAR(100),
    penulis VARCHAR(100),
    tahun_terbit INT
);
