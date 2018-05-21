---
title: Introduktion till Sakernas Internet (IoT) i Azure
description: Översikt över Azure IoT samt relaterade tjänster och tekniker.
services: iot-hub
documentationcenter: ''
author: BryanLa
manager: timlt
editor: bryanla
ms.service: iot
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: bryanla; v-clay
layout: LandingPage
ms.openlocfilehash: 568680d2e1eb8c09fa124a5302bb7170ba4af352
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="introduction-to-azure-and-the-internet-of-things"></a>Introduktion till Azure och Sakernas Internet

Azure IoT består av tre områden av tekniker och lösningar – lösningar, plattformstjänster och gränser; alla utformade för att underlätta utvecklingen av ditt IoT-program. Artikeln börjar med en beskrivning av de gemensamma egenskaperna för en IoT-molnlösning, följt av en översikt över hur Azure IoT löser problem i IoT-projekt och varför vi rekommenderar Azure IoT.

## <a name="iot-solution-architecture"></a>IoT-lösningsarkitektur

IoT-lösningar kräver säker, dubbelriktad kommunikation mellan enheter, potentiellt miljontals, samt en serverdelslösning. En lösning kan exempelvis använda automatiserad, förutsägande analys för att få fram insikter från din enhet-till-molnet händelseström. 

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

Slutanvändarna kan interagera med IoT-lösningen och enheterna via ett presentations- och företagsanslutningsskikt. I det här skiktet kan användare se och analysera data som samlats in från deras enheter. Vyerna kan bestå av instrumentpaneler eller BI-rapporter som kan visa både historiska data eller nästan realtidsdata. En operatör kan exempelvis kontrollera statusen för en viss pumpstation och se alla varningar som har utlösts i systemet. Här kan även IoT-lösningens serverdel integreras med befintliga branschspecifika program för att knytas samman med företagets verksamhetsprocesser eller arbetsflöden. En förutsägande underhållslösning kan till exempel integreras med ett system för schemaläggning som bokar in en ingenjör att besöka en pumpstation när en pump är i behov av underhåll.

## <a name="why-azure-iot"></a>Varför Azure IoT?

Azure IoT förenklar IoT-projekt och löser problem till exempel med säkerhet, inkompatibilitet mellan infrastrukturer och skalning av IoT-lösningen. Här är hur:

**Flexibel** <br>
Accelerera din IoT-resa
* Skala: börja litet och utöka till önskad storlek, var som helst och överallt – upp till miljoner enheter och terabyte av data i de flesta regioner över hela världen.

* Öppna: använd vad du har, eller modernisera för framtiden genom att ansluta till enheter, programvara och tjänster enligt behov.

* Hybrid: skapa efter behov genom att distribuera IoT-lösningen vid gränsen, i molnet eller var som helst däremellan.

* Takt: distribuera snabbare, med snabbare tid till marknad och hålls steget före konkurrenterna inom IoT-lösningar och -innovationer.

**Omfattande** <br>
Påverka marknaden

* Fullständig: Microsoft är den enda IoT-lösningsleverantören med en fullständig plattform från enheter till moln med funktioner för stordata och analys samt hanterade tjänster.

* Lyckas med partner: utnyttja världens största partnernätverk och ta i bruk affärsprogram och nya tekniker över branschgränser runtom i världen.

* Datadriven: IoT handlar om data, och de bästa IoT-lösningarna tillhandahåller alla verktyg du behöver för att lagra, tolka, transformera, analysera och presentera data för rätt användare på rätt plats vid rätt tidpunkt.

* Enhetscentrerad: med Microsoft IoT kan du ansluta allt, från äldre utrustning till ett brett spektrum av certifierad maskinvara, med möjligheten att skapa egna enheter över såväl gränssystem som mobila och inbäddade system.

**Skydda** <br>
Lös den svåraste delen av IoT – säkerhet

* Tillhandahåll: med Microsoft IoT samordnar du din vision med teknik, bästa praxis och funktioner för att lösa den svåraste delen av IoT – säkerhet.

* Åtgärda: skydda dina IoT-data och hantera risker med identitets-, åtkomst- och säkerhetshantering samt skydd mot hot och skydd av information.

* Sinnesro: säkerställ känslig information mellan enheter, programvara, program och molntjänster samt lokala miljöer.

* Efterlevnad: Microsoft är ledande i branschen med säkerhetskrav som uppfyller en bred uppsättning internationella och branschspecifika standarder för IoT-enheter, data och tjänster.

## <a name="next-steps"></a>Nästa steg

Läs mer om följande tekniker och lösningar, eller bekanta dig med listan över Azure IoT-tjänster i innehållsförteckningen till vänster.

<ul class="panelContent cardsF">  
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Lösningar</h3>
                        <a href="/azure/iot-suite">IoT-lösningsacceleratorer</a><br/>
                        <a href="/azure/iot-central">IoT Central</a>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Plattformstjänster</h3>
                        <a href="/azure/iot-hub">IoT Hub</a><br/>
                        <a href="/azure/iot-dps">IoT Hub Device Provisioning Service</a><br/>
                        <a href="/azure/azure-maps">Maps</a><br/>
                        <a href="/azure/time-series-insights">Time Series Insights</a>
                    </div>
                </div>
            </div>
        </div>
    </li>  
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Edge</h3>
                        <a href="/azure/iot-edge">IoT Edge</a><br/>
                        <a href="/azure/iot-edge/how-iot-edge-works">Vad är IoT Edge?</a>
                    </div>
                </div>
            </div>
        </div>
    </li>      
</ul>

[img-paas-saas-technologies-solutions]: media/index/paas-saas-technologies-solutions.png
[img-solution-architecture]: ./media/iot-introduction/iot-reference-architecture.png
[img-dashboard]: ./media/iot-introduction/iot-suite.png

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-iot-central-land]: https://docs.microsoft.com/microsoft-iot-central/
[lnk-iot-dps-land]: /azure/iot-dps/index.yml
[lnk-iot-edge-land]: /azure/iot-edge/index.yml
[lnk-iot-hub-land]: /azure/iot-hub/index.md
[lnk-iot-maps-land]: /azure/maps/index.yml
[lnk-iot-sa-land]: /azure/iot-suite/index.md
[lnk-iot-tsi-land]: /azure/time-series-insights/index.yml

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-sa]: ../articles/iot-suite/iot-suite-overview.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf


