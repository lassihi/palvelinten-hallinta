# Harjoitus 4: Pkg-file-service
Kurssi: https://terokarvinen.com/palvelinten-hallinta/ \
Tehtävänanto: https://terokarvinen.com/palvelinten-hallinta/#h4-pkg-file-service

## Suoritusympäristö
Tietokone: Lenovo Legion Y540-15IRH kannettava kytkettynä langallisesti kotiverkkoon.\
-Intel Core i7-9750H\
-NVIDIA Geforce RTX 2060 6GB\
-16GB DDR4 2666MHz\
Käyttöjärjestelmä: Windows 11 23H2

## x) Lue ja tiivistä 
Karvinen 2018: [Pkg-File-Service – Control Daemons with Salt – Change SSH Server Port](https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/?fromSearch=karvinen%20salt%20ssh)
* Demoneiden konfiguraationhallinnassa käytetään usein package-file-service mallia.
  * Package: Asentaa ohjelmiston, `pkg.installed`
  * File: Määritättää konfiguraation, `file.managed`
  * Service: Käynnistää demonin uudelleen konfiguraation jälkeen, `service.running`
* Määritetään masterin .sls-tiedostossa
