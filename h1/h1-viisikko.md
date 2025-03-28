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
    *  `file.managed "tiedosto"`, ´file.absent "tiedosto"´: tiedosto tule olla olemassa/ei olemassa
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

![image](https://github.com/user-attachments/assets/c564a599-27db-4b69-a235-343f346c6871)

Hain Salt repositoryn julkisen avaimen ja lisäsin sen avainrenkaaseen.

![image](https://github.com/user-attachments/assets/fc57cfdc-2823-452f-8f0f-c813516e2122)

Lisäsin paketit pektinhallintaan.

![image](https://github.com/user-attachments/assets/37250efb-c9f0-41d7-8e25-4a88ce8ce73a)

Päivitin paketit uudestaan

![image](https://github.com/user-attachments/assets/25de00ed-1cb7-4f91-b293-dabac79ea297)


## Lähteet
Karvinen 2025: Palvelinten Hallinta: https://terokarvinen.com/palvelinten-hallinta/

Karvinen 2023: Run Salt Command Locally: https://terokarvinen.com/2021/salt-run-command-locally/

Karvinen 2018: Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux: https://terokarvinen.com/2018/03/28/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/

Karvinen 2006: Raportin kirjoittaminen: https://terokarvinen.com/2006/06/04/raportin-kirjoittaminen-4/

WMWare Inc: Salt Install Guide: Linux (DEB): https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/linux-deb.html
