# Default Route i konfigurisanje Default Route-a u Cisco Packet Tracer-u
## Šta je ovo?
Ovo je vežba za administratore računarskih mreža koju sam radio tokom mog školovanja. Nalazi se jedan .pkt fajl koji se preuzme i to će Vam biti početno stanje. Zadak Vam je dat, kao i uputstvo za njegovo rešavanje. Ukoliko Vam nešto nije jasno tokom rešavanja ovih zadataka obratite mi se direktnom porukom na aplikaciji LinkedIn, www.linkedin.com/in/nikola-karanović-397185390

## Pitanja i odgovori — Default Route (podrazumevana ruta)

### 1. Šta je Default Route?
**Default route** (podrazumevana ruta) je posebna statička ruta koja se koristi za **sav saobraćaj čije odredište ne postoji eksplicitno u routing tabeli** rutera. Predstavlja se sa adresom mreže **0.0.0.0** i maskom **0.0.0.0** (što znači "svaka moguća mreža"), pa se zato često zove i **"ruta posljednje šanse" (gateway of last resort)**. Ukoliko ruter ne pronađe nijednu konkretniju (specifičniju) rutu za odredišnu adresu paketa, koristi baš ovu rutu.

---

### 2. Zašto se koristi Default Route?
Default route se koristi:
1. **Da bi se izbeglo ručno upisivanje rute za svaku pojedinačnu udaljenu mrežu** — posebno korisno na **stub mrežama** (mrežama koje imaju samo jednu izlaznu tačku, npr. ka internetu).
2. **Za pristup internetu** — pošto internet sadrži ogroman broj mreža, nemoguće je (i nepotrebno) da ruter ima konkretnu rutu za svaku od njih, pa se sav saobraćaj ka "spolja" usmerava preko jedne default rute ka ISP-u.
3. **Za jednostavnije i skalabilnije rutiranje** — smanjuje veličinu routing tabele i administrativni napor.

---

### 3. Šta je "Gateway of Last Resort"?
**Gateway of last resort** je termin kojim se opisuje **sledeći hop (next-hop) adresa ili izlazni interfejs** definisan default rutom. To je ruter (ili interfejs) na koji se šalje paket kada **nijedna druga, specifičnija ruta** u tabeli ne odgovara odredišnoj adresi. U `show ip route` izlazu, ova informacija se prikazuje u liniji:

"Gateway of last resort is 200.10.10.1 to network 0.0.0.0"

---

### 4. Koja je osnovna komanda za konfigurisanje IPv4 default rute?
Default ruta se konfiguriše kao statička ruta sa mrežom i maskom 0.0.0.0:

"Router(config)# ip route 0.0.0.0 0.0.0.0 200.10.10.1"

ili, ako se koristi izlazni interfejs umesto next-hop adrese:

"Router(config)# ip route 0.0.0.0 0.0.0.0 Serial0/0/0"

---

### 5. Kako se konfiguriše default ruta za IPv6?
Princip je identičan kao kod IPv4, samo se umesto mreže/maske 0.0.0.0/0.0.0.0 koristi **::/0**:

"Router(config)# ipv6 route ::/0 2001:DB8:ACAD::1"

---

### 6. Šta je Stub mreža i kakva je njena veza sa default rutom?
**Stub mreža** je mreža koja ima **samo jednu izlaznu tačku** (jedan ruter/link) ka ostatku mreže ili internetu — dakle, nema alternativnih putanja. Na ruteru koji se nalazi na granici stub mreže nije potrebno konfigurisati detaljne rute ka svim spoljnim mrežama, već je dovoljno postaviti **jednu default rutu** ka jedinom dostupnom izlazu, čime se značajno pojednostavljuje konfiguracija.

---

### 7. Kako se default ruta prosleđuje (propagira) drugim ruterima u mreži?
Ako koristimo dinamički protokol rutiranja, default rutu možemo automatski **propagirati ka drugim ruterima**, umesto da je ručno konfigurišemo na svakom od njih:

- Kod **OSPF**:
