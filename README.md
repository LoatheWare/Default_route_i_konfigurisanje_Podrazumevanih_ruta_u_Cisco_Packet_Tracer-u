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

"Router(config)# router ospf 1"

"Router(config-router)# default-information originate"

- Kod **EIGRP**, default ruta se obično propagira automatski kroz redistribuciju ili korišćenjem komande `ip default-network` (starija metoda).

Ovim se ruterima koji ne imaju direktno definisanu default rutu, ona "ubacuje" preko routing protokola kao **O*E2** (kod OSPF-a) ruta u njihovoj tabeli.

---

### 8. Šta je razlika između Default Route i Default Gateway-a?
- **Default Route** — koristi se na **ruteru**, konfiguriše se kao statička ruta u routing tabeli rutera, i odnosi se na rutiranje između mreža.
- **Default Gateway** — koristi se na **krajnjem uređaju** (računar, server, štampač), predstavlja IP adresu rutera preko kojeg taj uređaj šalje sav saobraćaj namenjen mrežama van njegove lokalne mreže (konfiguriše se npr. komandom `ip default-gateway` na svičevima ili kroz IP podešavanja na računaru).

Iako su konceptualno slični (oba predstavljaju "izlaz kada ne znamo drugu rutu"), razlika je u **nivou uređaja** na kome se primenjuju.

---

### 9. Šta je floating static route i kakva je njena veza sa default rutom?
**Floating static route** je statička ruta (uključujući i default rutu) kojoj je ručno podignuta **administrativna distanca (AD)** iznad vrednosti koju ima primarna ruta (npr. ona naučena dinamičkim protokolom). Zbog toga se ova ruta **ne koristi normalno**, već **samo kao backup** — ako primarna ruta (npr. OSPF ruta ili primarna default ruta) ispadne iz tabele (npr. zbog pada linka), floating ruta automatski preuzima ulogu.

Primer floating default rute (AD podignuta na 200, veće od default AD=1 za statičke rute):

"Router(config)# ip route 0.0.0.0 0.0.0.0 200.10.10.5 200"

---

### 10. Koje su komande za proveru default rute na ruteru?
Najvažnije komande za verifikaciju:

"Router# show ip route"

"Router# show ip route 0.0.0.0"

"Router# show running-config | include ip route"

- `show ip route` — u routing tabeli, default ruta je obeležena oznakom **S\*** (statička default ruta) ili **O\*E2** (default ruta naučena preko OSPF-a), a na vrhu se ispisuje i linija `Gateway of last resort`.
- `show ip route 0.0.0.0` — prikazuje detalje konkretno o default ruti (next-hop, administrativna distanca, metrika).

---

### 11. Šta se dešava ako paket ne odgovara nijednoj ruti, a default ruta nije konfigurisana?
Ukoliko ne postoji nijedna konkretna ruta **ni** default ruta za odredišnu mrežu paketa, ruter **odbacuje (drop-uje)** paket i, ukoliko je omogućeno, šalje **ICMP "Destination Unreachable"** poruku pošiljaocu paketa, jer nema informacije kuda dalje da prosledi saobraćaj.

---
