# Harjoitus 1: Viisikko
Kurssi: https://terokarvinen.com/palvelinten-hallinta/
Tehtävänanto: https://terokarvinen.com/palvelinten-hallinta/#h1-viisikko

## x) Lue ja tiivistä 
* Karvinen 2023: Run Salt Command Locally
  * Salt komentoa käytetään yleensä useampien orja tietokoneiden samanaikaiseen hallintaan. Saltin ajaminen paikallisesti on hyvä tapa harjoitella sen käyttöä.
  * Saltin käyttö paikallisesti vaati, että orja demoni on asennettua laitteella.
  * Käyttö paikallisesti: `$ sudo salt-call --local -l info state.single "toiminto"`. Tärkeimmät toiminnot:
    *  `pkg.installed "paketti"`, `pkg.removed "paketti"`: paketti tulee olla asennettuna/poistettuna
    *  `file.managed "tiedosto"`, ´file.absent "tiedosto"´: tiedosto tule olla olemassa/ei olemassa
    *  `service.running "demoni" state=True`, `service.dead "demoni" state=False`: demonin tulee olla käynnissä/ei käynnissä
    *  `user.present "käyttäjä"`, `user.absent "käyttäjä"`: käyttäjä tulee olla olemassa/ei olemassa
    *  `cmd.run "komento"`: ajaa komennon
   
* Karvinen 2018: Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux
  * Salt master ohjaa orjia ja sillä tulee olla julkinen palvelin, jonka IP-osoite tulee olla orjien tiedossa ja reikä palomuurissa 4505/tcp ja 4506/tcp.
  * Orjille määritetään master palvelimen IP-osoite ja id, jotta ne voivat hakea tiedot masterilta.
  * Master palvelimen tulee hyväksyä orjan liittyminen verkkoon, jonka jälkeen se voi antaa komentoja orjille, `sudo salt ´*´ ...`
