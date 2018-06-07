---
title: ta med fil
description: ta med fil
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/25/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f27dbf20eab5866ac3061d888c17e970a1d197f7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34666763"
---
# <a name="azure-and-the-internet-of-things"></a>Azure och Sakernas Internet

Välkommen till Microsoft Azure och Sakernas Internet (Internet of Things, IoT). Den här artikeln beskriver de vanligast förekommande egenskaperna för en IoT-lösning i molnet. IoT-lösningar kräver säker, dubbelriktad kommunikation mellan enheter, potentiellt miljontals, samt en serverdelslösning. En lösning kan exempelvis använda automatiserad, förutsägande analys för att få fram insikter från din enhet-till-molnet händelseström.

## <a name="iot-solution-architecture"></a>IoT-lösningsarkitektur

I följande diagram visas nyckelelement för en typisk IoT-lösningsarkitektur. Diagrammet är oberoende av specifik implementeringsinformation, till exempel vilka Azure-tjänster som används och enhetens operativsystem. I den här lösningen samlar IoT-enheter in data som de sedan skickar till en molngateway. Molngatewayen tillhandahåller data för bearbetning av andra serverdeltjänster. Dessa serverdelstjänster kan skicka data till:

* Andra verksamhetsspecifika program.
* Mänskliga operatörer via en instrumentpanel eller annan presentationsenhet.

![IoT-lösningsarkitektur][img-solution-architecture]

> [!NOTE]
> En detaljerad beskrivning av IoT-arkitekturen finns i [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Referensarkitektur för Microsoft Azure IoT).

### <a name="device-connectivity"></a>Enhetsanslutning

I en IoT-lösningsarkitektur skickar enheter vanligtvis telemetri till molnet för lagring och bearbetning. Till exempel, i ett scenario med förutsägande underhåll kan lösningen för serverdelen använda sig av strömmen med sensordata för att fastställa när en specifik pump kräver underhåll. Enheter kan också ta emot och svara på meddelanden från moln till enhet genom att läsa meddelanden från en molnslutpunkt. I samma exempel kan serverdelen av lösningen skicka meddelanden till andra pumpar på pumpstationen för att börja dirigera om flöden strax innan underhållet ska påbörjas. Den här proceduren säkerställer att underhållsteknikern kan sätta igång så fort han anländer.

Att ansluta enheter säkert och tillförlitligt är ofta den största utmaningen för IoT-lösningar. Det här beror på att IoT-enheter skiljer sig från andra klienter som till exempel webbläsare och mobilappar. IoT-enheter är mer specifikt:

* Ofta inbyggda system utan mänskliga operatörer (till skillnad från en telefon).
* Kan distribueras på avlägsna platser, där fysisk åtkomst är dyr.
* Kan i vissa fall enbart nås via lösningens serverdel. Det finns inget annat sätt att interagera med enheten.
* Kan ha begränsade ström- och bearbetningsresurser.
* Kan ha oregelbunden, långsam eller dyr nätverksanslutning.
* Kan behöva använda privatägda, anpassade eller branschspecifika programprotokoll.
* Kan skapas med en stor mängd populära maskinvaru- och programvaruplattformar.

Utöver föregående restriktioner måste alla IoT-lösningar också vara skalbara, säkra och tillförlitliga.

Beroende på vilket kommunikationsprotokoll som används och nätverkstillgänglighet kan en enhet antingen kommunicera med molnet direkt eller via en mellanliggande gateway. IoT-arkitekturer har ofta en blandning av dessa två kommunikationsmönster.

### <a name="data-processing-and-analytics"></a>Databearbetning och analys

I moderna IoT-lösningar kan databearbetning ske i molnet eller på enheten. Bearbetning på enheten kallas *Edge computing* (Edge-databehandling). Valet av var du vill bearbeta data beror på faktorer som:

* Nätverksbegränsningar. Om bandbredden mellan enheterna och molnet är begränsad så kan det vara en anledning att utföra mer edge-bearbetning.
* Svarstid. Om det finns ett krav på att agera på en enhet i nära realtid kan det vara bättre att bearbeta svaret på själva enheten. Till exempel om en robotarm måste stoppas i ett nödfall.
* Föreskriftsmiljö. Vissa data kan inte skickas till molnet.

I allmänhet är edge-databehandling och i databehandling i molnet en kombination av följande funktioner:

* Ta emot stora mängder telemetri från dina enheter och bestämma hur telemetridata bearbetas och lagras.
* Analysera telemetri för att ge insikter, oavsett om de skickas i realtid eller i efterhand.
* Skicka kommandon från molnet eller en gateway-enhet till en specifik enhet.

Dessutom bör en serverdel för IoT-molnet tillhandahålla:

* Funktioner för enhetsregistrering för att:
    * Etablera enheter.
    * Styra vilka enheter som tillåts att ansluta till din infrastruktur.
* Enhetshantering för att kontrollera status för enheter och övervaka deras verksamhet.

I ett förutsägande underhållsscenario lagrar till exempel molnserverdelen historisk telemetridata. Lösningen använder dessa data för att identifiera potentiellt avvikande beteenden på specifika pumpar innan de orsakar verkliga problem. Med hjälp av dataanalys kan den identifiera att den förebyggande lösningen ska skicka ett kommando till enheten för att vidta en korrigerande åtgärd. Den här processen skapar en automatisk feedback-loop mellan enheten och molnet som ökar effektiviteten för lösningen dramatiskt.

### <a name="presentation-and-business-connectivity"></a>Presentation och företagsanslutningar

Slutanvändarna kan interagera med IoT-lösningen och enheterna via ett presentations- och företagsanslutningsskikt. I det här skiktet kan användare se och analysera data som samlats in från deras enheter. Vyerna kan bestå av instrumentpaneler eller BI-rapporter som kan visa både historiska data eller nästan realtidsdata. En operatör kan exempelvis kontrollera statusen för en viss pumpstation och se alla varningar som har utlösts i systemet. Här kan även IoT-lösningens serverdel integreras med befintliga branschspecifika program för att knytas samman med företagets verksamhetsprocesser eller arbetsflöden. En lösningsaccelerator för förutsägande underhåll kan till exempel integreras med ett system för schemaläggning som bokar in en ingenjör att besöka en pumpstation när en pump är i behov av underhåll.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-accelerators/iot-accelerators-what-are-solution-accelerators.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT solution accelerators]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
