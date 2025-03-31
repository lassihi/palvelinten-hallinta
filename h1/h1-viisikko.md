# Harjoitus 1: Viisikko
Kurssi: https://terokarvinen.com/palvelinten-hallinta/
Tehtävänanto: https://terokarvinen.com/palvelinten-hallinta/#h1-viisikko

## Suoritusympäristö
Tietokone: Lenovo Legion Y540-15IRH kannettava kytkettynä langallisesti kotiverkkoon.\
-Intel Core i7-9750H\
-NVIDIA Geforce RTX 2060 6GB\
-16GB DDR4 2666MHz\
Käyttöjärjestelmä: Windows 11 23H2

## x) Lue ja tiivistä 
Karvinen 2023: Run Salt Command Locally
  * Salt komentoa käytetään yleensä useampien orja tietokoneiden samanaikaiseen hallintaan. Saltin ajaminen paikallisesti on hyvä tapa harjoitella sen käyttöä.
  * Saltin käyttö paikallisesti vaati, että orja demoni on asennettua laitteella.
  * Käyttö paikallisesti: `sudo salt-call --local -l info state.single "tilafunktio"`. Tärkeimmät tilafunktiot:
    *  `pkg.installed "paketti"`, `pkg.removed "paketti"`: paketti tulee olla asennettuna/ei asennettuna
    *  `file.managed "tiedosto"`, `file.absent "tiedosto"`: tiedosto tule olla olemassa/ei olemassa
    *  `service.running "demoni" state=True`, `service.dead "demoni" state=False`: demonin tulee olla käynnissä/ei käynnissä
    *  `user.present "käyttäjä"`, `user.absent "käyttäjä"`: käyttäjä tulee olla olemassa/ei olemassa
    *  `cmd.run "komento"`: ajaa komennon
  * Oma kommentti/huomio:

Karvinen 2018: Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux
  * Salt master ohjaa orjia ja sillä tulee olla julkinen palvelin, jonka IP-osoite tulee olla orjien tiedossa ja reikä palomuurissa 4505/tcp ja 4506/tcp.
  * Orjille määritetään master palvelimen IP-osoite ja id, jotta ne voivat hakea tiedot masterilta.
  * Master palvelimen tulee hyväksyä orjan liittyminen verkkoon, jonka jälkeen se voi antaa komentoja orjille, `sudo salt ´*´ ...`
  * Oma kommentti/huomio:

Karvinen 2006: Raportin kirjoittaminen
  * Raportissa kerrotaan mitä tehtiin ja mitä tapahtui. Sen tulee olla toistettava, täsmällinen ja helppolukuinen.
  * Raporttia kirjoitetaan jotta muistetaan jälkikäteen mitä on tehty, ajatusten selkeyttämiseksi ja muita auttamaan.
  * Oma kommentti/huomio:

VMWare Inc: Salt Install Guide: Linux (DEB) (poimi vain olennainen osa)
  * Salt repositoryn paketit (master, minion...) eivät kuuluu oletuksena Debianin paketinhallinnan paketteihin.
  * Jotta Salt-paketit voi asentaa paketinhallinnan avulla, on ensiksi:
    * varmistettava, että hakemisto avainrenkaille on olemassa: `mkdir -p /etc/apt/keyrings`
    * ladatta julkinen avain ja lisättävä se avainrenkaaseen: `curl -fsSL https://packages.broadcom.com/artifactory/api/security/keypair/SaltProjectKey/public | sudo tee /etc/apt/keyrings/salt-archive-keyring.pgp`
    * lisättävä Salt repon paketit paketinhallintaan: `curl -fsSL https://github.com/saltstack/salt-install-guide/releases/latest/download/salt.sources | sudo tee /etc/apt/sources.list.d/salt.sources`
    * päivitettävä saatavilla olevat paketit: `sudo apt-get update`
  * Asenna master ja minion: `sudo apt-get install salt-master`, `sudo apt-get install salt-minion`
  * Oma kommentti/huomio:

## a) Asenna Debian 12-Bookworm virtuaalikoneeseen. (Poikkeuksellisesti tätä alakohtaa ei tarvitse raportoida, jos siinä ei ole mitään ongelmia...)
Tein uuden virtuaalikoneen VirtualBoxilla, jonka käyttöjärjestelmäksi asetin Debian 12 Bookwormin. 

Tämän lisäksi asensin Debian 12 virtuaalikoneen M2 MacBook Airiin mahdollisia tulevia tarpeita varten. Tässä tapauksessa käytin asennukseen debian-12.10.0-arm64-netinst.iso teidostoa ja virtualisointiohjelmistona toimi UTM. Tämän raportin tehtävät on kuitenkin tehty suoritusympäristössä kuvatulla koneella.

## b) Asenna Salt (salt-minion) Linuxille (uuteen virtuaalikoneeseesi).
Avasin virtuaalikoneen terminaalin ja ajoin komennot `sudo apt-get update` ja `sudo apt-get dist-upgrade` varmistaakseni, että päivitykset ovat ajan tasalla.

Varmistin, että avainrengashakemisto on olemassa.

![image](https://github.com/user-attachments/assets/6b0aedc5-7999-4c22-a7ca-9bb5abb62192)

Hain Salt repositoryn julkisen avaimen ja lisäsin sen avainrenkaaseen.

![image](https://github.com/user-attachments/assets/4572b408-4b17-4a20-8d3f-c1145cfa0754)

Lisäsin paketit paketinhallintaan.

![image](https://github.com/user-attachments/assets/5cb506a8-fb39-4d53-ab0b-82f586450d14)

Päivitin paketit uudestaan.

![image](https://github.com/user-attachments/assets/f910f730-3640-4e39-87de-f86e0030bbbd)

Asensin salt-minionin koneeseen.

![image](https://github.com/user-attachments/assets/0385145f-3818-4a1d-a6a3-5d4d66c73ab7)

## c) Viisi tärkeintä. Näytä Linuxissa esimerkit viidestä tärkeimmästä Saltin tilafunktiosta: pkg, file, service, user, cmd. Analysoi ja selitä tulokset.
Käytän esimerkkeinä tiivistelmässäni kuvaamia tilafunktioita. Kertauksena salt-tilafunktion ajo paikallisesti: `sudo salt-call --local -l info state.single "tilafunktio"`.

### Pkg
Ensiksi asensin uuden paketin tilafunktiolla `pkg.installed "paketti"`. Asensin sovelluksen tree.

![image](https://github.com/user-attachments/assets/389eb69e-b619-4b0b-a3a7-3027a4f89a33)

Tulosteesta huomataan, että komento onnistui

### File
Luodaan seuraavaksi tiedosto ja poistetaan se, `file.managed "tiedosto"` ja `file.absent "tiedosto"`.

![image](https://github.com/user-attachments/assets/36abde9f-2e93-4902-a02c-a2d6725251ba)

![image](https://github.com/user-attachments/assets/4039c475-158a-4f7b-a2de-7552f1f9d421)

### Service
Asensin Apache2 demonin, jotta voin kokeilla demonin hallintaa. Ajoin komennon `sudo salt-call --local -l info state.single pkg.installed apache2` apachen asentamiseksi saltilla. Komennosta seurasi hyvin pitkä tuloste, mutta alla sen yhteenveto.

![image](https://github.com/user-attachments/assets/061b740e-f6d6-4a3a-b841-a94336e6d3c0)

Tarkastin selaimella, että apache on päällä localhost-osoitteessa.

![image](https://github.com/user-attachments/assets/dac1de7e-1e2b-4a51-bf49-4f2e795866b5)

Sammutin apachen tilafunktiolla `service.dead "demoni" state=False`.

![image](https://github.com/user-attachments/assets/5fef7226-4d2f-4011-8446-74f4e145a94a)

Tarkastin selaimella, että apache ei enää ole päällä.

![image](https://github.com/user-attachments/assets/9f423155-4dde-4800-b4c9-ff372a93c20a)

Käynnistin apachen uudelleen tilafunktiolla `service.running "demoni" state=True`.

![image](https://github.com/user-attachments/assets/46bddc44-cc43-4d1d-aa08-435cdc4dc14d)

![image](https://github.com/user-attachments/assets/57a27838-94cc-460f-bc4c-77cbf6df7253)

### User
Loin uuden käyttäjän `user.present "käyttäjä"` tilafunktiolla.

![image](https://github.com/user-attachments/assets/a64ca1cf-d9d4-4835-8711-d56b436551d5)

Uuden käyttäjän sisäänkirjautuminen on poistettu automaattisesti, jonka voi huomata tiedostosta `/etc/shadow`, sillä käyttäjän salasanakentässä on huutomerkki. Pääkäyttäjän oikeuksin kuitenkin voi tälle kirjautua. 

![image](https://github.com/user-attachments/assets/0a0e7caa-c30c-4edb-9b4d-8026c3033ed4)

Poistin luomani käyttäjän `user.absent "käyttäjä"` tilafunktiolla. Varmistin myös, että käyttäjää ei ole enää olemassa.

![image](https://github.com/user-attachments/assets/5c369b2a-8fec-40b3-a0fa-773284406464)

### Cmd
## d) Idempotentti. Anna esimerkki idempotenssista. Aja 'salt-call --local' komentoja, analysoi tulokset, selitä miten idempotenssi ilmenee.

Impotenssin määritelmä [Wikipedian mukaan](https://en.wikipedia.org/wiki/Idempotence): "Idempotence is the property of certain operations in mathematics and computer science whereby they can be applied multiple times without changing the result beyond the initial application."


## Lähteet
Karvinen 2025: Palvelinten Hallinta: https://terokarvinen.com/palvelinten-hallinta/

Karvinen 2023: Run Salt Command Locally: https://terokarvinen.com/2021/salt-run-command-locally/

Karvinen 2018: Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux: https://terokarvinen.com/2018/03/28/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/

Karvinen 2006: Raportin kirjoittaminen: https://terokarvinen.com/2006/06/04/raportin-kirjoittaminen-4/

WMWare Inc: Salt Install Guide: Linux (DEB): https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/linux-deb.html
