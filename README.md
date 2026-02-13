# 💊 PharmaChain
### Blockchain Tabanlı İlaç Tedarik Zinciri Takip Sistemi

<p align="center">
  <img src="https://img.shields.io/badge/Solidity-0.8.20-363636?style=for-the-badge&logo=solidity" />
  <img src="https://img.shields.io/badge/React-18.2.0-61DAFB?style=for-the-badge&logo=react" />
  <img src="https://img.shields.io/badge/Ethers.js-6.9.0-3C3C3D?style=for-the-badge&logo=ethereum" />
  <img src="https://img.shields.io/badge/Hardhat-2.x-F5DE19?style=for-the-badge" />
  <img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge" />
</p>

---

## 📖 Proje Hakkında

PharmaChain, ilaç tedarik zincirini uçtan uca takip eden, **Ethereum blockchain** üzerine inşa edilmiş merkezi olmayan bir uygulamadır (dApp). Her ilacın üretimden satışa kadar geçtiği her adım blockchain üzerinde değiştirilemez biçimde kaydedilir.

**Temel amaç:** Sahte ilaçları önlemek, tedarik zincirini şeffaf hale getirmek ve her ilacın geçmişini herkesin doğrulayabileceği bir sisteme kavuşturmak.

---

## ✨ Özellikler

### 🔐 Rol Tabanlı Erişim Kontrolü
| Rol | Yetki |
|---|---|
| 👑 **Admin (Owner)** | Kullanıcı ekle/kaldır, sistem yönetimi |
| 🏭 **Üretici (Manufacturer)** | İlaç üret, dağıtıcıya gönder |
| 🚚 **Dağıtıcı (Distributor)** | İlaç al, eczaneye gönder |
| 💊 **Eczacı (Retailer)** | İlaç al, müşteriye sat |

### 📦 Akıllı Kontrat Özellikleri
- **Tekil & Toplu Üretim** — Tek MetaMask onayı ile 1-500 arası ilaç üretimi
- **Toplu Transfer** — Tek onay ile onlarca ilacı dağıtıcıya/eczaneye gönder
- **Toplu Satış** — Tek onay ile birden fazla ilacı sat
- **Değiştirilemez Kayıt** — Her işlem blockchain'de kalıcı

### 🖥️ Arayüz Özellikleri
- **📊 Analytics Dashboard** — Canlı istatistikler, Pie & Bar grafikler
- **📱 QR Kod** — Her ilaç için benzersiz QR kod ile doğrulama
- **🗓️ Timeline** — İlacın üretimden satışa geçmişi adım adım
- **⚠️ SKT Uyarıları** — Son kullanma tarihi yaklaşan ilaçlar için otomatik uyarı
- **🔍 Arama & Filtre** — İlaç adı, seri no, durum bazlı filtreleme
- **🎴 Tablo / Kart Görünümü** — Tercihine göre değiştirilebilir görünüm
- **☑️ Çoklu Seçim** — Checkbox ile toplu işlem desteği

---

## 🏗️ Proje Yapısı

```
pharmachain/
│
├── contracts/
│   ├── Owner.sol                    # Sahiplik yönetimi
│   ├── access/
│   │   └── Roles.sol               # Rol tabanlı erişim kontrolü
│   └── MedicineTracker.sol         # Ana iş mantığı
│
├── scripts/
│   └── deploy.js                   # Deploy scripti
│
└── src/
    ├── App.js                      # Ana uygulama
    ├── App.css                     # Stiller
    ├── MedicineTracker.json        # Kontrat ABI
    └── components/
        ├── Dashboard.js            # Analitik dashboard
        ├── Dashboard.css
        ├── MedicineDetailModal.js  # İlaç detay & QR kod
        └── MedicineDetailModal.css
```

---

## 🔄 İş Akışı

```
🏭 Üretici          🚚 Dağıtıcı          💊 Eczacı
    │                    │                    │
    │  produceMedicine   │                    │
    │─────────────────►  │                    │
    │                    │                    │
    │ transferToDistrib. │                    │
    │───────────────────►│                    │
    │                    │  transferToRetail  │
    │                    │───────────────────►│
    │                    │                    │  sellMedicine
    │                    │                    │─────────────► ✅ Satıldı

[Manufactured]      [ToDistributor]      [ToRetailer]        [Sold]
```

---

## 🚀 Kurulum

### Gereksinimler
- Node.js >= 16
- MetaMask tarayıcı eklentisi

### 1. Repoyu Klonla

```bash
git clone https://github.com/kullanici-adin/pharmachain.git
cd pharmachain
```

### 2. Bağımlılıkları Yükle

```bash
npm install
```

### 3. Hardhat Local Node Başlat

```bash
npx hardhat node
```

> Bu komut size test hesapları ve private key'leri verecektir. İlk hesap otomatik olarak **Admin** olur.

### 4. Contract'ı Deploy Et

```bash
# Yeni terminal aç
npx hardhat run scripts/deploy.js --network localhost
```

Deploy çıktısı:
```
✅ MedicineTracker deployed to: 0xADRES_BURAYA
```

### 5. Contract Adresini Güncelle

`src/App.js` dosyasında:
```javascript
const CONTRACT_ADDRESS = "0xBURAYA_YAPISTIR";
```

### 6. ABI Dosyasını Güncelle

```bash
cp artifacts/contracts/MedicineTracker.sol/MedicineTracker.json src/MedicineTracker.json
```

### 7. Uygulamayı Başlat

```bash
npm start
```

Uygulama `http://localhost:3000` adresinde açılır.

---

## ⚙️ MetaMask Yapılandırması

1. MetaMask'ı açın → **Ağ Ekle** → Manuel giriş:

| Alan | Değer |
|---|---|
| Ağ Adı | Hardhat Local |
| RPC URL | `http://127.0.0.1:8545` |
| Chain ID | `31337` |
| Para Birimi | ETH |

2. Hardhat'in verdiği private key ile hesabı içe aktarın:
**MetaMask → Hesap İçe Aktar → Private Key yapıştır**

---

## 📋 Kontrat Fonksiyonları

### Üretici
```solidity
produceMedicine(string name, uint256 batchNumber, uint256 expiryDate)
batchProduceMedicines(string name, uint256 batchNumber, uint256 expiryDate, uint256 quantity) // maks. 500
transferToDistributor(uint256 medicineId, address distributor)
batchTransferToDistributor(uint256[] medicineIds, address distributor) // maks. 100
```

### Dağıtıcı
```solidity
transferToRetailer(uint256 medicineId, address retailer)
batchTransferToRetailer(uint256[] medicineIds, address retailer) // maks. 100
```

### Eczacı
```solidity
sellMedicine(uint256 medicineId)
batchSellMedicines(uint256[] medicineIds) // maks. 100
```

### Admin
```solidity
addManufacturer(address)  |  removeManufacturer(address)
addDistributor(address)   |  removeDistributor(address)
addRetailer(address)      |  removeRetailer(address)
transferOwnership(address)
```

---

## 🧪 Hızlı Test Senaryosu

```
1. Account 0 (Admin) ile bağlan
   → Account 1'i Manufacturer olarak ekle
   → Account 2'yi Distributor olarak ekle
   → Account 3'ü Retailer olarak ekle

2. Account 1 (Manufacturer) ile bağlan
   → 50 adet Aspirin üret → yalnızca 1 MetaMask onayı!
   → Tüm ilaçları checkbox ile seç
   → Account 2'ye toplu gönder → yalnızca 1 MetaMask onayı!

3. Account 2 (Distributor) ile bağlan
   → Tüm ilaçları seç → Account 3'e toplu gönder (1 onay)

4. Account 3 (Retailer) ile bağlan
   → Tüm ilaçları toplu sat (1 onay) ✅
```

---

## 🛠️ Kullanılan Teknolojiler

| Teknoloji | Kullanım |
|---|---|
| **Solidity 0.8.20** | Akıllı kontrat geliştirme |
| **Hardhat** | Geliştirme ortamı & deploy |
| **React 18** | Kullanıcı arayüzü |
| **Ethers.js v6** | Blockchain iletişimi |
| **MetaMask** | Cüzdan & işlem imzalama |
| **Recharts** | Dashboard grafikleri |
| **qrcode.react** | QR kod üretimi |

---

## 🔒 Güvenlik

- Tüm kritik fonksiyonlar `modifier` ile korunmaktadır (`onlyOwner`, `onlyManufacturer` vb.)
- Batch işlemlerde maksimum sınırlar uygulanmıştır (gas limit aşımını önler)
- Her transfer blockchain üzerinde kalıcı ve değiştirilemez olarak kaydedilir
- Adres doğrulaması frontend tarafında `ethers.isAddress()` ile yapılmaktadır

---

## 🗺️ Gelecek Planlar

- [ ] Testnet (Sepolia) deploy
- [ ] IPFS ile sertifika & belge saklama
- [ ] Mobil QR okuyucu ile herkese açık ilaç doğrulama sayfası
- [ ] Dark mode
- [ ] Çoklu dil desteği (TR / EN)
- [ ] Email / push bildirimleri

---

## 📄 Lisans

Bu proje [MIT](LICENSE) lisansı altında lisanslanmıştır.

---

<p align="center">
  Geliştirici: <strong>@kullanici-adin</strong> &nbsp;|&nbsp; 💊 <strong>PharmaChain</strong> — Sağlığınız için şeffaf bir tedarik zinciri
</p>
