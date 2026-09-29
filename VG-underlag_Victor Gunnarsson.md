# Individuell fördjupning för VG-underlag

## 1. Översikt
- **Syfte:** Redogörelse för alla virtualiserade maskiner, hårdvara, nätverk och användare. Dessa maskiner används för att utforska både Linux och Windows för att skaffa kunskap och färdigheter inom systemdrift.
- **Ägare:** Victor Gunnarsson

---

## 2. Systeminformation

De två maskinerna är virtueliserade i VirtualBox med identisk virtuell hårdvara, det som skiljer sig är storleken på hårddiskarna då Windows 11 kräver större utrymme. 2 kärnor med 8GB tillåter att systemen har en någorlunda bra prestanda.

| Information | Linux | Windows |
| ----------| -------- | ------- |
| Hostname | ubuntuserverlab | WINDOWS11LAB |
| Operativsystem | Ubuntu 26.04 LTS |Microsoft Windows 11 Home|
| Build | ----- | ----- |
| CPU | 2 Kärnor | 2 Kärnor |
| RAM | 8GB | 8 GB |
| Lagring | 25GB virtuell hårddisk | 60 GB virtuell hårddisk |

---

## 3. Nätverksinformation

Båda nätverkskorten är satta till Internal Network i VirtualBox så de har kontakt med varandra, men ingen utanför. IP-adresserna är statiska på maskinerna.

| Information | Linux | Windows |
| ----------| -------- | ------- |
| IP address | 192.168.10.10 | 192.168.10.20 |
| Subnetmask | 255.255.255.0 | 255.255.255.0 |
| MAC-address | 08:00:27:6e:4d:94 | 08-00-27-EB-8B-5C |
| Interface | enp0s3 | Ethernet adapter Ethernet |

Brandväggsinformation:
Windows Echo Reply allowed - Satte igång regeln för att testa anslutning mellan maskinerna. Windows inbyggda brandvägg samt antivirus är igång.

På Ubuntu är UFW igång men inga speciella regler har blivit tillagda.

---

## 4. Användare & grupper

### Linux

- **Lokala användare:**  Victor, bob, alice
- **Lokala grupper:** Ledare (alice), Personal (bob)

### Windows

- **Lokala användare:**  Victor, bob, alice
- **Lokala grupper:** Ledare (alice), Personal (bob)

Lösenord tillhörande användarna finns i extern krypterad fil för referens.