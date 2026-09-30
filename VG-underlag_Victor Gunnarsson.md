# Individuell fördjupning för VG-underlag

## Översikt
- **Syfte:** Detta dokument är indelat i tre moment, moment A förklarar ett pakets väg från en klient till en server. Moment B redogör hur filrättigheter fungerar i Linux kontra Windows. Moment C är en teknisk dokumentation som går igenom de virtualiserade maskinerna, hårdvara & mjjukvara samt nätverk och användare. Dessa maskiner används för skaffa kunskap och färdigheter inom systemdrift.
- **Ägare:** Victor Gunnarsson

## Moment A - Hur ett paket färdas från klient till server



## Moment B - Filrättigheter i Linux & Windows

### Linux

Först skapar jag grupperna *g_ledare* och *g_personal* samt användarna *alice* och *bob* och sätter lösenord på dem.
```
sudo groupadd g_ledare
sudo groupadd g_personal
sudo useradd -m -s /bin/bash alice
sudo passwd alice
sudo useradd -m -s /bin/bash bob
sudo passwd bob
```

Sedan lägger jag till *alice* till *g_ledare* och *bob* till *g_personal*
```
sudo usermod -aG g_ledare alice
sudo usermod -aG g_personal bob
```
Bekräfta med groups om de har blivit tillagda korrekt.
```
groups alice
groups bob
```

Skapa katalogerna:
```
sudo mkdir -p /Projekt/Gemensamt
sudo mkdir /Projekt/Ledning
```
Ändra ägare samt sätt rättigheter:
```
sudo chown :g_ledare /Projekt/Ledning
sudo chmod 777 /Projekt/Gemensamt
sudo chmod 670 /Projekt/Ledning
```
Nu kan alice kan skapa en fil i Ledning och ändra men bob kan inte komma åt filen eller katalogen. Både alice och bob kan skapa och ändra filer i katalogen gemensamt.


### Windows

Alla kommandom är körda i Powershell som admin, annars blir det felmeddelande att kommandot inte går att köra.

Först så skapar jag grupperna *g_ledare* och *g_personal*:
```
New-LocalGroup -Name "g_ledare" -Description "Ledargrupp"
New-LocalGroup -Name "g_personal" -Description "Personalgrupp"
```
Sedan skapa användarna *Alice* och *Bob* med lösenord:
```
New-LocalUser -Name "Alice" -Password (Read-Host -AsSecureString "Lösenord")
New-LocalUser -Name "Bob" -Password (Read-Host -AsSecureString "Lösenord")
```
Innan de kan logga in måste kontona aktiveras vilket inte görs automatiskt:
```
net user Alice /active:yes
net user Bob /active:yes
```
Lägg till *Alice* till *g_ledare* och *Bob* till *g_personal*:
```
Add-LocalGroupMember -Group "g_ledare" -Member "Alice"
Add-LocalGroupMember -Group "g_personal" -Member "Bob"
```
Bekräfta att de är med i respektive grupp:
```
Get-LocalGroupMember -Group "g_ledare"
Get-LocalGroupMember -Group "g_personal"
```

Jag skapar katalogerna:
```
New-Item -Path "C:\Projekt\Gemensamt" -ItemType Directory
New-Item -Path "C:\Projekt\Ledning" -ItemType Directory
```

I Gemensamt-katalogen ska både g_ledare och g_personal få göra ändringar i:
```
icacls "C:\Projekt\Gemensamt" /grant g_ledare:M
icacls "C:\Projekt\Gemensamt" /grant g_personal:M
```
M – Modify så att man kan läsa, skriva samt ta bort filer. Nu kan båda grupperna läsa och skriva i Gemensamt-katalogen

För katalogen Ledning:
```
icacls "C:\Projekt\Ledning" /grant g_ledare:M
icacls "C:\Projekt\Ledning" /deny g_personal:F
```
Det första kommandot ger gruppen g_ledare tillåtelse att läsa, skriva och ta bort filer i katalogen ledning. Det andra kommandot nekar åtkomst (deny) till gruppen g_personal.

Kolla rättigheterna med `Get-ACL`
```
Get-ACL C:\Projekt\Gemensamt | Format-List
Get-ACL C:\Projekt\Ledning | Format-List
```

**Reflektion:**

Både Alice och Bob kan skapa filer och ändra i Gemensamt men bara Alice får tillgång till Ledning. När Alice skapar en fil står hon som ägare och samma om Bob skapar en fil, då står han som ägare för den.

Linux har inte ett bra inbyggt sätt att hantera att att två eller fler grupper ska ha tillgång till samma katalog eller filer. I Linux så har varje fil och katalog en ägare och grupp, och de kan bara sättas till en åt gången. Det finns dock ett paket man kan installera som heter `acl` som gör det möjligt att lägga till flera olika användare och grupper i rättigheter på filer och kataloger.

I Windows kan man lägga till flera användare och grupper på samma fil/katalog vilket ger ett mer dynamiskt system för rättighetshantering. Dock kan det medfölja säkerhetsrisker, i Linux är det mer bergränsat men säkrare då obehöriga användare inte kommer åt för mycket eller inte kan ändra i systemfiler.

**Arv i de olika filsystemen:**

I Linux är det filen/katalogens skapare som blir ägare och därmed bestäms rättigheterna. I Windows får en ny fil/katalog rättigheterna från huvudkatalogen men ägaren är användaren som skapat filen.


## Moment C - Teknisk dokumentation

### 1. Systeminformation

De två maskinerna är virtueliserade i VirtualBox med identisk virtuell hårdvara, det som skiljer sig är storleken på hårddiskarna då Windows 11 kräver större utrymme. 2 kärnor med 8GB tillåter att systemen har en någorlunda bra prestanda eftersom inga tunga program ska köras, mestadels CLI i Linux och Powershell i Windows.

| Information | Linux | Windows |
| ----------| -------- | ------- |
| Hostname | ubuntuserverlab | WINDOWS11LAB |
| Operativsystem | Ubuntu 26.04 LTS |Microsoft Windows 11 Home |
| Build | 7.0.0-31-generic | 10.0.26200 |
| CPU | 2 Kärnor | 2 Kärnor |
| RAM | 8GB | 8 GB |
| Lagring | 25GB virtuell hårddisk | 60 GB virtuell hårddisk |

---

### 2. Nätverksinformation

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

### 3. Användare & grupper

#### Linux

- **Lokala användare:**  victor, alice, bob
- **Lokala grupper:** g_ledare (alice), g_personal (bob)

#### Windows

- **Lokala användare:**  victorwindows, Alice, Bob
- **Lokala grupper:** g_ledare (Alice), g_personal (Bob)

Lösenord tillhörande användarna finns i extern krypterad fil för referens.