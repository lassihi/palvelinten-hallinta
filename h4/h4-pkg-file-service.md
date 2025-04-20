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

### Ensin käsin vm01:lle

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

![image](https://github.com/user-attachments/assets/5002c2dd-9a32-44af-ab28-0e5e8163fbd4)

Tiedosto...
* varmistaa, että openssh-server on asennettuna

      openssh-server:
        pkg.installed

* varmistaa, että orjan ssh-konfiguraatiotiedosto vastaa masterin tiedostoa `/srv/salt/ssh_22_2222/sshd_config`. Tiedosto on aiemmin luomani tiedosto, joka asettaa ssh:n käyttämään portteja 22 ja 2222.

      /etc/ssh/sshd_config:
        file.managed
          - source: salt://ssh_22_2222/sshd_config
* varmistaa, että sshd on käynnissä ja uudelleenkäynnistää sshd:n aina kun tiedostoa `/etc/ssh/sshd_config` muutetaan.

      sshd:
        service.running
          - watch:
            - file: /etc/ssh/sshd_config

  
