## <a name="overview"></a>Översikt

I den här kursen kan du utföra följande steg:

- Distribuera en instans av fjärråtkomst övervakning förkonfigurerade lösningen till din Azure-prenumeration. Det här steget kan du automatiskt distribuerar och konfigurerar Azure-tjänster.
- Ställa in enheten att kommunicera med datorn och den fjärranslutna övervakningslösning.
- Uppdatera enheten exempelkod för att ansluta till den fjärranslutna övervakningslösning och skicka simulerade telemetri som kan visas på instrumentpanelen för lösningen.

## <a name="prerequisites"></a>Krav

Du behöver en aktiv Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen.

> [!NOTE]
> Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [kostnadsfri utvärderingsversion av Azure][lnk-free-trial].

### <a name="required-software"></a>Programvara som krävs

Du måste SSH-klienten på den stationära datorn så att du kan fjärransluta till kommandoraden på hallon Pi.

- Windows innehåller inte en SSH-klient. Vi rekommenderar att du använder [PuTTY](http://www.putty.org/).
- De flesta distributioner för Linux och Mac OS inkluderar SSH-kommandoradsverktyget. Mer information finns i [SSH med Linux- eller Mac OS x](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-hardware"></a>Nödvändig maskinvara

En stationär dator så att du kan fjärransluta till kommandoraden på hallon Pi.

[Microsoft IoT startpaket för hallon Pi 3] [ lnk-starter-kits] eller motsvarande komponenter. Den här kursen använder följande objekt från kit:

- Raspberry Pi 3
- MicroSD-kort (med NOOBS)
- En Mini USB-kabel
- En Ethernet-kabel

[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/