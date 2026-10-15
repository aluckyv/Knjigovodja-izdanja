# Rad na više računara

Baza je na jednom računaru u kancelariji (u daljem tekstu: **server**). Ostali računari se na nju povezuju preko lokalne mreže. Podešavanje se radi jednom, na serveru.

## 1. Saznajte adresu servera

Na serveru otvorite **Start → cmd** i upišite `ipconfig`. Zapišite **IPv4 Address**, na primer `192.168.1.20`.

Preporuka: u ruteru serveru dodelite stalnu adresu (DHCP rezervacija), da se ne bi menjala posle restarta.

## 2. Dozvolite bazi da prima veze iz mreže

Na serveru otvorite folder sa podacima PostgreSQL-a, obično
`C:\Program Files\PostgreSQL\16\data`.

**postgresql.conf** (otvorite u Notepadu kao administrator): nađite red sa `listen_addresses` i postavite ga ovako, bez `#` na početku:

```
listen_addresses = '*'
```

**pg_hba.conf**: na kraj fajla dodajte red za vašu mrežu. Ako adresa servera počinje sa `192.168.1.`, red glasi:

```
host    all    all    192.168.1.0/24    scram-sha-256
```

(Ako počinje sa `192.168.0.`, upišite `192.168.0.0/24`, i tako redom.)

Zatim restartujte bazu: **Start → Services**, nađite **postgresql-x64-16**, desni klik → **Restart**.

## 3. Otvorite port u Windows zaštitnom zidu

Na serveru otvorite **Start → cmd** kao administrator (desni klik → *Run as administrator*) i upišite:

```
netsh advfirewall firewall add rule name="PostgreSQL" dir=in action=allow protocol=TCP localport=5432 profile=private
```

Mreža u kancelariji treba da bude podešena kao **Private** (Settings → Network → svojstva mreže).

## 4. Na ostalim računarima

1. Instalirajte program: `Knjigovodja-win-Setup.exe`.
2. Na ekranu za prijavu, u polje **Server** upišite adresu servera (npr. `192.168.1.20`), a u **Lozinka baze** istu lozinku kao na serveru.
3. Prijavite se nalogom koji vam je administrator napravio u kartici **Korisnici** (verzija 0.1.2 i novije).

## Kad nešto ne radi

- **„Server baze nije dostupan”**: proverite adresu, da je server uključen, korak 3 (zaštitni zid) i da je baza restartovana posle koraka 2.
- **„no pg_hba.conf entry for host …”**: adresa računara nije obuhvaćena redom iz koraka 2; proverite taj red.
- **„Pogrešna lozinka baze”**: lozinka mora biti ista kao na serveru.

Baza i podaci su samo na serveru. Program sam pravi dnevnu rezervnu kopiju na svakom računaru (folder Dokumenti\Knjigovodja\Rezervne kopije).
