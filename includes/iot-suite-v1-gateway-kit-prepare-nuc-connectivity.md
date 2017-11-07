## <a name="prepare-your-intel-nuc"></a>Förbered din Intel NUC

Du slutför installationen av maskinvara, måste du:

- Anslut din Intel NUC till strömförsörjning som ingår i kit.
- Anslut din Intel NUC till nätverket med hjälp av en Ethernet-kabel.

Du har nu slutfört maskinvaruinställningar av Intel NUC-gateway-enheten.

### <a name="sign-in-and-access-the-terminal"></a>Logga in och öppna terminalen

Du har två alternativ för att komma åt en terminal miljö på Intel-NUC:

- Om du har ett tangentbord och bildskärm har anslutits till Intel-NUC, kan du komma åt gränssnittet direkt. Standardautentiseringsuppgifterna som är användarnamnet **rot** och lösenord **rot**.

- Åtkomst till gränssnittet på din Intel NUC använder SSH från den stationära datorn.

#### <a name="sign-in-with-ssh"></a>Logga in med SSH

Om du vill logga in med SSH, måste du IP-adressen för Intel-NUC. Om du har ett tangentbord och bildskärm har anslutits till Intel-NUC använder den `ifconfig` kommando för att hitta IP-adress. Du kan också ansluta till routern att lista adresserna till enheter i nätverket.

Logga in med användarnamn **rot** och lösenord **rot**.

#### <a name="optional-share-a-folder-on-your-intel-nuc"></a>Valfritt: Dela en mapp på Intel-NUC

Alternativt kan behöva du dela en mapp på Intel-NUC med skrivbordsmiljön. Dela en mapp kan du använda din önskade skrivbord textredigerare (t.ex [Visual Studio Code](https://code.visualstudio.com/) eller [Sublime Text](http://www.sublimetext.com/)) att redigera filerna på din Intel NUC istället för att använda `nano` eller `vi`.

Konfigurera en Samba-server på Intel NUC om du vill dela en mapp med Windows. Du kan också använda SFTP-server på Intel NUC med SFTP-klienten på den stationära datorn.
