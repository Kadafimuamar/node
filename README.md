1️⃣ Persiapan Server

🔹 Spesifikasi Minimum untuk Full Node Stargaze:

✅ CPU: 4-core atau lebih tinggi

✅ RAM: 16GB RAM (lebih tinggi lebih baik)

✅ Penyimpanan: SSD 1TB (karena blockchain terus bertambah)

✅ OS: Ubuntu 20.04 LTS (disarankan)

✅ Koneksi Internet: Stabil dan cepat (minimal 100 Mbps)

💡 Gunakan VPS atau Dedicated Server:
Hetzner (https://www.hetzner.com/)
DigitalOcean (https://www.digitalocean.com/)
AWS, Google Cloud, atau Azure


2️⃣ Instalasi Dependensi & Stargaze Node

🔹   Update Sistem dan Install Paket Pendukung
Jalankan perintah berikut di terminal server:

    ```
     sudo apt update && sudo apt upgrade -y
     sudo apt install -y build-essential jq wget curl git
     

🔹 Install Go (Golang)

   Stargaze membutuhkan Go untuk menjalankan node. Install versi terbaru:

    ```
    wget https://go.dev/dl/go1.21.0.linux-amd64.tar.gz
    sudo rm -rf /usr/local/go
    sudo tar -C /usr/local -xzf go1.21.0.linux-amd64.tar.gz
    echo "export PATH=$PATH:/usr/local/go/bin" >> ~/.bashrc
    source ~/.bashrc
    go version  # cek apakah sudah terinstall


3️⃣ Clone dan Build Stargaze Full Node
   Clone repository Stargaze dan build binary node-nya:

    ```
    cd $HOME
    git clone https://github.com/public-awesome/stargaze
    cd stargaze
    git checkout v10.0.0  # Gunakan versi terbaru
    make install

   
  Setelah ini, binary starsd akan tersedia. Cek apakah sudah berhasil dengan:

    starsd version


4️⃣ Setup Node dan Konfigurasi Private RPC
🔹 Inisialisasi Node
    ```
    starsd init myprivaterpc --chain-id stargaze-1
    
🔹 Unduh Genesis File & Addrbook
    
    ```
    curl -s https://rpc.stargaze.network/genesis | jq .result.genesis > ~/.starsd/config/genesis.json

Unduh Addrbook untuk mempercepat sinkronisasi node:
  
    ```
    wget -O ~/.starsd/config/addrbook.json https://rpc.stargaze.network/addrbook.json

🔹 Konfigurasi Peer & RPC
Edit file konfigurasi config.toml untuk menggunakan RPC pribadi:

    nano ~/.starsd/config/config.toml

Cari bagian:

    ```
    persistent_peers = ""
    seeds = ""

Tambahkan seed node Stargaze:
  
    ```
    seeds = "2d0e6044...@seed.stargaze.network:26656"
    
Edit app.toml untuk mengaktifkan RPC private:

    nano ~/.starsd/config/app.toml

    
Cari [rpc] dan ubah:

    [rpc]
    laddr = "tcp://0.0.0.0:26657"  # Pastikan terbuka untuk akses eksternal
    cors_allowed_origins = ["*"]   # Bisa diatur hanya untuk IP tertentu
    

5️⃣ Jalankan dan Sinkronisasi Node
Setelah konfigurasi selesai, jalankan node dengan:

    starsd start --rpc.laddr tcp://0.0.0.0:26657

Catatan: Sinkronisasi bisa memakan waktu beberapa jam atau bahkan hari tergantung koneksi internet dan kapasitas server.

6️⃣ Cek Status Node & RPC
Setelah sinkronisasi selesai, cek status node:
      
    starsd status

Cek apakah RPC sudah berjalan:

    curl http://localhost:26657/status
    
Jika ingin mengakses dari luar server, gunakan:

    curl http://[IP_SERVER_ANDA]:26657/status
    

7️⃣ Membuat Private RPC Endpoint
Agar Private RPC bisa digunakan oleh Anda sendiri atau bot minting, pastikan hanya IP tertentu yang bisa mengaksesnya.
Edit file app.toml lagi dan batasi akses dengan:

    cors_allowed_origins = ["http://yourwebsite.com"]

Atau, gunakan firewall untuk membatasi akses:

    sudo ufw allow from YOUR_IP to any port 26657
    sudo ufw enable

8️⃣ Gunakan Private RPC di Metamask atau Bot
Setelah RPC aktif, Anda bisa menggunakan endpoint pribadi Anda di Metamask atau bot minting NFT.
📌 Contoh Endpoint RPC:

    http://YOUR_SERVER_IP:26657
    
Tambahkan ke Metamask dengan:

Network Name: Stargaze Private RPC
RPC URL: http://YOUR_SERVER_IP:26657
Chain ID: stargaze-1
