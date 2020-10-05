# Deploy Web App Expressjs ke Docker
Melanjutkan pada pembahasan sebelumnya [project web sederhana menggunakan nodejs & express](https://github.com/jokopurwanto/nodejs-express-app). Pada pembahasan kali ini kita akan coba mendeploy aplikasi express ke docker menggunakan project sebelumnya dengan struktur project yang sama

![myApp](https://user-images.githubusercontent.com/32213421/94996237-84d63f00-05cd-11eb-8212-1050c11c321a.PNG)

# Membuat .dockerignore
Buat file baru dengan nama .dockerignore, lalu isikan **node_modules**, **.tmp** dan **.log**, tujuannya untuk mencegah agar file-file tersebut tidak tersalin dan tidak menimpa module yang di install di docker image
```
node_modules
.tmp
.log
```
![dockerignore](https://user-images.githubusercontent.com/32213421/95021390-f29c6c80-069a-11eb-925b-c25dd07b0e6a.PNG)

# Membuat Dockerfile dan Build Image
Buat file baru dengan nama Dockerfile, lalu isikan dengan baris instruksi berikut
```dockerfile
FROM node:lts-alpine
RUN mkdir -p /src/app
WORKDIR /src/app
COPY package*.json ./
RUN npm install
COPY . ./
EXPOSE 3000
CMD [ "npm", "start" ]
```
![Dockerfile](https://user-images.githubusercontent.com/32213421/95021393-f4fec680-069a-11eb-8aa0-ecdc38c43d81.PNG)

* **FROM** instruksi untuk inisialisasi base image, base image yang digunakan node:lts-alpine
* **RUN** instruksi untuk mengeksekusi perintah, perintah yang di eksekusi  
  * mkdir untuk membuat folder baru 
  * npm install untuk menginstal semua dependency yang diperlukan oleh project
* **WORDIR** instruksi untuk mendefinisikan direktori yang digunakan sebagai area kerja, area kerja yang digunakan untuk menaruh source code aplikasi yaitu **/src/app**
* **COPY** instruksi untuk menyalin file atau direktori dari lokal ke system docker image, file atau direktori yang di salin  
  * copy package*.json ./ yaitu menyalin file package.json dan package-lock.json ke WORDIR atau area kerja
  * copy . ./ yaitu menyalin seluruh file dan direktori pada project ke WORDIR atau area kerja
* **EXPOSE** instruksi untuk melisten port, port yang digunakan yaitu 3000
* **CMD** instruksi untuk mengeksekusi perintah. Perbedaan dengan RUN yaitu **RUN** mengeksekusi perintah ketika proses build image baru sedangkan **CMD** mengeksekusi perintah ketika container dijalankan selain itu CMD bisa menspesifikasikan perintah atau parameter. Perintah yang di eksekusi yaitu npm start untuk menjalankan aplikasi

Setelah selesai membuat instruksi Dockerfile, jalankan perintah build image
```
$ docker build -t docker-expressjs:0.0.1 .
```
![docker-build](https://user-images.githubusercontent.com/32213421/95021394-f62ff380-069a-11eb-92f3-e64bde6ed19d.PNG)

* **-t** digunakan untuk membuat nama pengguna (opsional), nama image dan tag dengan format penulisan **“nama-pengguna/nama-image:tag”**

Setelah proses build selesai, lakukan pengecekan docker image dengan perintah
```
$ docker images
```
![docker-images](https://user-images.githubusercontent.com/32213421/95021395-f6c88a00-069a-11eb-86e9-99f10242fd92.PNG)

# Membuat Docker Container
Jalankan image yang telah dibuat pada docker container dengan perintah
```
$ docker run -d --name myExpressApp -p 3000:3000 docker-expressjs:0.0.1
```
![docker-run](https://user-images.githubusercontent.com/32213421/95021397-f7f9b700-069a-11eb-8a33-f9e44c62688a.PNG)
* **-d** digunakan untuk menjalankan container di mode detached
* **--name** digunakan untuk memberikan nama container
* **-p** digunakan untuk mengekspos port ke host dengan format penulisan **"host port":"exposed container port"**

Lalu cek container yang sedang aktif saat ini dengan perintah
```
$ docker ps
```
![docker-run](https://user-images.githubusercontent.com/32213421/95021398-f8924d80-069a-11eb-92c7-38da986c02f7.PNG)

# Test Web Server pada Docker Host
Kemudian test akses docker container yang aktif melalui web browser dengan URL http://localhost:3000, maka akan ditampilkan halaman express project

![testApp](https://user-images.githubusercontent.com/32213421/95021399-f9c37a80-069a-11eb-8bbb-b26034e78f4d.PNG)