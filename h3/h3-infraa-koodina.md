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
Karvinen 2014: [Hello Salt Infra-as-Code](https://terokarvinen.com/2024/hello-salt-infra-as-code/)
* Moduuli on kansio, joka sisältää kaikki tiettyyn Salt-tilaan liittyvä koodit, tiedostot tai mallit. Ne asentavat, konfiguroivat ja asentavat haluttavan asian. Moduuli sijoitetaan hakemistoon `/srv/salt/`.
* Moduulin sisälle sijoitetaan tiedosto `init.sls`, jolla määritellään tiloja. Tiedoston sisältämän koodin tulee olla idempotentti.
* Tila otetaan käyttöön `sudo salt-call "*" state.apply...`.

Salt contributors: [Salt overview](https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html#rules-of-yaml) (kohdat Rules of YAML, YAML simple structure ja Lists and dictionaries - YAML block structures)
* YAML on tehokkailla ominaisuuksilla varusteltu markup-kieli ja sen tulkki on käytössä oletuksena monessa Saltin tiedostossa. Tulkkia käytetään muuttamaan YAML data Pythoniksi.
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
