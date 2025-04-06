# Harjoitus 2: Soitto kotiin
Kurssi: https://terokarvinen.com/palvelinten-hallinta/ \
Tehtävänanto: https://terokarvinen.com/palvelinten-hallinta/#h2-soitto-kotiin

## Suoritusympäristö
Tietokone: Lenovo Legion Y540-15IRH kannettava kytkettynä langallisesti kotiverkkoon.\
-Intel Core i7-9750H\
-NVIDIA Geforce RTX 2060 6GB\
-16GB DDR4 2666MHz\
Käyttöjärjestelmä: Windows 11 23H2

## x) Lue ja tiivistä 
Karvinen 2021: Two Machine Virtual Network With Debian 11 Bullseye and Vagrant
* Vagrant automatisoi virtuaalikoneiden luomisen, niihin yhdistämisen ja niiden tuhoamisen.
* Vagrantfile määrittelee virtuaalikoneet.

Karvinen 2018: Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux
* Salt master ohjaa orjia ja sillä tulee olla julkinen palvelin, jonka IP-osoite tulee olla orjien tiedossa ja reikä palomuurissa 4505/tcp ja 4506/tcp.
* Orjille määritetään master palvelimen IP-osoite ja id, jotta ne voivat hakea tiedot masterilta.
* Master palvelimen tulee hyväksyä orjan liittyminen verkkoon, jonka jälkeen se voi antaa komentoja orjille, `sudo salt ´*´ ...`

Karvinen 2023: Salt Vagrant - automatically provision one master and two slaves
