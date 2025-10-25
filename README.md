# Portofolio SQL: Sistem Basis Data Sekolah

## Deskripsi Proyek
Proyek ini bertujuan untuk membuat sistem basis data sederhana untuk manajemen sekolah, termasuk siswa, guru, kelas, dan registrasi.  
Fokus utama:
- Menyimpan data siswa, guru, dan kelas
- Mengelola registrasi siswa ke kelas
- Menghubungkan guru dengan kelas (wali kelas)
- Melakukan analisis dasar dan query kompleks

**Teknologi:** SQL (SQLite/MySQL/PostgreSQL)  
**Kategori:** Basis Data Relasional, Query Analitis

---

## Struktur Tabel

| Tabel       | Fungsi |
|-------------|--------|
| `STUDENT`   | Menyimpan data siswa (ID, nama, umur, kewarganegaraan) |
| `CLASS`     | Menyimpan data kelas (ID, nama kelas) |
| `REGISTRATION` | Menghubungkan siswa dengan kelas (many-to-many) |
| `TEACHER`   | Menyimpan data guru (ID, nama, umur) |
| `WALI`      | Menghubungkan guru dengan kelas (wali kelas) |

---

## Query dan Insight

- **Menampilkan semua siswa:** Memudahkan cek integritas data awal
- **Filter siswa berdasarkan kewarganegaraan dan umur:** Analisis demografi siswa
- **Mengurutkan siswa berdasarkan umur:** Mempermudah analisis kelompok usia
- **Analisis rata-rata umur per kewarganegaraan:** Membandingkan kelompok siswa
- **Menampilkan siswa per kelas:** Mempermudah laporan pendaftaran siswa
- **Menghitung jumlah siswa per guru:** Mengevaluasi beban guru
- **Analisis umur siswa tertua per guru:** Mengetahui distribusi umur di bawah pengawasan guru tertentu

---

## Insight Keseluruhan
- Memahami struktur relational database
- Melakukan query analitis (count, avg, max)
- Menghubungkan banyak tabel untuk laporan kompleks
- Menjaga integritas data melalui update dan validasi




-- 🏫 Tabel STUDENT
CREATE TABLE STUDENT(
    ID INTEGER PRIMARY KEY,
    FIRSTNAME CHAR(20),
    LASTNAME CHAR(20),
    AGE INTEGER CHECK (AGE > 6 AND AGE < 13),
    'NATIONALITY' CHAR(20) DEFAULT 'INDONESIA');

INSERT INTO Student VALUES
(1, 'John', 'Wick', 8, 'Malaysia'),
(2, 'David', 'Beckham', 7, 'England'),
(3, 'Michael', 'Owen', 9 , 'Inbdonesia'),
(4, 'Cristiano', 'Ronaldo', 8, 'England'),
(5, 'Lionel', 'Messi', 8, 'Malaysia'),
(6, 'David', 'Villa', 10, 'Indonesia'),
(7, 'John', 'Terry', 7, 'Malaysia'),
(8, 'Michael', 'Jordan', 12, 'Indonesia'),
(9, 'John', 'Wick', 8, 'Malaysia'),
(10, 'John', 'Wick', 8, 'England');

-- 🏫 Tabel CLASS
CREATE TABLE CLASS (ID PRIMARY KEY , NAME CHAR(20));

INSERT INTO Class VALUES
(1001, 'Class A'),
(1002, 'Class B'),
(1003, 'Class C');

-- 🧾 Tabel REGISTRATION
CREATE TABLE REGISTRATION (STUDENTID INTEGER , CLASSID INTEGER , PRIMARY KEY ( STUDENTID,CLASSID) FOREIGN KEY (STUDENTID) REFERENCES STUDENT(ID), FOREIGN KEY (CLASSID) REFERENCES CLASS (ID));

INSERT INTO Registration VALUES
(1, 1001),
(2, 1001),
(3, 1001),
(4, 1002),
(5, 1002),
(6, 1002),
(7, 1003),
(8, 1003),
(9, 1003),
(10, 1001);

-- 👨‍🏫 Tabel TEACHER
CREATE TABLE TEACHER (ID  INTEGER PRIMARY KEY, FIRSTNAME CHAR(20), LASTNAME CHAR(20), AGE INTEGER CHECK (AGE < 60));

INSERT INTO Teacher VALUES
(201, 'Luois', 'Figo', 40),
(202, 'Alex', 'Ferguson', 59),
(203, 'Arsene', 'Wenger', 55);

-- 🧑‍🏫 Tabel WALI
CREATE TABLE WALI ( CLASSID INTEGER , TEACHERID INTEGER , PRIMARY KEY (CLASSID , TEACHERID), FOREIGN KEY (CLASSID) REFERENCES CLASS (ID), FOREIGN KEY (TEACHERID) REFERENCES TEACHER (ID));

INSERT INTO Wali VALUES
(1001, 201),
(1002, 202),
(1003, 203);

-- 🔄 Update & Query Analisis
UPDATE STUDENT SET NATIONALITY = 'INDONESIA' WHERE ID = 3;
SELECT * FROM STUDENT; 

SELECT FIRSTNAME,LASTNAME FROM STUDENT WHERE NATIONALITY = 'Indonesia';
UPDATE STUDENT SET NATIONALITY = 'Indonesia' WHERE ID =3;

SELECT * FROM STUDENT ORDER BY AGE DESC;
SELECT * FROM STUDENT ORDER BY AGE;

SELECT * FROM STUDENT WHERE NOT NATIONALITY = "England" AND AGE > 7 ORDER BY ID;

SELECT COUNT(NATIONALITY) FROM STUDENT WHERE NATIONALITY = 'Malaysia' or NATIONALITY = 'England';
SELECT AVG(AGE) FROM STUDENT WHERE NATIONALITY = 'Malaysia' or NATIONALITY = 'England';

SELECT DISTINCT(NATIONALITY) FROM STUDENT;
SELECT NATIONALITY,AVG(AGE) FROM STUDENT GROUP BY NATIONALITY;
SELECT NATIONALITY , AVG(AGE) AS AVERAGE_AGE FROM STUDENT GROUP BY NATIONALITY HAVING COUNT(ID) > 3; 

SELECT STUDENT.FIRSTNAME, STUDENT.LASTNAME FROM STUDENT,REGISTRATION WHERE STUDENT.ID = REGISTRATION.STUDENTID AND REGISTRATION.CLASSID =1002;

SELECT STUDENT.ID AS studentid , STUDENT.FIRSTNAME AS firstname , STUDENT.LASTNAME AS lastname , CLASS.ID as classid   
FROM STUDENT , REGISTRATION  , CLASS 
WHERE STUDENT.ID = REGISTRATION.STUDENTID 
AND CLASS.ID = REGISTRATION.CLASSID 
AND CLASS.NAME = 'Class C';

SELECT TEACHER.FIRSTNAME AS firstname , TEACHER.LASTNAME AS lastname, COUNT(STUDENT.ID) AS StudentCount 
FROM STUDENT,TEACHER,  WALI , CLASS , REGISTRATION 
WHERE TEACHER.ID = WALI.TEACHERID 
AND CLASS.ID = WALI.CLASSID 
AND STUDENT.ID = REGISTRATION.STUDENTID 
AND REGISTRATION.CLASSID = CLASS.ID 
GROUP BY TEACHER.ID; 

SELECT  MAX(STUDENT.AGE) AS StudentAge 
FROM STUDENT,TEACHER,WALI,CLASS,REGISTRATION 
WHERE TEACHER.ID = WALI.TEACHERID 
AND CLASS.ID = WALI.CLASSID 
AND STUDENT.ID = REGISTRATION.STUDENTID 
AND REGISTRATION.CLASSID = CLASS.ID 
AND TEACHER.FIRSTNAME = 'Luois' 
AND TEACHER.LASTNAME = 'Figo';
