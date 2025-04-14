# Harjoitus 3: Infraa koodina
Kurssi: https://terokarvinen.com/palvelinten-hallinta/ \
Tehtävänanto: https://terokarvinen.com/palvelinten-hallinta/#h3-infraa-koodina

## Suoritusympäristö
Tietokone: Lenovo Legion Y540-15IRH kannettava kytkettynä langallisesti kotiverkkoon.\
-Intel Core i7-9750H\
-NVIDIA Geforce RTX 2060 6GB\
-16GB DDR4 2666MHz\
Käyttöjärjestelmä: Windows 11 23H2

## x) Lue ja tiivistä 
Karvinen 2024: [Hello Salt Infra-as-Code](https://terokarvinen.com/2024/hello-salt-infra-as-code/)
* Moduuli on kansio, joka sisältää kaikki tiettyyn Salt-tilaan liittyvä koodit, tiedostot tai mallit. Ne asentavat ja konfiguroivat halutun asian. Moduuli sijoitetaan hakemistoon `/srv/salt/`.
* Moduulin sisälle sijoitetaan tiedosto `init.sls`, jolla määritellään tiloja. Tiedoston sisältämän koodin tulee olla idempotentti.
* Tilan ajo paikallisesti `sudo salt-call --local state.apply...`.

Salt contributors: [Salt overview](https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html#rules-of-yaml) (kohdat Rules of YAML, YAML simple structure ja Lists and dictionaries - YAML block structures)
* YAML on tehokkailla ominaisuuksilla varustettu markup-kieli ja sen tulkki on käytössä oletuksena monessa Saltin tiedostossa. Tulkkia käytetään muuttamaan YAML data Pythoniksi.
* YAML:n perussäänöt
  * Data = `avain: arvo`, ": " merkkaa parin
  * Avaimen arvojen määrittämiseen voidaan käyttää erilaisia rakenteita
  * Kirjainten koolla on merkitystä
  * Ei saa käyttää tabia
  * #Kommentti
* Kolme tapaa luoda rakenne
  * Skalaari: `avain: arvo`, jossa arvo voi olla numero, merkkijono tai boolean
  * Lista: avainta seuraa lista arvoja, välilyönneillä on merkitystä

        avain:
          - arvo1
          - arvo2
  * Sanakirja: skalaarien ja listojen yhdistelmä

        avain1:
          avain2:
            - arvo1
            - arvo2
          avain3: arvo3
* YAML organisoidaan lohkorakenteisiin, joita määrittävät sisennykset (kaksi välilyöntiä on vakio)
  
## a) Hei infrakoodi! Kokeile paikallisesti (esim 'sudo salt-call --local') infraa koodina. Kirjota sls-tiedosto, joka tekee esimerkkitiedoston /tmp/ -kansioon.
Avasin PowerShellin, vaihdoin vagrant-hakemistoon `cd vagrant` ja käynnistin virtuaalikoneet, `vagrant up`.

Yhdistin viime tehtävässä Vagrantilla luotuun vm01 virtuaalikoneeseen.

    vagrant ssh vm01

Virtuaalikoneella päivitin paketit `sudo apt-get update` ja asensin Micron `sudo apt-get install micro`.

Teron Hello Salt Infra-as-Code ohjeiden mukaan loin uudelle moduulille "hello-salt" hakemiston, polkuun `/srv/salt/`.

    sudo mkdir -p /srv/salt/hello-salt/

Siirryin hakemistoon, `cd /srv/salt/hello-salt/`. Loin moduulille init.sls-tiedoston.

![image](https://github.com/user-attachments/assets/4f0c323d-e432-4dec-ab2f-44e64dfed77c)

![image](https://github.com/user-attachments/assets/3ec9af32-196f-496e-b907-57641a74a6c1)

init.sls määrittää tilan, jossa tiedosto `/tmp/hei-suola` on olemassa.

Tallensin tiedoston ja ajoin moduulin paikallisesti Saltilla.

![image](https://github.com/user-attachments/assets/9991b0bc-0793-443c-968b-771db78d128b)

Tulosteen perusteella tiedosto /tmp/hei-suola luotiin (file /tmp/hei-suola created) ja tila saavutettiin (Succeedeed: 1). Tarkastin, että tila on idempotentti ajamalla komennon uudelleen.

![image](https://github.com/user-attachments/assets/94cbfb03-d5e3-4eb7-a0a4-4825156fe4b9)

Tällä kertaa tila saavutettiin ilman muutoksia, joten se on idempotentti.

Varmistin myös, että tiedosto löytyy.

![image](https://github.com/user-attachments/assets/ae61e3fe-821b-4dbd-b0dd-6fcbd0973646)

## b) Aja esimerkki sls-tiedostosi verkon yli orjalla.
Avasin uuden PowerShell ikkunan, siirryin vagrant hakemistoon ja otin yhteyden vm02:een, joka toimii orjana.

Masterina toimivalla vm01:llä ajoin komennon `sudo salt '*' state.apply hello-salt`.

![image](https://github.com/user-attachments/assets/a68acf02-aed7-4794-9601-4d810c09d5ad)

Moduulin kuvaama tila saavutettiin ja yksi muutos tehtiin.

Tiedoston tarkastus orjalla.

![image](https://github.com/user-attachments/assets/b5b7fd4b-b691-4215-bc37-61cf4db4bba3)

## c) Tee sls-tiedosto, joka käyttää vähintään kahta eri tilafunktiota näistä: package, file, service, user. Tarkista eri ohjelmalla, että lopputulos on oikea. Osoita useammalla ajolla, että sls-tiedostosi on idempotentti.
Loin masterilla uuden moduulin "h3", vaihdoin tekstieditoriksi micro ja loin init.sls-tiedoston. Ohjeina käytin Salt tilojen dokumententaatiota, `sudo salt-call --local sys.state_doc user` ja `sudo salt-call --local sys.state_doc file`.

![image](https://github.com/user-attachments/assets/34c96442-f658-4e91-a455-faddd1e90e61)

![image](https://github.com/user-attachments/assets/cc4337eb-9d91-4cd4-b483-0d69f68c8178)

init.sls määrittää tilan, jossa koneella on käyttäjä "kayttaja", jonka kotihakemistossa `/home/kayttaja/` on tiedosto "salainen-tiedosto". Tiedoston omistaa käyttäjä ja sen oikeudet ovat `-r--------`, eli vain omistajalla on lukuoikeus.

Ajoin moduulin h3.

![image](https://github.com/user-attachments/assets/82cc01bb-d865-47bb-aff2-051806cc179b)

Tästä seurasi pitkä virheilmoitus, joka näyttäisi johtuvan yhteysongelmista. Ajoin vielä salt-komennon `sudo salt '*' cmd.run 'hostname'` varmistaakseni, ettei ongelma johtunut init.sls-tiedostosta.

![image](https://github.com/user-attachments/assets/1cb5d8cc-3d44-4a73-9772-c79cf87cd2e3)

Sama virheilmoitus. Aiemmin Saltin uudelleenkäynnistys ratkaisi ongelmat, joten potkaisin ensiksi salt-minionia vm02:lla komennolla `sudo systemctl restart salt-minion.service`, ajoin edellisen komennon uudelleen ja sain saman virheilmoituksen. Potkaisin vielä salt-masteria vm01:llä komennolla `sudo systemctl restart salt-master.service` ja ajoin komennon uudestaan.

![image](https://github.com/user-attachments/assets/f1908df9-e839-411f-9cda-268d2e151e8a)

Nyt salt taas toimi, joten aika ajaa moduuli h3.

![image](https://github.com/user-attachments/assets/72e5b153-92fc-4f66-8c93-98d822c510b7)

Saltin tulosteen perusteella init.sls-tiedostossa kuvattu tila saavutettiin kahdella muutoksella.

Siirryin vm02:lle, tarkastin uuden käyttäjän olemassaolon `id`-työkalulla ja tiedostosta `/etc/shadow`.

![image](https://github.com/user-attachments/assets/51b16a25-5278-4b3c-924e-3c870e7eeb04)

Seuraavaksi tarkastin, että tiedoston luonti onnistui.

![image](https://github.com/user-attachments/assets/5ab41be0-790f-4c53-985e-9ded9ed4228a)

Myös näiden perusteella tila saavutettiin.

Lopuksi ajoin moduulin vielä kaksi kertaa varmistaakseni idempotentin.

![image](https://github.com/user-attachments/assets/62c70a9c-ee89-4ca9-b5e6-01b4fd9216a9)

Tila saavutettiin molemmilla kerroilla ilman yhtään muutosta, joten voidaan varmistaa idempotentti.

## Lähteet
Karvinen 2024: Hello Salt Infra-as-Code: https://terokarvinen.com/2024/hello-salt-infra-as-code/

Salt contributors: Salt overview: https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html

Salt dokumentaatio: komennot `sudo salt-call --local sys.state_doc user` ja `sudo salt-call --local sys.state_doc file`
