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

## a) Hello Vagrant! Osoita jollain komennolla, että Vagrant on asennettu (esim tulostaa vagrantin versionumeron).
Vagrant ei ollut asennettuna entuudestaan, joten asensins sen installerin osoitteesta https://developer.hashicorp.com/vagrant/install?product_intent=vagrant#windows. Asennus installerissa vaati lisenssiehtojen hyväksymisen ja uudelleenkäynnistyksen. Uudelleenkäynnistyksen jälkeen Vagrant oli asennettuna.

Ajoin komennon `vagrant --version` PowerShellillä vagrant version tulostamiseksi.

![image](https://github.com/user-attachments/assets/a17ce207-59dc-4206-9db7-fc3047a1a6dc)

## b) Linux Vagrant. Tee Vagrantilla uusi Linux-virtuaalikone.
Tein uuden vagrant hakemiston. 

![image](https://github.com/user-attachments/assets/c090e075-d750-452a-96e9-4c19c4734bc8)

Siirryin hakemistoon vagrant, `cd vagrant` ja alustin debian/bookworm64 Vagrant ympäristön tähän hakemistoon.

![image](https://github.com/user-attachments/assets/875bfe1b-781a-4bce-b014-3162d49b905e)

Käynnistin virtuaalikoneen komennolla `vagrant up` alustetussa hakemistossa. Noin minuutin latailun jälkeen virtuaalikone oli valmiina käytettäväksi.

Yhdistin koneeseen komennolla `vagrant ssh`.

![image](https://github.com/user-attachments/assets/b6cc4748-dba8-450c-a6e4-1ccaee26ba1a)

Varmistin, että kone on yhteydessä internettiin.

![image](https://github.com/user-attachments/assets/d05cbdfd-e7a4-4844-8da3-470547176996)

Poistuin koneesta ja tuhosin sen.

![image](https://github.com/user-attachments/assets/f40e9420-500d-47ec-b673-55ae3b99da5e)

## c) Kaksin kaunihimpi. Tee kahden Linux-tietokoneen verkko Vagrantilla. Osoita, että koneet voivat pingata toisiaan.
Muokkaamalla Vagrantfilea voitiin määrittää virtuaalikoneet ja -ympäristö. Sivulla https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/ on Vagrantfile tiedosto kahden debian/bullseye64 virtuaalikoneen ympäristön luomiseksi vagrantilla.

Avasin oman ympäristöni Vagrantfile-tiedoston notepadilla. 

![image](https://github.com/user-attachments/assets/9eea2ca7-a99a-483d-974c-488b3966bb41)

Poistin kaikki tiedostossa olevat tekstit ja kopion sinne valmiin Vagrantfile-tiedoston. Vaihdoin käyttöjärjestelmäksi debian/bookworm64, vaihdoin virtuaalikoneiden nimet ja tallensin tiedoston. Lopulta tiedosto näytti seuraavalta.

![image](https://github.com/user-attachments/assets/059647d8-537a-4337-872c-e9f9d54f704b)

Ajoin tämän jälkeen `vagrant up` komennon. Koneiden käynnistymisessä meni noin kaksi minuuttia. Tämän valmistuttua yhdistin ensimmäiseen koneeseen "vm01". Tarkastin samalla hostnamen ja IP-osoitteen.

![image](https://github.com/user-attachments/assets/bc8bddcd-5d97-48d7-ba68-1840440ffaf4)

Tein saman toiselle virtuaalikoneelle "vm02".

![image](https://github.com/user-attachments/assets/f1f08be7-bb5e-4e88-88d0-8a3fa3d1d9d3)

Tulosteiden perusteella kumpikin kone oli käynnistynyt oikein ja saanut IP-osoitteen verkosta 192.168.88.0/24, niin kuin Vagrantfilessa määritettiin. Testasin myös, että vm01 (192.168.88.101) ja vm02 (192.168.88.102) pystyvät pingata toisiaan. 

vm02 -> vm01

![image](https://github.com/user-attachments/assets/75132cce-9c6e-418a-b159-e3448516f0c0)

vm01 -> vm02

![image](https://github.com/user-attachments/assets/8acd9d4c-56f2-4ce4-b875-bafb091ee126)

Jokaiseen pingiin vastattiin, joten koneet voivat pingata toisiaan.

## d) Herra-orja verkossa. Demonstroi Salt herra-orja arkkitehtuurin toimintaa kahden Linux-koneen verkossa, jonka teit Vagrantilla. Asenna toiselle koneelle salt-master, toiselle salt-minion. Laita orjan /etc/salt/minion -tiedostoon masterin osoite. Hyväksy avain ja osoita, että herra voi komentaa orjakonetta.

### Salt-master vm01:lle
Käytin ohjeena viime raporttiani, josta löytyy asennuksen tarkemmat tiedot ja ohjeet. 

Avainrenkaan luominen.

![image](https://github.com/user-attachments/assets/21ec35e3-f6eb-454f-a754-913c25719ca0)

Julkinen avain avainrenkaaseen.

![image](https://github.com/user-attachments/assets/9837f115-752e-4cc0-a84b-8ffdac758696)

Curl ei ollut asennettuna, joten päivitin paketit ja asensin sen: `sudo apt-get update`, `sudo apt-get install curl`. Ajoin komennon uudestaan.

![image](https://github.com/user-attachments/assets/bb362569-b313-4e9b-8d93-9755c15e5df7)

Salt-pakettien lisäys paketinhallintaan.

![image](https://github.com/user-attachments/assets/c91a0ccd-06a2-47a8-80c7-4ff82a835289)

Pakettien päivitys ja salt-master asennus.

![image](https://github.com/user-attachments/assets/d9328533-16d5-4ec2-b35e-30b5208527f9)

Varmistin, että asennus onnistui tulostamalla version.

![image](https://github.com/user-attachments/assets/426e6cc7-9609-4c83-9abd-b3482d7923f2)

Poistuin lopuksi vm01:lta.

### Salt-minion vm02:lle

Yhdistin koneeseen, päivitin paketit ja asensin Curl:n.

![image](https://github.com/user-attachments/assets/a4647620-0dea-4b04-b67e-bdf5366ee745)

Avainrenkaan luominen.

![image](https://github.com/user-attachments/assets/08d7ef21-f8fc-4bf0-8f83-bafdeff4f9d9)

Junkinen avain avainrenkaaseen.

![image](https://github.com/user-attachments/assets/364fec7b-d855-43cc-b14d-71966a3a7f9e)

Salt-pakettien lisäys paketinhallintaan.

![image](https://github.com/user-attachments/assets/65dd6655-37ab-4ea5-9e14-bf1f98a6920f)

Pakettien päivitys ja salt-minion asennus.

![image](https://github.com/user-attachments/assets/ac85a0e3-0519-42c1-84a2-0177bc59fc27)

Varmistin, että asennus onnistui tulostamalla version.

![image](https://github.com/user-attachments/assets/ce74c893-4f9c-4983-8a22-85ead622011e)

### Masterin osoite orjalle

Salt-minion ja -master käyttävät pull-arkkitehtuuria, joka tarkoittaa sitä, että orja (client) pyytää masterilta (server) uusimmat tiedot ajettavista tiloista/komennoista. Tämän takia masterin osoite täytyy olla orjan tiedossa.

Avasin nanolla tiedoston /etc/salt/minion.

![image](https://github.com/user-attachments/assets/e903124c-5d7c-4741-a67c-530084d41444)

Lisäsin tiedostoon masterin eli vm01:n IP-osoitteen ja minionin vapaavalintaisen id:n.

    master: 192.168.88.101
    id: vm02

Lopuksi tallensin tiedoston ja poistuin vm02:lta.

### Orjan hyväksyminen ja komentaminen masterilta.

Yhdistin vm01 koneeseen, `vagrant ssh vm01`.

Ajoin komennon `sudo salt-key -A` orjan avaimen hyväksymiseksi, mutta master ei ollut vastaanottanut vielä sen avainta.

![image](https://github.com/user-attachments/assets/a3c9da10-ed2d-43b7-8d0a-85da2fb29f6b)

Tutkin ohjeet läpi (https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/?fromSearch=salt%20quickstart%20salt%20stack%20master%20and%20slave%20on%20ubuntu%20linux) ja huomasin, että olin unohtanut uudelleenkäynnistää salt-minionin muutosten jälkeen. Siirryin siis vm02:lle ja annoin seuraavan komennon.

![image](https://github.com/user-attachments/assets/3dea7071-d298-4df6-966e-b9bd459d823a)

Siirryin tämän jälkeen vm01:lle ja ajoin komennon `sudo salt-key -A`. Master ei vieläkään ollut saanut minionin avainta. 

Asensin ufw palomuuriohjelmiston ja sallin ulkoa tulevan tcp liikenteen portteihin 4505 ja 4506.
![image](https://github.com/user-attachments/assets/510db6a8-4c4f-4768-85e5-3b66f77ba9fe)


