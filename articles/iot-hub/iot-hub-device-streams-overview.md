---
title: Azure IoT Hub device strömmar (förhandsversion) | Microsoft Docs
description: Översikt över IoT Hub device strömmar.
author: rezasherafat
manager: briz
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 672b06dda41edb18cbf31352188b0fdd2a155782
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60401110"
---
# <a name="iot-hub-device-streams-preview"></a>IoT Hub Device dataströmmar (förhandsversion)

## <a name="overview"></a>Översikt
Azure IoT Hub *enheten strömmar* enklare att skapa säkra dubbelriktad TCP-tunnlar för en mängd olika scenarier med moln-till-enhet-kommunikation. En dataström med enhet genom med en IoT-hubb *slutpunkt för direktuppspelning* som fungerar som proxy mellan slutpunkterna för din tjänst och enhet. Den här konfigurationen kan visas i diagrammet nedan, är särskilt användbart när enheter är bakom en brandvägg eller finns i ett privat nätverk. IoT Hub device dataströmmar hjälper därför adress kunders behov att nå IoT-enheter på ett brandväggsvänligt sätt och utan att behöva brett att nätverksportar öppnas inkommande eller utgående brandvägg.

![Alternativ text](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png "IoT Hub device strömmar översikt")


Med IoT Hub device strömmar enheter förblir säkra och behöver bara öppna utgående TCP-anslutningar till slutpunkten för direktuppspelning av IoT hub via port 443. När en dataström har upprättats har på tjänstsidan och enhetssidan program var och en programmatisk åtkomst till en WebSocket-klientobjekt skicka och ta emot byte till varandra. Tillförlitlighet och ordning garantier som tillhandahålls av den här tunneln är likvärdigt TCP.

## <a name="benefits"></a>Fördelar
Strömmar för IoT Hub-enhet ger följande fördelar:
- **Brandväggsvänligt säker anslutning:** IoT-enheter kan nås från tjänstslutpunkter utan inkommande brandväggsport öppnas på de enhet eller nätverket perimetrar (endast utgående anslutning till IoT Hub krävs via port 443).

- **Autentisering:** Både enheten och tjänsten sidor av tunneln måste autentisera med IoT Hub med sina autentiseringsuppgifter för motsvarande.

- **Kryptering:** Som standard använder IoT Hub device strömmar TLS-aktiverade anslutningar. Detta säkerställer att trafiken krypteras alltid oavsett om programmet använder kryptering eller inte.

- **Enkelheten med anslutning:** I många fall inga enheten strömmar behov av komplicerad konfiguration av virtuella privata nätverk genom att aktivera anslutning till IoT-enheter.

- **Kompatibilitet med TCP/IP-stack:** IoT Hub device strömmar kan hantera TCP/IP-programtrafik. Det innebär att en stor mängd upphovsrättsskyddad samt standardbaserade protokoll kan dra nytta av den här funktionen.

- **Användarvänlighet i privat nätverk inställningar:** Tjänsten kan kommunicera med en enhet genom att referera till dess enhets-ID i stället för enhetens IP-adress. Detta är användbart i situationer där en enhet finns i ett privat nätverk och har en privat IP-adress eller IP-adressen tilldelas dynamiskt och är okända för serversidan.

## <a name="device-stream-workflows"></a>Enheten Stream arbetsflöden
En enhet dataström initieras när tjänstbegäranden för att ansluta till en enhet med dess enhets-ID. Det här arbetsflödet är särskilt passar in i en klient/server-kommunikation modell, inklusive SSH och ansluter via RDP, där en användare har för avsikt att fjärransluta till SSH eller RDP-servern som körs på den enhet som använder en SSH eller RDP-klientprogram.

Enheten stream skapandeprocessen innebär en förhandling mellan enheten, tjänst, IoT-hubbens main och slutpunkter för direktuppspelning. När IoT-hubbens huvudsakliga slutpunkten styr skapandet av en enhet-dataström, hanterar slutpunkten för direktuppspelning trafik som flödar mellan tjänster och enheter.

### <a name="device-stream-creation-flow"></a>Enheten stream Skapa flöde
Programmässig skapandet av en enhet-dataström med hjälp av SDK omfattar följande steg, som också illustreras i bilden nedan:

![Alternativ text](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png "enheten strömma handskakningen")


1. Enheten programmet registrerar ett återanrop i förväg för att meddelas om när en ny enhet ström initieras till enheten. Det här steget vanligtvis sker när enheten startas och ansluter till IoT Hub.

2. Programmet tjänstsidan initierar en enhet-dataström vid behov genom att tillhandahålla enhets-ID (_inte_ IP-adress).

3. IoT hub meddelar enhetssidan programmet genom att aktivera det återanrop som är registrerade i steg 1. Enheten kan godkänna eller avvisa initiation strömningsbegäran. Den här logiken kan vara specifika för ditt program-scenario. Om stream begäran avvisas av enheten, informerar IoT Hub tjänsten i enlighet därmed. Annars följer stegen nedan.

4. Enheten skapar en säker utgående TCP-anslutning till slutpunkten för direktuppspelning via port 443 och uppgraderas anslutningen till en WebSocket. URL: en för slutpunkten för direktuppspelning, samt autentiseringsuppgifterna som du använder för att autentisera både som enheten av IoT Hub som en del av begäran som skickats i steg 3.

5. Tjänsten är aviseras om resultatet av enhet som tar emot dataströmmen och fortsätter att skapa en egen WebSocket-klient till slutpunkten för direktuppspelning. På samma sätt kan den tar emot strömmande URL och autentisering information om slutpunkten från IoT Hub.

I handskakningsprocessen ovan:
- Handskakningsprocessen måste slutföras inom 60 sekunder (steg 2 till 5), annars handskakningen misslyckades med en tidsgräns och tjänsten kommer att meddelas.

- När stream skapa flödet ovan är klar slutpunkten för direktuppspelning fungerar som proxy och överföra trafik mellan tjänsten och enheten via sina respektive WebSockets.

- Enheten och tjänsten måste du utgående anslutning till IoT Hub huvudsakliga slutpunkten samt slutpunkten för direktuppspelning via port 443. Webbadressen till dessa slutpunkter finns på *översikt* fliken på IoT-hubbens portal.

- Tillförlitlighet och ordning garantier för en etablerad dataström är likvärdigt TCP.

- Alla anslutningar till IoT Hub och slutpunkten för direktuppspelning använder TLS och krypteras.

### <a name="termination-flow"></a>Avslutning flöde
En etablerad stream avslutas när något av TCP-anslutningar till gatewayen kopplas (av tjänsten eller enhet). Detta kan ske frivilligt genom att stänga WebSocket på enheten eller tjänsten program eller automatiskt vid ett nätverk anslutningsfel timeout eller process. Den andra TCP-anslutningen också (kernelpaketet) avslutas vid uppsägning av enheten eller tjänstens anslutning till slutpunkten för direktuppspelning och tjänsten och enheten är ansvarig för att återskapa stream, om det behövs.


## <a name="connectivity-requirements"></a>Krav för anslutning

Både enheten och tjänsten sidor av en dataström med enheten måste kunna upprätta TLS-aktiverade anslutningar till IoT Hub och dess slutpunkt för direktuppspelning. Detta kräver utgående anslutningar via port 443 till dessa slutpunkter. Värdnamnet som associeras med dessa slutpunkter finns på den *översikt* fliken IoT-hubb, enligt bilden nedan: ![Alternativ text](./media/iot-hub-device-streams-overview/device-stream-portal.png "enheten stream slutpunkter")

Du kan också slutpunkter information kan använda hämtas med hjälp av Azure CLI under hubbens properties-avsnittet, mer specifikt `property.hostname` och `property.deviceStreams` nycklar.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

Utdata är en JSON-objekt för alla slutpunkter som din hubb enheten och tjänsten kan behöva ansluta till för att upprätta en dataström med enheten.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Se till att du har installerat Azure CLI version 2.0.57 eller senare. Du kan hämta den senaste versionen [här](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
> 

## <a name="whitelist-device-streaming-endpoints"></a>Lista över tillåtna enheter-slutpunkter för direktuppspelning

Som vi redan nämnt [tidigare](#overview), enheten skapar en utgående anslutning till slutpunkten för direktuppspelning av IoT Hub under enheten strömmar initiation process. Dina brandväggar på enheten eller nätverket måste tillåta utgående anslutning till strömmande gatewayen via port 443 (Observera att kommunikationen sker via en WebSocket-anslutning som har krypterats med TLS).

Värdnamnet för slutpunkten för direktuppspelning av enheten finns på Azure IoT Hub-portalen under fliken Översikt. ![Alternativ text](./media/iot-hub-device-streams-overview/device-stream-portal.PNG "enheten stream slutpunkter")

Du kan också hitta den här informationen med hjälp av Azure CLI:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Se till att du har installerat Azure CLI version 2.0.57 eller senare. Du kan hämta den senaste versionen [här](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
> 

## <a name="troubleshoot-via-device-streams-activity-logs"></a>Felsök via enheten strömmar aktivitetsloggar

Du kan ställa in Azure Monitor-loggar att samla in aktivitetsloggen på strömmar av enheten i IoT Hub. Det kan vara mycket användbart vid felsökning av scenarier.

Följ stegen nedan för att konfigurera Azure Monitor-loggar för din IoT-hubb enheten stream aktiviteter:

1. Navigera till den *diagnostikinställningar* i din IoT-hubb och klicka på *slå på diagnostik* länk.

   ![Alternativ text](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings.PNG "aktiverar disgnostics loggar")


2. Ange ett namn för diagnostikinställningar och välj *skicka till Log Analytics* alternativet. Vägleds du vill välja en befintlig resurs i Log Analytics-arbetsyta eller skapa en ny. Dessutom bör du läsa den *DeviceStreams* i listan.

    ![Alternativ text](./media/iot-hub-device-streams-overview/device-streams-diagnostics.PNG "Aktivera enheten strömmar loggar")

3. Du kan nu komma åt enheten strömmar loggarna under den *loggar* fliken i din IoT-hubb-portalen. Strömma aktivitetsloggar för enheten visas i den `AzureDiagnostics` tabell och ha `Category=DeviceStreams`.

    <p>
Identiteten för målenheten och resultatet av åtgärden är också tillgängliga i loggarna som visas nedan.
    ![Alternativ text](./media/iot-hub-device-streams-overview/device-streams-log-analytics.PNG "komma åt enheten strömningsloggar")


## <a name="regional-availability"></a>Regional tillgänglighet

Allmänt tillgängliga förhandsversionen är strömmar för IoT Hub-enhet tillgängliga i regionerna centrala USA och centrala USA – EUAP. Kontrollera att du skapar din hubb i någon av dessa regioner. 


## <a name="sdk-availability"></a>SDK-tillgänglighet

Två sidor av varje dataström (på enheten och tjänsten sida) använder IoT Hub-SDK för att upprätta tunneln. Allmänt tillgängliga förhandsversionen kan kunder välja mellan följande SDK språk:
- C och C# SDK: er stöder enheten strömmar på enheten.

- NodeJS och C# SDK har stöd för enheten strömmar på serversidan.


## <a name="iot-hub-device-stream-samples"></a>IoT Hub Device Stream-exempel

Vi har publicerat två [Snabbstart exempel](/azure/iot-hub) till demonstrerar användningen av enheten strömmar av program.
* Den *echo* exempel visar programmässig användning av enheten strömmar (genom att anropa API SDK: er direkt).
* Den *lokal proxy* exempel visar tunneling trafiktyper startklara klient/server-program (till exempel SSH, RDP eller web) via enheten strömmar.

De här exemplen beskrivs mer i detalj nedan.

### <a name="echo-sample"></a>Echo-exempel
Echo-exempel visar programmässig användning av enheten strömmar för att skicka och ta emot byte mellan tjänsten och program. Använd länkarna nedan för att få åtkomst till snabbstartguider. Observera att du kan använda tjänsten och program i olika språk, t.ex. C enhetsprogram kan arbeta med C# serviceprogram.

| SDK    | Tjänstprogrammet                                          | Enhetsprogram                                           |
|--------|----------------------------------------------------------|----------------------------------------------------------|
| C#     | [Länk](quickstart-device-streams-echo-csharp.md) | [Länk](quickstart-device-streams-echo-csharp.md) |
| Node.js | [Länk](quickstart-device-streams-echo-nodejs.md) | -                                                        |
| C      | -                                                        | [Länk](quickstart-device-streams-echo-c.md)      |

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Exempel på lokal Proxy (för SSH eller RDP)
Lokal proxy-exempel visar ett sätt att aktivera tunnlar för trafik i ett befintligt program som omfattar kommunikation mellan en klient och en serverprogram. Den här konfigurera fungerar för klient/server-protokoll som SSH och ansluter via RDP, där tjänstsidan fungerar som en klient (SSH eller RDP-klientprogram som körs), och enheten sida som fungerar som servern (som kör daemon för SSH eller RDP-serverprogram). 

Det här avsnittet beskriver användningen av enheten strömmar för att låta användarna SSH till en enhet över enheten strömmar (fallet med RDP eller annat klient/server-program är liknande med hjälp av det protokoll motsvarande port).

Installationen använder två *lokal proxy* program visas i bilden nedan, nämligen *enhet-lokal proxy* och *service-lokal proxy*. Lokal proxy-program är ansvarig för att utföra den [enheten stream initiation handskakning](#device-stream-creation-flow) med IoT Hub och interagera med SSH-klient och SSH-daemon med hjälp av vanliga klient/server sockets.

![Alternativ text](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png "Enhetsinställningar stream proxy för SSH/RDP")

1. Användaren kör service-lokal proxy för att initiera en enhet ström till enheten.

2. Proxy för lokal enhet accepterar initiation strömningsbegäran och tunneln har upprättats till slutpunkten för direktuppspelning av IoT Hub (som beskrivs ovan).

3. Proxy för lokal enhet ansluter till SSH-daemon-slutpunkten lyssnar på port 22 på enheten.

4. Service-lokal proxy lyssnar på en avsedda port som väntar på ny SSH-anslutningar från användaren (port 2222 som används i exemplet, men detta kan konfigureras att någon annan port). Användaren pekar proxyporten lokal tjänst på localhost SSH-klienten.

### <a name="notes"></a>Anteckningar
- En slutpunkt till slutpunkt-tunnel mellan SSH-klient (till höger) till SSH-daemon (till vänster) för att slutföra stegen ovan. En del av den här slutpunkt till slutpunkt-anslutning innebär att skicka trafik via en dataström med enheten till IoT Hub.

- Pilarna i bilden anger riktningen där anslutningar upprättas mellan slutpunkter. Observera särskilt att det finns inga inkommande anslutningar kommer att enheten (detta är ofta blockeras av en brandvägg).

- Välja att använda port 2222 på service-lokal proxy är ett godtyckligt val. Proxyn kan konfigureras för att använda någon annan port.

- Valet av port 22 är i det här fallet procotocol-beroende och specifikt för SSH. I fallet med RDP måste port 3389 användas. Detta kan konfigureras i de angivna exempelprogram.

Använd länkarna nedan för instruktioner om hur du kör lokal proxy-program i valfritt språk. Liknar den [echo exemplet](#echo-sample), du kan köra enheten och tjänsten lokal proxy-program på olika språk som de är helt kompatibelt.

| SDK    | Service-lokal Proxy                                       | Enhet-lokal Proxy                                |
|--------|-----------------------------------------------------------|---------------------------------------------------|
| C#     | [Länk](quickstart-device-streams-proxy-csharp.md)  | [Länk](quickstart-device-streams-proxy-csharp.md) |
| NodeJS | [Länk](quickstart-device-streams-proxy-nodejs.md)         | -                                                 |
| C      | -                                                         | [Länk](quickstart-device-streams-proxy-c.md)      |

## <a name="next-steps"></a>Nästa steg

Använd länkarna nedan om du vill läsa mer om enhetsströmmar:

> [!div class="nextstepaction"]
> [Enheten strömmar på IoT visa (Channel 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
> [prova en enhet stream Snabbstartsguide](/azure/iot-hub)
