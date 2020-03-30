---
title: Azure IoT Hub-enhet strömmar | Microsoft-dokument
description: Översikt över Azure IoT Hub-enhetsströmmar, vilket underlättar säkra dubbelriktade TCP-tunnlar för en mängd olika kommunikationsscenarier från molnet till enheten.
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.openlocfilehash: ff738e56226f7cbb720a754573a9d8607e0e3247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890456"
---
# <a name="iot-hub-device-streams-preview"></a>IoT Hub-enhetsströmmar (förhandsgranskning)

Azure IoT *Hub-enhetsströmmar* underlättar skapandet av säkra dubbelriktade TCP-tunnlar för en mängd olika kommunikationsscenarier från molnet till enheten. En enhetsström medieras av en slutpunkt för *strömning* av IoT Hub som fungerar som en proxy mellan enheten och tjänstslutpunkterna. Den här inställningen, som visas i diagrammet nedan, är särskilt användbar när enheter är bakom en nätverksbrandvägg eller finns inuti ett privat nätverk. IoT Hub-enhetsströmmar hjälper till att hantera kundernas behov av att nå IoT-enheter på ett brandväggsvänligt sätt och utan att behöva öppna inkommande eller utgående brandväggsportar.

!["Översikt över IoT Hub-enheter strömmar"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Med hjälp av IoT Hub-enhetsströmmar är enheterna säkra och behöver bara öppna utgående TCP-anslutningar till IoT-hubbens slutpunkt för direktuppspelning över port 443. När en ström har upprättats, kommer tjänstsidan och enhetsprogrammen att ha programmatisk åtkomst till ett WebSocket-klientobjekt för att skicka och ta emot råbyte till varandra. Tillförlitlighets- och beställningsgarantierna från denna tunnel är i nivå med TCP.

## <a name="benefits"></a>Fördelar

IoT Hub-enhetsströmmar ger följande fördelar:

* **Brandväggsvänlig säker anslutning:** IoT-enheter kan nås från tjänstslutpunkter utan att öppna inkommande brandväggsport vid enheten eller nätverksper perimetererna (endast utgående anslutning till IoT Hub behövs över port 443).

* **Autentisering:** Både enhets- och tjänstsidorna i tunneln måste autentisera med IoT Hub med hjälp av motsvarande autentiseringsuppgifter.

* **Kryptering:** Som standard använder IoT Hub-enhetsströmmar TLS-aktiverade anslutningar. Detta säkerställer att trafiken alltid krypteras oavsett om programmet använder kryptering eller inte.

* **Enkelhet i anslutning:** I många fall eliminerar användningen av enhetsströmmar behovet av komplex installation av virtuella privata nätverk för att möjliggöra anslutning till IoT-enheter.

* **Kompatibilitet med TCP/IP-stack:** IoT Hub-enhetsströmmar kan hantera TCP/IP-programtrafik. Detta innebär att ett brett utbud av egna såväl som standardbaserade protokoll kan utnyttja den här funktionen.

* **Användarvänlighet i privata nätverksinställningar:** Tjänsten kan kommunicera med en enhet genom att referera till dess enhets-ID i stället för enhetens IP-adress. Detta är användbart i situationer där en enhet finns i ett privat nätverk och har en privat IP-adress, eller dess IP-adress tilldelas dynamiskt och är okänd för tjänstsidan.

## <a name="device-stream-workflows"></a>Arbetsflöden för enhetsström

En enhetsström initieras när tjänsten begär att ansluta till en enhet genom att ange dess enhets-ID. Det här arbetsflödet passar särskilt in i en kommunikationsmodell för klient/server, inklusive SSH och RDP, där en användare avser att fjärransluta till SSH- eller RDP-servern som körs på enheten med hjälp av ett SSH- eller RDP-klientprogram.

Processen för att skapa enhetsström innebär en förhandling mellan enheten, tjänsten, IoT-hubbens huvud- och strömningsslutpunkter. Medan IoT-hubbens huvudslutpunkt dirigerar skapandet av en enhetsström hanterar slutpunkten för direktuppspelning den trafik som flödar mellan tjänsten och enheten.

### <a name="device-stream-creation-flow"></a>Flöde för att skapa enhetsström

Programmatisk skapande av en enhet ström med hjälp av SDK innebär följande steg, som också avbildas i figuren nedan:

!["Handskakningsprocess för enhetsströmmar"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. Enhetsprogrammet registrerar en motringning i förväg för att meddelas om när en ny enhetsström initieras till enheten. Det här steget sker vanligtvis när enheten startar och ansluter till IoT Hub.

2. Serviceprogrammet initierar en enhetsström när det behövs genom att ange enhets-ID _(inte_ IP-adressen).

3. IoT-hubben meddelar enhetsprogrammet genom att anropa motringningen som registrerats i steg 1. Enheten kan acceptera eller avvisa begäran om initiering av strömmen. Den här logiken kan vara specifik för ditt programscenario. Om begäran om direktuppspelning avvisas av enheten informerar IoT Hub tjänsten om detta. Annars följer stegen nedan.

4. Enheten skapar en säker utgående TCP-anslutning till slutpunkten för direktuppspelning över port 443 och uppgraderar anslutningen till en WebSocket. Url:en för slutpunkten för direktuppspelning samt de autentiseringsuppgifter som ska användas för att autentisera tillhandahålls båda till enheten av IoT Hub som en del av begäran som skickas i steg 3.

5. Tjänsten meddelas om resultatet av enheten accepterar strömmen och fortsätter att skapa sin egen WebSocket-klient till slutpunkten för direktuppspelning. På samma sätt tar den emot url:en för direktuppspelning och autentiseringsinformation från IoT Hub.

I handskakningsprocessen ovan:

* Handskakningen måste slutföras inom 60 sekunder (steg 2 till 5), annars skulle handskakningen misslyckas med en timeout och tjänsten meddelas därefter.

* När flödet skapas flödet ovan är klart, kommer slutpunkten för direktuppspelning att fungera som en proxy och överföra trafik mellan tjänsten och enheten över sina respektive WebSockets.

* Både enhet och tjänst behöver utgående anslutning till IoT Hubs huvudslutpunkt samt slutpunkten för direktuppspelning över port 443. URL:en för dessa slutpunkter är tillgänglig på fliken *Översikt* på IoT Hub-portalen.

* Tillförlitligheten och beställningsgarantierna för en etablerad ström är i nivå med TCP.

* Alla anslutningar till IoT Hub och strömningsslutpunkten använder TLS och krypteras.

### <a name="termination-flow"></a>Avslutningsflöde

En etablerad ström avslutas när någon av TCP-anslutningarna till gatewayen kopplas från (av tjänsten eller enheten). Detta kan ske frivilligt genom att stänga WebSocket på antingen enheten eller tjänstprogram, eller ofrivilligt i händelse av en nätverksanslutning timeout eller processfel. Vid uppsägning av antingen enhetens eller tjänstens anslutning till slutpunkten för direktuppspelning kommer den andra TCP-anslutningen också att (kraftfullt) avslutas och tjänsten och enheten är ansvariga för att återskapa strömmen om det behövs.

## <a name="connectivity-requirements"></a>Anslutningskrav

Både enheten och tjänstsidorna på en enhetsström måste kunna upprätta TLS-aktiverade anslutningar till IoT Hub och dess slutpunkt för direktuppspelning. Detta kräver utgående anslutning via port 443 till dessa slutpunkter. Värdnamnet som är associerat med dessa slutpunkter finns på fliken *Översikt* i IoT Hub, vilket visas i bilden nedan:

!["Slutpunkter för enhetsström"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Alternativt kan slutpunktsinformationen hämtas med Azure CLI under hubbens egenskapsavsnitt, specifikt, `property.hostname` och `property.deviceStreams` nycklar.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

Utdata är ett JSON-objekt för alla slutpunkter som hubbens enhet och tjänst kan behöva ansluta till för att upprätta en enhetsström.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Se till att du har installerat Azure CLI version 2.0.57 eller nyare. Du kan hämta den senaste versionen från sidan [Installera Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Tillåt utgående anslutning till slutpunkter för direktuppspelning av enheten

Som nämndes i början av den här artikeln skapar enheten en utgående anslutning till slutpunkten för strömning av IoT Hub under initieringsprocessen för enhetsströmmar. Brandväggarna på enheten eller dess nätverk måste tillåta utgående anslutning till den strömmande gatewayen via port 443 (observera att kommunikation sker via en WebSocket-anslutning som är krypterad med TLS).

Värdnamnet för slutpunkten för direktuppspelning av enheter finns på Azure IoT Hub-portalen under fliken Översikt. ![](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Du kan också hitta den här informationen med Azure CLI:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Se till att du har installerat Azure CLI version 2.0.57 eller nyare. Du kan hämta den senaste versionen från sidan [Installera Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
>

## <a name="troubleshoot-via-device-streams-activity-logs"></a>Felsöka via aktivitetsloggar för enhetsströmmar

Du kan ställa in Azure Monitor-loggar för att samla in aktivitetsloggen för enhetsströmmar i din IoT Hub. Detta kan vara till stor hjälp vid felsökningsscenarier.

Följ stegen nedan för att konfigurera Azure Monitor-loggar för IoT Hubs enhetsflödesaktiviteter:

1. Navigera till fliken *Diagnostikinställningar* i IoT-hubben och klicka på länken *Aktivera diagnostik.*

   !["Aktivera diagnostikloggar"](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings-pane.png)

2. Ange ett namn för diagnostikinställningarna och välj alternativet *Skicka till logganalys.* Du kommer att styras att välja en befintlig Log Analytics arbetsyta resurs eller skapa en ny. Kontrollera dessutom *DeviceStreams* från listan.

    !["Aktivera enhetsströmmar loggar"](./media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png)

3. Nu kan du komma åt dina enhetsströmmarloggar under fliken *Loggar* i IoT Hub-portalen. Aktivitetsloggar för enhetsström visas i tabellen `AzureDiagnostics` och har `Category=DeviceStreams`.

   Som visas nedan är målenhetens identitet och resultatet av operationen också tillgängligt i loggarna.

   !["Komma åt enhetsströmmar loggar"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

## <a name="regional-availability"></a>Regional tillgänglighet

Under den offentliga förhandsversionen är IoT Hub-enhetsströmmar tillgängliga i regionerna Centrala USA, CENTRALA USA, NORDEURopa och Sydostasien. Se till att du skapar hubben i något av dessa regioner.

## <a name="sdk-availability"></a>SDK-tillgänglighet

Två sidor av varje ström (på enheten och tjänstsidan) använder IoT Hub SDK för att upprätta tunneln. Under den offentliga förhandsversionen kan kunderna välja mellan följande SDK-språk:

* C och C# SDK:s stödenhet strömmar på enhetssidan.

* NodeJS och C# SDK-stödenheten strömmar på tjänstsidan.

## <a name="iot-hub-device-stream-samples"></a>IoT Hub-enhetsströmexempel

Det finns två [snabbstartsexempel](/azure/iot-hub) tillgängliga på IoT Hub-sidan. Dessa visar användningen av enhetsströmmar av program.

* Ekoexemplet visar programmatisk användning av enhetsströmmar (genom att anropa SDK API:er direkt). *echo*

* Det lokala proxyexemplet visar tunnelning av direktklient-/serverprogramtrafik (till exempel SSH, RDP eller webb) via enhetsströmmar. *local proxy*

Dessa prover behandlas mer i detalj nedan.

### <a name="echo-sample"></a>Eko-exempel

Ekoexemplet visar programmatisk användning av enhetsströmmar för att skicka och ta emot byte mellan tjänst- och enhetsprogram. Observera att du kan använda service- och enhetsprogram på olika språk. Du kan till exempel använda C-enhetsprogrammet med C#-serviceprogrammet.

Här är ekoproverna:

* [C# service- och serviceprogram](quickstart-device-streams-echo-csharp.md)

* [Nod.js serviceprogram](quickstart-device-streams-echo-nodejs.md)

* [C-enhetsprogram](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Lokalt proxyexempel (för SSH eller RDP)

Det lokala proxyexemplet visar ett sätt att aktivera tunnelning av ett befintligt programs trafik som innebär kommunikation mellan en klient och ett serverprogram. Den här uppsättningen fungerar för klient-serverprotokoll som SSH och RDP, där servicesidan fungerar som en klient (kör SSH- eller RDP-klientprogram) och enhetssidan fungerar som servern (kör SSH-daemon- eller RDP-serverprogram).

I det här avsnittet beskrivs användningen av enhetsströmmar för att göra det möjligt för användaren att SSH till en enhet via enhetsströmmar (fallet för RDP eller annat klient-serverprogram är liknande med hjälp av protokollets motsvarande port).

Installationen utnyttjar två *lokala proxyprogram* som visas i figuren nedan, nämligen *enhetslokal proxy* och *service-lokal proxy*. De lokala proxyprogrammen ansvarar för att utföra [enhetsströminitieringshandskakningen](#device-stream-creation-flow) med IoT Hub och interagera med SSH-klient och SSH-demon med vanliga klient-serveruttag.

!["Konfiguration av enhetsströmproxy för SSH/RDP"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. Användaren kör tjänstlokal proxy för att initiera en enhetsström till enheten.

2. Den enhetslokala proxyn accepterar begäran om initiering av strömmen och tunneln upprättas till IoT Hubs slutpunkt för direktuppspelning (som diskuterats ovan).

3. Den enhetslokala proxyn ansluter till slutpunkten SSH daemon som lyssnar på port 22 på enheten.

4. Den servicelokala proxyn lyssnar på en angiven port som väntar på nya SSH-anslutningar från användaren (port 2222 som används i exemplet, men detta kan konfigureras till alla andra tillgängliga portar). Användaren pekar SSH-klienten på den servicelokala proxyporten på localhost.

### <a name="notes"></a>Anteckningar

* Ovanstående steg slutför en end-to-end-tunnel mellan SSH-klienten (till höger) till SSH-demonen (till vänster). En del av den här slutpunkt till slutpunkt-anslutning innebär att skicka trafik via en enhetsström till IoT Hub.

* Pilarna i figuren anger i vilken riktning anslutningarna upprättas mellan ändpunkterna. Observera att det inte finns några inkommande anslutningar som går till enheten (detta blockeras ofta av en brandvägg).

* Valet att använda port 2222 på den lokala proxyn för tjänsten är ett godtyckligt val. Proxyn kan konfigureras för att använda någon annan tillgänglig port.

* Valet av port 22 är protokollberoende och specifikt för SSH i det här fallet. För RDP måste porten 3389 användas. Detta kan konfigureras i de medföljande exempelprogrammen.

Använd länkarna nedan för instruktioner om hur du kör de lokala proxyprogrammen på ditt språk. I likhet med [ekoexemplet](#echo-sample)kan du köra enhets- och tjänstlokala proxyprogram på olika språk eftersom de är helt driftskompatibla.

* [C# service- och serviceprogram](quickstart-device-streams-proxy-csharp.md)

* [Nod.js serviceprogram](quickstart-device-streams-proxy-nodejs.md)

* [C-enhetsprogram](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Nästa steg

Använd länkarna nedan om du vill veta mer om enhetsströmmar.

> [!div class="nextstepaction"]
> [Enhetsströmmar på IoT-mässan (Kanal 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
