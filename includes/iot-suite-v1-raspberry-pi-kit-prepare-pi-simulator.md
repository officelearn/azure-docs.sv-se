## <a name="prepare-your-raspberry-pi"></a>Förbered din Raspberry Pi

### <a name="install-raspbian"></a>Installera Raspbian

Om det här är första gången du använder din hallon Pi som du behöver installera Raspbian operativsystemet med hjälp av NOOBS på SD-kort som ingår i kit. Den [hallon Pi Software Guide] [ lnk-install-raspbian] beskriver hur du installerar ett operativsystem på din hallon Pi. Den här kursen förutsätter att du har installerat operativsystemet Raspbian på din hallon Pi.

> [!NOTE]
> SD-kort som ingår i den [Microsoft Azure IoT-startpaket för hallon Pi 3] [ lnk-starter-kits] NOOBS installerat finns redan. Du kan starta hallon Pi från det här kortet och välja att installera Raspbian OS.

Du slutför installationen av maskinvara, måste du:

- Anslut din hallon Pi till strömförsörjning som ingår i kit.
- Anslut din hallon Pi till nätverket via Ethernet-kabel som ingår i din kit. Du kan också ställa in [trådlös anslutning] [ lnk-pi-wireless] för din hallon Pi.

Du har nu slutfört maskinvaruinställningar hallon-pi.

### <a name="sign-in-and-access-the-terminal"></a>Logga in och öppna terminalen

Du har två alternativ för att komma åt en terminal miljö på din hallon Pi:

- Om du har ett tangentbord och bildskärm ansluten till din hallon Pi, kan du använda det grafiska Användargränssnittet Raspbian att komma åt ett terminalfönster.

- Åtkomst till kommandoraden på din hallon Pi använder SSH från den stationära datorn.

#### <a name="use-a-terminal-window-in-the-gui"></a>Använd ett terminalfönster i det grafiska Användargränssnittet

Standardautentiseringsuppgifterna för Raspbian är användarnamn **pi** och lösenord **hallon**. I Aktivitetsfältet i det grafiska Användargränssnittet kan du starta den **Terminal** verktyget med hjälp av ikonen som ser ut som en bildskärm.

#### <a name="sign-in-with-ssh"></a>Logga in med SSH

Du kan använda SSH för kommandoraden åtkomst till din hallon Pi. Artikeln [SSH (Secure Shell)] [ lnk-pi-ssh] beskriver hur du konfigurerar SSH på din hallon Pi och hur du ansluter från [Windows] [ lnk-ssh-windows] eller [ Linux- och Mac OS][lnk-ssh-linux].

Logga in med användarnamn **pi** och lösenord **hallon**.

#### <a name="optional-share-a-folder-on-your-raspberry-pi"></a>Valfritt: Dela en mapp på din hallon Pi

Alternativt kan behöva du dela en mapp på din hallon Pi med skrivbordsmiljön. Dela en mapp kan du använda din önskade skrivbord textredigerare (t.ex [Visual Studio Code](https://code.visualstudio.com/) eller [Sublime Text](http://www.sublimetext.com/)) att redigera filerna på din hallon Pi istället för att använda `nano` eller `vi`.

Om du vill dela en mapp med Windows, konfigurera en Samba-server på hallon Pi. Du kan också använda inbyggt [SFTP](https://www.raspberrypi.org/documentation/remote-access/) server med en SFTP-klient på skrivbordet.

[lnk-install-raspbian]: https://www.raspberrypi.org/learning/software-guide/quickstart/
[lnk-pi-wireless]: https://www.raspberrypi.org/documentation/configuration/wireless/README.md
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/