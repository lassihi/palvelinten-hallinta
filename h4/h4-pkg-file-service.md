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

## a) Apache easy mode. Asenna Apache, korvaa sen testisivu ja varmista, että demoni käynnistyy.
Siirryin PowerShellissä vagrant-kansioon ja käynnistin vagrant-virtuaalikoneet.

    cd vagrant
    vagrant up

Yhdistin virtuaalikoneeseen vm01.

    vagrant ssh vm01

### Apache manuaalisesti vm01:lle

Avasin curlilla localhost-sivun osoittaakseni, että Apachea ei ole vielä asennettu.

![image](https://github.com/user-attachments/assets/00df1d91-49cf-4ee3-aa01-7dad50a4fccd)

Päivitin pakettilistan ja asensin Apachen.

    sudo apt-get update
    sudo apt-get install apache2

 Avasin localhost-sivun jälleen curlilla varmistaakseni, että Apache on toiminnassa.

 ![image](https://github.com/user-attachments/assets/4718686c-7852-45f3-8293-3e556bea6d4a)

Testisivu tulostui, joten Apache toimii. Päivitin seuraavaksi testisivun sijainnissa `/var/www/html/index.html`.

    echo "Hei Apache" | sudo tee /var/www/html/index.html

Muutos ei vaadi demonin uudelleenkäynnistämistä, joten tarkastin muutokset curlilla.

![image](https://github.com/user-attachments/assets/7eb4c53f-b547-4600-8e22-fdd47d6b2710)

### Apache pkg-file-service:n avulla orjalle
Orjana toimiva vm02 virtuaalikone käynnistyi, kun tehtävän alussa ajoin komennon `vagrant up`, joten sitä ei erikseen tarvitse enää käynnistää.

Loin masterina toimivalla vm01:lle uuden moduulin "apache2" sijaintiin `/srv/salt/`

    sudo mkdir -p /srv/salt/apache2/

Loin "init.sls" -tiedoston moduulin sisälle ja aloin muokkaamaan sitä. Tiedosto määrittelee tilan, joka orjassa ajetaan.

    sudoedit /srv/salt/apache2/init.sls

Tiedostossa käytin package-file-service mallia demonin asennuksessa ja konfiguroinnissa. Mallina käytin sivun https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/?fromSearch=karvinen%20salt%20ssh "sshd.init" -tiedostoa.

![image](https://github.com/user-attachments/assets/87188175-d056-45ed-951f-771b2a0af222)

Tiedosto...
* varmistaa, että apache on asennettuna.

      apache2:
        pkg.installed
* käyttää orjan `/var/www/html/index` tiedostona kopiota masterin `/srv/salt/apache2/index.html` tiedostosta.

      /var/www/html/index.html:
        file.managed:
          - source: salt://index.html

Seuraavaksi loin moduuliin index.html tiedoston, jonne lisäsin "Hei orja" merkkijonon.

![image](https://github.com/user-attachments/assets/09e3de49-8095-4dd8-9447-c599f50fa62f)

Ennen tilan ajoa varmistin curlilla, että orjalla (192.168.88.102) ei ole Apachea asennettuna.

![image](https://github.com/user-attachments/assets/f0aa1ffe-1c9e-4a45-8a24-cfdbc5ff1a8e)

Ajoin apache2-tilan, `sudo salt "*" state.apply apache2`.

![image](https://github.com/user-attachments/assets/b8a89ea4-b615-474d-862f-f5c322c26530)

Tulosteen perustella 1/2 tilan tilafunktioista saavutettiin. Ilmeisesti tilafunktion `file.managed` virhe johtui siitä, että salt etsi tiedostoa index.html hakemistosta `/srv/salt/`.

Muutin tiedostoa niin, että index.html tiedoston polku on oikein. Lisäsin samalla tiedostoon `service.running` tilafunktion, jollaa varmistetaan, että demoni on päällä.

    sudoedit /srv/salt/apache2/init.sls

![image](https://github.com/user-attachments/assets/b335b016-e55e-4fe7-bc45-32c83eda3587)

Tallensin tiedoston ja ajoin tilan uudestaan.

![image](https://github.com/user-attachments/assets/d40a16d7-a3c3-48be-9b80-b9486b254447)

Saamani ilmoitus oli aiemmista harjoituksista tuttu ja kävin uudelleenkäynnistämässä salt-minionin vm02:lta.

![image](https://github.com/user-attachments/assets/a1e51064-6e91-4977-9b90-3c1cb7f7d4f2)

Ajoin tilan uudelleen vm01:llä.

![image](https://github.com/user-attachments/assets/d2a7377e-292c-4f4b-9584-130485755832)

Tällä kertaa määritelty tila saavutettiin kokonaisuudessaan ilman muutoksia. Ilmeisesti uudelleenkäynnistyksen jälkeen salt-minion oli osannut käydä hakemassa tiedot uusimmasta tilasta, jonka vuoksi muutoksia ei nyt näkynyt.

Varmistin, että Apachen asennus onnistui ja testisivu päivittyi orjalla.

![image](https://github.com/user-attachments/assets/4e44621d-8a33-4cbf-a56b-ff6695bfef96)

Curl-tulosteen perusteella tämä onnistui.

## b) SSHouto. Lisää uusi portti, jossa SSHd kuuntelee.

### Ensin manuaalisesti vm01:lle

Systemd näyttää, että ssh on aktiivinen ja kuuntelee porttia 22.

![image](https://github.com/user-attachments/assets/0e14ed39-99f6-4fd6-aeb3-3deb6c8a51f2)

Lähdin muokkaamaan ssh:n konfiguraatiotiedostoa, `sudoedit /etc/ssh/sshd_config` ja lisäsin tiedostoon alla olevat rivit.

    Port 22
    Port 2222

Tallensin tiedoston ja käynnistin ssh:n demonin uudelleen, jotta muutokset astuvat voimaan.

    sudo systemctl restart sshd

Uudelleenkäynnistyksen jälkeen ssh:n tulisi kuunnella portteja 22 ja 2222.

![image](https://github.com/user-attachments/assets/19cdbe9f-194a-4646-89fa-df863302809b)

### Sama orjalle pkg-file-service:n avulla.
Loin uuden moduulin masterilla.

    sudo mkdir -p /srv/salt/ssh_22_2222

Koska olen jo tehnyt masterilla oikeanlaisen konfiguraatiotiedoston, niin kopioin sen moduulin sisään.

    sudo cp /etc/ssh/sshd_config /srv/salt/ssh_22_2222/

Tarkastin, että oikea tiedosto kopioitiin oikeaan sijaintiin.

![image](https://github.com/user-attachments/assets/878b9385-74fa-428b-ba01-c019b8b4e3c2)

Tiedoston kopiointi onnistui, joten loin seuraavaksi init.sls-tiedoston. Tiedoston sisällön loin jälleen sivun https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/?fromSearch=karvinen%20salt%20ssh ohjeiden pohjalta.

    sudoedit /srv/salt/ssh_22_2222/init.sls

![image](https://github.com/user-attachments/assets/bbd8bf69-0285-4afd-bad2-b7ac4538d73d)

Tiedosto...
* varmistaa, että openssh-server on asennettuna

      openssh-server:
        pkg.installed

* varmistaa, että orjan ssh-konfiguraatiotiedosto vastaa masterin tiedostoa `/srv/salt/ssh_22_2222/sshd_config`. Tiedosto on aiemmin luomani tiedosto, joka asettaa ssh:n käyttämään portteja 22 ja 2222.

      /etc/ssh/sshd_config:
        file.managed:
          - source: salt://ssh_22_2222/sshd_config
* varmistaa, että sshd on käynnissä ja uudelleenkäynnistää sshd:n aina kun tiedostoa `/etc/ssh/sshd_config` muutetaan.

      sshd:
        service.running:
          - watch:
            - file: /etc/ssh/sshd_config

Ajoin seuraavaksi tilan orjalle.

![image](https://github.com/user-attachments/assets/9db55507-4bc9-41f3-b25b-edcdac2c2db2)

Tulosteen perustella tila onnistui, eli sshd:n tulisi nyt kuunnella portteja 22 ja 2222 koneella vm02. Kävin vm02:lla tarkastamassa tämän.

![image](https://github.com/user-attachments/assets/19e85be4-7b4e-45b5-ace0-60119fd57f11)

Systemd näytti, että vm02:n sshd kuuntelee portteja 22 ja 2222.

## c) Vapaaehtoinen, haastavahko tässä vaiheessa: Asenna ja konfiguroi Apache ja Name Based Virtual Host. Sen tulee näyttää palvelimen etusivulla weppisivua. Weppisivun tulee olla muokattavissa käyttäjän oikeuksin, ilman sudoa.

### Manuaalisesti vm01:lle
Loin Apachelle uuden sivun konfiguraatiotiedoston sijaintiin `/etc/apache2/sites-available`.

![image](https://github.com/user-attachments/assets/b6b03851-7be5-4564-b0ae-2b7329378c74)

![image](https://github.com/user-attachments/assets/456ecf1d-d3b0-4671-a353-7b07c272a725)

Otin käyttöön uuden asetustiedoston "h4c" ja poistin käytöstä oletussivun "000-default", jonka jälkeen potkaisin apachea

    sudo a2ensite h4c.conf
    sudo a2dissite 000-default.conf
    sudo systemctl restart apache2

Loin asetustiedostossa määrittelemäni polun.

    mkdir -p /home/vagrant/public_sites/h4c.com

Loin index.html-tiedoston, jonne kirjoitin "testisivu", ilman sudoa.

    echo testisivu > /home/vagrant/public_sites/h4c.com/index.html

Testasin curlilla, että sivu päivittyi.

![image](https://github.com/user-attachments/assets/d78a0187-2a3f-4978-a1f2-5a6cd6b0d0d5)

### Orjalle pkg-file-service:n avulla

Loin uuden salt-moduulin.

    sudo mkdir -p /srv/salt/NBVH

Loin moduuliin init.sls-tiedoston, jonka määrityksessä käytin apuna dokumentaatiota Saltin [apache.configfile](https://docs.saltproject.io/en/3006/ref/states/all/salt.states.apache.html#salt.states.apache.configfile) ja [apache_site.enabled](https://docs.saltproject.io/en/3006/ref/states/all/salt.states.apache_site.html) tiloista.

    sudoedit /srv/salt/NBVH/init.sls
    
![image](https://github.com/user-attachments/assets/74df661a-3fc5-4e57-9835-ab3b3c9c81a2)

Tiivistettynä tiedosto...
* varmistaa, että apache on asennettuna
* käyttää Saltin sisäänrakennettua apache.configfile-tilaa konfiguraatioon
* varmistaa, että oikea polku ja index.html ovat olemassa tarvittavilla oikeuksilla
* varmistaa, että luotu konfiguraatiotiedosto on käytössä
* varmistaa, että oletus konfiguraatiotiedosto ei ole käytössä
* varmistaa, että demoni on päällä ja uudelleenkäynnistää sen jos konfiguraatiotiedostoa muutetaan

Tilan on tarkoitetus luoda käyttäjälle "vagrant" tyhjä index.html-tiedosto, jota tämä voi muokata ilman pääkäyttäjän oikeuksia. Index.html-tiedostoon tehdyt muutokset pitäisivät näkyä heti Apachen verkkosivulla.

Ennen tilan ajoa testasin, että orjalla on vielä aiemmin asetettu sivu käytössä

![image](https://github.com/user-attachments/assets/fd6fbea9-7851-48f4-8631-61ab3d7b32c7)

Sivu ei ollut vaihtunut mihinkään, joten ajoin tilan. Alla ajon tulokset.

![image](https://github.com/user-attachments/assets/375992ea-f234-402e-8bb3-3879a5089119)

Tulosten perusteella kaikki muut tilat onnistuttiin saavuttamaan, paitsi tilafunktiot `apache_site.enabled` ja `service.running`. Yhteistä näillä tilafunktioilla oli, että ne molemmat vaativat tiedoston `/etc/apache2/sites-available/h4c.conf` olemassaolon. Tulosteen perusteella kumpikaan ei tiedostoa löytänyt, vaikka `apache.configfile` tilafunktio suoritettiin onnistuneesti.

Menin vm02:lle tarkastamaan, onko tiedostoa olemassa.

![image](https://github.com/user-attachments/assets/b5a6bbb0-5b75-49df-b0d7-cb1f97f8009f)

Vertasin seuraavaksi omaa init.sls-tiedoston `apache.configfile`-tilafunktiota ja [dokumentaation](https://docs.saltproject.io/en/3006/ref/states/all/salt.states.apache.html) esimerkkiä ja huomasin, että sisennykset, eivät aivan täsmää omassa tilafunktiossani. Alla muokattu tilafunktio oikeilla sisennyksillä.

![image](https://github.com/user-attachments/assets/00825e2f-1d87-4fd1-bd45-0b52d1db17e8)

Ajoin tilan uudelleen.

![image](https://github.com/user-attachments/assets/240c0d6e-2ee4-40a6-8aeb-b48b51f48a01)

Tällä kertaa myös `apache_site.enabled` saavutettiin, eli h4c.conf otettiin käyttöön. Vain viimeinen tilafunktio `service.running` epäonnistui, sillä tiedostoa /etc/apache2/sites-available/h4c.conf ei löytynyt.

Löysin Saltin dokumentaatiosta kohdan, jossa käytetään hyvin samanlaista tilafunktiota kuin omassa init.sls-tiedostossani.
https://docs.saltproject.io/en/3006/ref/states/requisites.html#requisites
![image](https://github.com/user-attachments/assets/3a5f0ecb-c93e-4e82-8db1-ee47db242b68)

Muutin omaa tiedostoani tämän perusteella.

![image](https://github.com/user-attachments/assets/7ec7a4e5-c6b0-4ccf-b569-ddd096f473d2)

Ajoin tilan ja sain edelleen virheen viimeisestä tilafunktiosta. 

Käännyin tässä kohtaa tekoälyn puoleen ja kysyin ChatGPT 4o mallilta apua. Promptiin liitin init.sls tiedoston ja ilmoitin, että service.running antaa virheen.

![image](https://github.com/user-attachments/assets/e51e0cd9-80c4-49d3-a0bc-6bcd2f1ca9ba)

Muutin tiedoston vastaamaan tämän ehdotusta.

![image](https://github.com/user-attachments/assets/1ecb4753-c235-4912-9629-663210247ab8)

Ajoin tilan ja sain virheen.

![image](https://github.com/user-attachments/assets/0c13b8e7-b5e5-4f29-98a9-8d5e07d0199d)

Käytin virheilmoituksen ehdotusta ja muutin init.sls tiedostoa.

![image](https://github.com/user-attachments/assets/a17af82c-4f2f-40df-a158-241122ea0e2f)

Ajoin tilan.

![image](https://github.com/user-attachments/assets/eade3240-4cdb-42cc-8f1b-9fe2c536209e)

Tila saavutettiin vihdoin.

Siirryin vm02:lle muokkaamaan index.html-tiedostoa.

![image](https://github.com/user-attachments/assets/bdb64772-bd1c-401c-ad6c-e383a2110ddf)

![image](https://github.com/user-attachments/assets/b7fdd1bb-20cb-4614-9281-497b43e20919)

Tallensin teidoston ja testasin curlilla.

![image](https://github.com/user-attachments/assets/084adea9-d6c4-4b7f-b3d8-ce9ec458ab8a)

Sain yhä vanhan sivun näkyviin.

Siirryin vm01:lle ja tutkimaan init.sls-tiedostoa ja huomasin kirjoitusvirheen.

![image](https://github.com/user-attachments/assets/ea4288df-509d-4c69-86e6-788dce837517)

Korjasin tämän.

![image](https://github.com/user-attachments/assets/dd2fe5a7-45a8-49d1-a3b8-5844fcc20c21)

Ajoin tilan uudestaan ja testasin curlilla sivun toiminnan.

![image](https://github.com/user-attachments/assets/6c84af96-ee34-4478-9b4b-bff56629a94a)

Vihdoin tila näyttäisi toimivan. Siiryin vielä kerran vm02:lle testaamaan, että index.html-tiedostoon tehdyt muutokset astuvat voimaan oiken.

![image](https://github.com/user-attachments/assets/c21d7829-155d-43b2-b897-04b32c4c4476)

Muutokset ilman sudoa päivittyivät kotisivulle, joten tila toimii.

## Lähteet
Karvinen 2025: palvelinten hallinta: https://terokarvinen.com/palvelinten-hallinta/

Karvinen 2018: Pkg-File-Service – Control Daemons with Salt – Change SSH Server Port: https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/?fromSearch=karvinen%20salt%20ssh

VMWare Inc 2025: SALT.STATES.APACHE: https://docs.saltproject.io/en/3006/ref/states/all/salt.states.apache.html#module-salt.states.apache

VMWare Inc 2025: SALT.STATES.APACHE_SITE: https://docs.saltproject.io/en/3006/ref/states/all/salt.states.apache_site.html
