# Github Classroom

### Github Account aanmaken

We gaan gebruik maken van github classrooms in dit vak. Dus als je nog geen github account hebt dan kan je dit [hier ](https://www.github.com)doen. 

### Join Assignment

We gaan voor alle oefeningen van de komende weken gebruik maken van 1 assignment. Die kan je nadat je een account hebt aangemaakt via [https://classroom.github.com/a/zFN5GQee](https://classroom.github.com/a/zFN5GQee) joinen. 

Je moet eerst je github account authorizeren om gebruik te maken van GitHub Classroom.

Daarna krijg je een scherm te zien met Identifiers. Als je naam hier in de lijst staat kan je deze aanklikken en naar de volgende stap gaan, indien niet kan je nog altijd joinen door naar de volgende stap te gaan door deze stap te skippen.

Daarna kan je **Accept this assignment** doen om de opdracht te aanvaarden. Hij zal dan voor jou een repository aanmaken in je persoonlijke github waar ik ook aan kan.

![](../.gitbook/assets/image%20%2836%29.png)

### SSH keys toevoegen

Het kan zijn dat je deze stap al eens in een ander vak hebt uitgevoerd dus moeten we eerst kijken of je al een SSH key hebt aangemaakt in het verleden. Start git bash op of powershell en doe:

```text
ls ~/.ssh/
```

Als je al bestanden hebt staan die beginnen met id\_ zoals `id_rsa.pub` en `id_rsa` dan heb je al een ssh key en moet je de volgende stap overslaan.

#### **Indien je nog geen ssh keys hebt:**

Indien je er nog geen hebt of deze directory zelfs nog niet bestaat dan moet je er een aanmaken. 

```text
ssh-keygen -t rsa -b 4096
```

Aanvaard het voorgestelde pad en druk gewoon enter:

```text
Enter file in which to save the key (/Users/jenny/.ssh/id_rsa):
```

Je hebt de mogelijkheid om een passphrase in te geven, laat dit voorlopig maar leeg. Indien we de security willen verbeteren zullen we dit later wel oplossen. 

Je scherm moet er ongeveer zo uit zien na het hele proces:

```text
jenny@2015-mbp ~ $ ssh-keygen -t rsa -b 4096 -C "USEFUL-COMMENT"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/jenny/.ssh/id_rsa):     
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/jenny/.ssh/id_rsa.
Your public key has been saved in /Users/jenny/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:ki0TNHm8qIvpH7/c0qQmdv2xxhYHCwlpn3+rVhKVeDo USEFUL-COMMENT
The key's randomart image is:
+---[RSA 4096]----+
|      o+   . .   |
|     .=.o . +    |
|     ..= + +     |
|      .+* E      |
|     .= So =     |
|    .  +. = +    |
|   o.. = ..* .   |
|  o ++=.o =o.    |
| ..o.++o.=+.     |
+----[SHA256]-----+
```

#### **Indien je al een ssh key had:**

Je moet nu de ssh key kopieren zodat je die in github kan ingeven. Je kan dit doen door het id\_rsa.pub bestand te open  en in een editor of direct via de terminal te kopieren in je clipboard.

```text
cat ~/.ssh/id_rsa.pub

ssh-rsa AAZZAB3NzaC1yc2EAZZAADAQABAAACAQCbho+JtZZSlo1MY0/X7gVmafdSZ/56MTermOAwB12M9xfMjqz3TP3VYoJsSJyAUrTBdB2zPAuFxhOhO/4rHLjYwfLrUtNwH8qB/4UbBmuCQ2wsX5cKWGMo0R+be3TQn6Wmiz1Piu6Oqyf374pzV2H3hfpji/Gq2T7vbWjFlRMZtZT9zD3f5UAJrCOPJ9s0BrJFVbEFtkxu52G6+LlVsX9uRiFhMNy4Xe0cmj1GpE7Rtc2b3Tt1npOdpbou1z/iVqr8a+u8JuTvq6QGnrkcsQ4hmTIupKgPRFqa26uj9f43DTQ3ZzTnAN38aygMfpnkMTTiJPFPYAG+JF7OpFfMINCPm4WzAC6/MQIRPhPS0mNVqtm74w2DPnCYmwN9o/vtMBCzSLS9R/WFbJBqkRVSZRFTcPDea7K3Vp11hFCHjETjtoo1S9mguAyU+2dVAZF62+JjlXZdNWleboCrQZLNlyn81YaGHAzE61CYbK+P1bl5s+mVxEorLNRjOzsKNQRHzVQJ67sG5wmlocEf3k+EoMavIqP64VhJGzOtDT1VEMX8ujWrHzMY1kqRWyEAU2ztsQAfINphtRS+WPYLtn+RBQc65W+xPFKOCM8h3yJ77TiBXzuRMChIejZWqQ7LZkUSGLjBHpUr2SwKMubvuQae4OGOtqx2hdTt1CqHK325UArlQ== slimmii@DESKTOP-T8ISLOS
```

Copy paste vervolgens alles wat dit commando uitprint in github

#### **Op github:**

Zorg ervoor dat je ingelogd bent in GitHub. Klik op je profiel foto in de rechterbovenhoek en ga naar Settings en klik dan op SSH en GPG keys. Klik vervolgens op "New SSH key". Plak je publieke sleutel in de "Key" box. Geef het een titel zodat je hem gemakkelijk kan terug vinden. Bijvoorbeeld een unieke naam van je computer. Daarna klik je op "Add SSH key"

Normaal gezien zijn we nu klaar. Je kan 

```text
ssh -T git@github.com
```

doen om je connectie te testen naar GitHub. Je zal dan 

```text
$ ssh -T git@github.com
Hi jenny! You've successfully authenticated, but GitHub does not provide shell access.
```

te zien krijgen als het gelukt is.

### **Repository clonen**

Als alles van de vorige stappen gelukt is kan je je github repository voor dit vak clonen en beginnen gebruiken.

Je zult een url te zien krijgen die zegt waar je repository staat. Daarna doe je in je git bash of powershell:

```text
git clone git@github.com:G-PRO-AP/webframeworks-jouw-gebruikersnaam.git
```

Maak een bestand aan README dat jouw volledige naam bevat en ap email adres en slaag het bestand op. Doe vervolgens

```text
git add README
```

```text
git commit -m "Uw commit message"
```

en geef er een beschrijving van je eerste commit mee en doe dan vervolgens

```text
git push
```

om alles naar de remote github repository te sturen.

