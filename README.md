# Gyakorlati vizsgám Junior System Administratorként.

A vizsgán a tanár által automatizáltan létrehozott AWS EC2-es gépre kellett be sshzni. Minden vizsgázónak az email címével volt egy EC2 Instance. A gépen az Exam mappában volt egy shell-1, shell-2, sql-1, sql-2, cloud-1 mappa. A megoldásokat a feladathoz tartozó mappában lévő üres filokba kellett írni vagy létrehozni a leírás alapján. A mappákban még található volt egy check.sh script file, mely vizsgálta a megoldásaimat illetve hogy a rész- illetve egész vizsga megoldásaimmal hány %-nál tartok. A gyakorlati vizsgát 100%ra teljesítettem.  
A vizsga 2 db shell, 2 db sql és 1 db AWS cloud feladatsort adott. A megoldáshoz kapott idő 3 óra volt.

## shell-1 mappa feladatai:

### 1. FELADAT:
Tömörítsd ki a task1-xdg.tar.gz archív állományt és módosítsd a kitömörített fájlstruktúrát (task1.sh)

Az archív fájl tartalma a következő:
```
etc
└── [drwxr-xr-x]  xdg
    ├── [drwxr-xr-x]  autostart
    │   ├── [-rw-r--r--]  at-spi-dbus-bus.desktop
    │   ├── [-rw-r--r--]  snap-userd-autostart.desktop
    │   └── [-rw-r--r--]  xdg-user-dirs.desktop
    ├── [drwxr-xr-x]  systemd
    │   └── [lrwxrwxrwx]  user -> ../../systemd/user
    ├── [-rw-r--r--]  user-dirs.conf
    └── [-rw-r--r--]  user-dirs.defaults
```
- Tömörítsd ki az állományt (a munka könyvtáradban ezután létezzen az etc mappa)
- Töröld le az autostart mappát és benne minden fájlt (egyéb fájlokat ne törölj)
- A user-dirs.conf ne legyen olvasható akárki számára (csak a tulajdonos és a csoport tudja olvasni)
- A user-dirs.conf fájl végéhez fűzd hozzá az exam_path=shell-1 szöveget
- A user-dirs.conf fájl eredeti tartalma ne törlődjön/módosuljon (csak kerüljön a végére az előbbi szöveg)

#### task1.sh tartalma:
```bash
#!/bin/bash
tar -xzf task1-xdg.tar.gz 
rm -fr etc/xdg/autostart
chmod o=-r etc/xdg/user-dirs.conf
echo 'exam_path=shell-1' >> etc/xdg/user-dirs.conf
```
### 2. FELADAT:
Keresd meg a megfelelő adatokat a task2-oscars.csv fájlban és mentsd el őket (task2.sh)

A fájlban Oscar-díjas férfi színészek neve, életkora és a film címe és megjelenési éve található amiért díjat kaptak 4 oszlopba rendezve vesszővel elválasztva:
Megjelenés éve,Színész életkora,Színész neve,Film címe
1995,38,Tom Hanks,Forrest Gump

- Mentsd le a fájl utolsó 10 sorát a last10.txt-be
- Mentsd le a Nic szót tartalmazó sorokat a nic.txt-be
- Mentsd le a '80-as években megjelent filmek címeit a movies.txt-be
- Mentsd le a 33 éves színészek nevét az age.txt-be
- Mentsd le a '90-es években megejelent filmekben szereplő 40-es éveiben járó színészek nevét az actors.txt-be

#### task2.sh tartalma:
```bash
#!/bin/bash
F='task2-oscars.csv'
tail -n 10 $F > last10.txt
cat $F | grep 'Nic' > nic.txt
cat $F | grep '^198' | awk -F ',' '{print $4}' > movies.txt
cat $F | awk -F ',' '{if($2=="33"){print $3}}' > age.txt
cat $F | grep '^199' | awk -F ',' '{print $2,$3}' | grep '^4' | awk -F ' ' '{print $2,$3}' > actors.txt
```

## shell-2 mappa feladatai:

### 1. FELADAT:
Készítsd el a következő könyvtárszerkezetet a megfelelő fájlokkal és jogosultságokkal (task1.sh)
```
solution
├── [drwxr-x---]  secret
│   ├── [-rw-------]  passwords.txt
│   └── [-rw-rw-r--]  accounts.txt
├── [drwxr-xr-x]  links
│   └── [lrwxrwxrwx]  default.conf -> ../settings.defaults
└── [-rw-r--r--]  settings.defaults
```
#### task-1.sh tartalma:
```bash
#!/bin/bash
mkdir -p solution/secret
mkdir -p solution/links
touch solution/secret/passwords.txt
touch solution/secret/accounts.txt
touch solution/settings.defaults
ln -s ../settings.defaults solution/links/default.conf
chmod 750 solution/secret
chmod 755 solution/links
chmod 600 solution/secret/passwords.txt
chmod 664 solution/secret/accounts.txt
chmod 777 solution/links/default.conf
chmod 644 solution/settings.defaults
```
### 2. FELADAT:
Az archív fájl tartalma a következő:
```
www
    └── html
        └── index.nginx-debian.html
```
- Töltsd le a www.tar.gz archívumot a https://github.com/CodecoolBase/sysadmin-exam-assets/raw/master/www.tar.gz linkről
- Az archívum www.tar.gz néven kerüljön mentésre/letöltésre
- Csomagold ki az archívumot és helyezd át a kicsomagolt mappastruktúrát a /tmp mappába, az alá
- Állítsd be a /tmp/www könyvtár felhasználói csoportját, hogy az exam csoport tulajdona legyen
- Állítsd be ugyanezt minden fájlra a /tmp/www mappán belül

#### task-2.sh tartalma:
```bash
#!/bin/bash
wget https://github.com/CodecoolBase/sysadmin-exam-assets/raw/master/www.tar.gz
tar -xzf www.tar.gz -C /tmp/
chown -R :exam /tmp/www
```
## sql-1 mappa feladatai:

A képen látható chinook DB séma a Unix felhasználód nevével azonos nevű DB-be van betöltve (tehát, ha john.doe@example.com az email címed, akkor a DB neve john.doe)

### 1. FELADAT:
Figyelem: minden lekérdezést külön fájlba kell írnod (task1a.sql, task2b.sql, stb.)
Kérdezd le a megfelelő adatokat a "customer" táblából
- Jelenítsd meg az összes vásárlót (task1a.sql)
- Jelenítsd meg a London-i vásárlók email címét (task1b.sql)
- Jelenítsd meg a Stuttgart-i és Berlin-i vásárlók email címét (task1c.sql)
- Jelenítsd meg a vásárlók kereszt- (firstname) és családnevét (lastname) akiknek gmail.com-ra végződő email címük van (task1d.sql)
- Jelenítsd meg országok nevét és azt, hogy hány vásárló származik az adott országból, csökkenő sorrendben (task1e.sql)

#### task1a.sql tartalma:
```sql
SELECT * FROM customer
```
#### task1b.sql tartalma:
```sql
SELECT email FROM customer WHERE city='London'
```
#### task1c.sql tartalma:
```sql
SELECT email FROM customer WHERE city IN ('Stuttgart', 'Berlin') 
```
#### task1d.sql tartalma:
```sql
SELECT firstname, lastname FROM customer WHERE email LIKE'%@gmail.com'
```
#### task1e.sql tartalma:
```sql
SELECT country, count(customerid) as "count" FROM customer
GROUP BY country 
ORDER BY "count" DESC
```
### 2. FEALADAT:
Kérdezd le a megfelelő adatokat az "artist", "album" és "track" táblákból
- Jelenítsd meg az Audioslave előadó albumainak nevét és az albumokon szereplő dalok címét (task2a.sql)
- Jelenítsd meg az előadók és az albumaik nevét és azt, hogy hány dal szerepel az egyes albumokon, de csak azokat, ahol 25-nél több dal szerepel egy albumon és rendezd csökkenő sorrendbe a dalok száma szerint az eredményt (task2b.sql)

#### task2a.sql tartalma:
```sql
SELECT album.title, track.name FROM track
LEFT JOIN album ON track.albumid=album.albumid
LEFT JOIN artist ON album.artistid=artist.artistid
WHERE artist.name LIKE 'Audioslave'
```
#### task2b.sql tartalma:
```sql
SELECT artist.name, album.title, count(track.trackid) AS "count" FROM track
LEFT JOIN album ON track.albumid=album.albumid
LEFT JOIN artist ON album.artistid=artist.artistid
GROUP BY album.albumid, artist.artistid HAVING count(track.trackid) > 25
ORDER BY "count" DESC
```
## sql-2 mappa feladatai:

### 1. FELADAT:
Módosítsd az adatbázis "playlist" táblájának szerkezetét és tartalmát (task1.sql)
- Adj hozzá a "playlist" táblához egy új, max. 255 karakter hosszú szöveges adatokat tárolni képes "description" (leírás) nevű oszlopot
- A "description" oszlop alapértelmezett értéke legyen 'N/A'
- Hozz létre új rekordot a "playlist" táblában, legyen az elsődleges kulcs értéke 20, a neve 'My playlist' a leírás (description) pedig 'No description'
- Frissítsd a "playlist" táblában a 18-as azonosítóval rendelkező rekordot, aminek a neve 'On-The-Go 1' és nevezd át 'On-The-Go'-ra
- Töröld az összes olyan lejátszási listát a "playlist" táblából, amelyeknek a neve 'Audiobooks'

#### task1.sql tartalma:
```sql
ALTER TABLE public.playlist
    ADD COLUMN description character varying(255) DEFAULT 'N/A';
INSERT INTO playlist
VALUES (20, 'My playlist', 'No description');
UPDATE playlist SET name='On-The-Go' WHERE playlistid=18;
DELETE FROM playlist WHERE name='Audiobooks';
```
### 2. FELADAT:
Végezd el az adminisztratív feladatokat az adatbázis szerveren (task2.sql)
- Hozz létre egy "student" nevű táblát az adatbázisban, két oszloppal: "id" és "name"
- Az "id" oszlop egészszám típusú, és elsődleges kulcs
- A "name" karakter típusú, max. 128 karakter hosszú szöveg, és nem lehet üres

#### task2.sql tartalma:
```sql
CREATE TABLE public.student
(
    id integer NOT NULL,
    name character varying(128) NOT NULL,
    PRIMARY KEY (id)
);
```
## clod-1 mappa feladatai:
#ennek a megoldásait nem tudom értelemszerűen közölni, itt csak a kívánt feladatok listáját lehet olvasni

### 1. FELADAT:
Hozz létre egy security group-ot:
- frankfurti régióban hozd létre (eu-central-1)
- a régió default VPC-jében
- a neve legyen az email címed eleje "-Final1a" toldalékkal, pl. "joe.smith@codecool.com" esetében "joe.smith-Final1a"
- adj hozzá "Name" tag-et/címkét
- a "Name" tag értéke legyen ua., mint a security group neve (figyelem, nem ua. a kettő)
- engedélyezd a bejövő SSH kapcsolatokat a 22-es TCP porton bármilyen IP-címről

### 2. FELADAT:
Hozz létre egy másik security group-ot:
- frankfurti régióban hozd létre (eu-central-1)
- a régió default VPC-jében
- a neve legyen az email címed eleje "-Final1b" toldalékkal, pl. "joe.smith@codecool.com" esetében "joe.smith-Final1b"
- adj hozzá "Name" tag-et/címkét
- a "Name" tag értéke legyen ua., mint a security group neve (figyelem, nem ua. a kettő)
- engedélyezd a bejövő SSH kapcsolatokat a korábban létrehozott security group-ból

### 3. FELADAT:
Indíts egy EC2 instance-t:
- frankfurti régióban hozd létre (eu-central-1)
- a régió default VPC-jében
- az eu-central-1a availability zone-hoz tartozó subnet-ben hozd létre
- használd az Ubuntu Server 18.04 LTS AMI-t
- az instance type legyen t2.micro
- adj hozzá "Name" tag-et/címkét
- a "Name" tag értéke legyen az email címed eleje "-Final1" toldalékkal, pl. "joe.smith@codecool.com" esetében "joe.smith-Final1"
- a korábban létrehozott "-Final1b" végződésű névvel ellátott security group-ot rendeld hozzá az instance-hoz
- add hozzá a kulcspárod, tehát csak a saját SSH privát kulcsoddal (aminek a neve az email címed) lehessen elérni
- állítsd le az instance-t (ne legyen running state-ben)

# Köszönöm a figyelmet! 









