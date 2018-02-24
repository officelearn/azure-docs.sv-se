---
title: "Översikt över Azure IoT Hub | Microsoft Docs"
description: "Översikt över tjänsten Azure IoT Hub: Vad är IoT Hub, enhetsanslutning, IoT-kommunikationsmönster (Internet of Things), gateways och tjänstassisterat kommunikationsmönster"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b00c89183ff0d4e7df49d29834508643e68246bb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="overview-of-the-azure-iot-hub-service"></a>Översikt över tjänsten Azure IoT Hub

Välkommen till Azure IoT Hub. Den här artikeln innehåller en översikt över Azure IoT Hub och beskriver varför du bör använda den här tjänsten när du implementerar en IoT-lösning (Internet of Things). Azure IoT Hub är en helt hanterad tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan flera miljoner IoT-enheter och som tillhandahåller en serverdel för lösningar av den här typen. Azure IoT Hub:

* Ger flera alternativ för kommunikation från enhet till moln och från moln till enhet. Dessa alternativ omfattar enkelriktade meddelanden, filöverföring och begäran/svar-metoder.
* Tillhandahåller inbyggd deklarativ meddelanderoutning till andra Azure-tjänster.
* Tillhandahåller frågningsbart lager för enhetsmetadata och synkroniserad tillståndsinformation.
* Kan skydda kommunikation och åtkomstkontroll med säkerhetsnycklar eller X.509-certifikat för varje enhet.
* Tillhandahåller omfattande övervakning för händelser relaterade till enhetsanslutningar och enhetsidentiteter.
* Innehåller enhetsbibliotek för de mest populära språken och plattformarna.

Artikeln [Jämförelse av IoT Hub och Event Hubs][lnk-compare] beskriver de viktigaste skillnaderna mellan de här två tjänsterna och visar fördelarna med att använda IoT Hub i dina IoT-lösningar.

Mer information om hur Azure och IoT Hub hjälper dig att skydda din IoT-lösning finns i [Internet of Things security from the ground up][lnk-security-ground-up] (Internet of Things-säkerhet från grunden).

![Azure IoT Hub som molngateway i IoT-lösningar][img-architecture]

> [!NOTE]
> En detaljerad beskrivning av IoT-arkitekturen finns i [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Referensarkitektur för Microsoft Azure IoT).

## <a name="iot-device-connectivity-challenges"></a>Utmaningar med IoT-enhetsanslutningar

IoT Hub och enhetsbiblioteken hjälper dig att på ett tillförlitligt och säkert sätt ansluta enheter till serverdelen i lösningen. IoT-enheter:

* Är ofta inbyggda system utan en mänsklig operatör.
* Kan finnas på avlägsna platser där fysisk åtkomst är dyr.
* Kan i vissa fall enbart nås via lösningens serverdel.
* Kan ha begränsade ström- och bearbetningsresurser.
* Kan ha oregelbunden, långsam eller dyr nätverksanslutning.
* Kan behöva använda privatägda, anpassade eller branschspecifika programprotokoll.
* Kan skapas med en stor mängd populära maskinvaru- och programvaruplattformar.

Förutom kraven ovan behöver alla IoT-lösningar också erbjuda skalbarhet, säkerhet och tillförlitlighet. Den resulterande uppsättningen anslutningskrav är svår och tidskrävande att implementera med traditionella tekniker, till exempel webbehållare och asynkrona meddelandeköer.

## <a name="why-use-azure-iot-hub"></a>Varför ska du använda Azure IoT Hub?

Azure IoT Hub har en stor uppsättning kommunikationsalternativ från [enhet till moln][lnk-d2c-guidance] och från [moln till enhet][lnk-c2d-guidance]. Azure IoT Hub bemöter dessutom utmaningarna med att ansluta till enheter på ett säkert och tillförlitligt sätt så här:

* **Enhetstvillingar**. Med hjälp av [enhetstvillingar][lnk-twins] kan du lagra, synkronisera och fråga efter enhetsmetadata och tillståndsinformation. Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd, som metadata, konfigurationer och villkor. IoT Hub lagrar en enhetstvilling för varje enhet som du ansluter till IoT Hub.

* **Autentisering per enhet och säkra anslutningar**. Du kan etablera varje enhet med dess [säkerhetsnyckel][lnk-devguide-security] så att den kan ansluta till IoT Hub. [IoT Hub-identitetsregistret][lnk-devguide-identityregistry] lagrar enhetsidentiteter och nycklar i en lösning. Serverdelen i lösningen kan lägga till enskilda enheter på vitlistor eller svartlistor för att ge fullständig kontroll över enhetsåtkomsten.

* **Routa enhet-till-moln-meddelanden till Azure-tjänster som baseras på deklarativa regler**. Med IoT Hub kan du definiera meddelandevägar baserade på routningsregler och styra vart hubben ska skicka enhet-till-moln-meddelandena. Du behöver inte skriva någon kod för att skapa routningsregler och de kan ersätta anpassade meddelandehanterare för efterinmatning.

* **Integrera IoT Hub-händelser i dina affärsprogram**. IoT Hub kan integreras med Azure Event Grid. Använd den här integreringen till att konfigurera andra Azure-tjänster eller tredjepartsprogram att lyssna efter IoT Hub-händelser. Med Azure Event Grid kan du snabbt reagera på kritiska händelser på ett tillförlitligt, skalbart och säkert sätt.

* **Övervaka anslutningsåtgärder för enheter**. Du kan få detaljerade loggar om åtgärder för identitetshantering av enheter och om händelser för enhetsanslutningar. Den här övervakningsfunktionen gör det möjligt för IoT-lösningen att identifiera anslutningsproblem. Använd de här loggarna för att identifiera enheter som anger fel autentiseringsuppgifter, skickar meddelanden för ofta eller avvisar alla moln-till-enhet-meddelanden.

* **En omfattande uppsättning enhetsbibliotek**. [SDK:er för Azure IoT-enheter][lnk-device-sdks] är tillgängliga och stöds för olika språk och plattformar – C för många Linux-distributioner, Windows och realtidsoperativsystem. SDK:erna för Azure IoT-enheter stöder även hanterade språk som C#, Java och JavaScript.

* **IoT-protokoll och utökningsbarhet**. Om lösningen inte kan använda enhetsbiblioteken exponerar IoT Hub ett offentligt protokoll som gör att enheter kan använda MQTT v3.1.1-, HTTPS 1.1- och AMQP 1.0-protokoll. Du kan också utöka IoT Hub om du vill skapa stöd för anpassade protokoll genom att:

  * Skapa en fältgateway med [Azure IoT Edge][lnk-iot-edge] som konverterar ditt anpassade protokoll till ett protokoll som IoT Hub förstår.
  * Anpassa [protokollgatewayen i Azure IoT][protocol-gateway], en komponent med öppen källkod som körs i molnet.

* **Skalning**. Azure IoT Hub kan skalas till flera miljoner samtidiga anslutna enheter och flera miljoner händelser per sekund.

* **Enhetsetablering**. [IoT Hub Device Provisioning](https://docs.microsoft.com/azure/iot-dps/) är en hjälptjänst för IoT Hub som möjliggör zero-touch och just-in-time-enhetsetablering till rätt IoT-hubb utan mänsklig interaktion, så att du kan etablera miljontals enheter på ett säkert och skalbart sätt.

## <a name="gateways"></a>Gateways

En gateway i en IoT-lösning är vanligtvis antingen en [protokollgateway][lnk-iotedge] som har distribuerats i molnet eller en [fältgateway][lnk-field-gateway] som har distribuerats lokalt med dina enheter.

En _protokollgateway_ utför protokollöversättning, till exempel MQTT till AMQP.

En _fältgateway_ kan göra följande:

* Köra analys på gränsenheter.
* Fatta tidskänsliga beslut som minskar svarstiderna.
* Tillhandahålla tjänster för enhetshantering.
* Säkerställa begränsningar kring säkerhet och sekretess.
* Utföra protokollöversättning.

Båda gatewaytyperna fungerar som en mellanhand mellan dina enheter och IoT Hub.

En fältgateway skiljer sig från en enkel trafikroutningsenhet (till exempel en enhet för översättning av nätverksadresser eller en brandvägg) eftersom den vanligtvis har en aktiv roll i hanteringen av åtkomst- och informationsflödet i din lösning.

En lösning kan omfatta både protokoll- och fältgateways.

## <a name="how-does-iot-hub-work"></a>Hur fungerar IoT Hub?

Azure IoT Hub implementerar [tjänstassisterad kommunikation][lnk-service-assisted-pattern] för att hantera interaktionen mellan dina enheter och serverdelen i lösningen. Syftet med mönstret är att upprätta tillförlitliga, dubbelriktade kommunikationsvägar mellan ett kontrollsystem, t.ex IoT Hub, och enheter för särskilda ändamål i ett icke betrott fysiskt utrymme. Mönstret tillämpar följande principer:

* Säkerhet prioriteras högre än alla andra funktioner.

* Enheter accepterar inte oönskad nätverksinformation. En enhet upprättar alla anslutningar och vägar som ”endast utgående”. För att en enhet ska ta emot ett kommando från lösningens serverdel måste enheten regelbundet initiera en anslutning för att söka efter kommandon som väntar på att bearbetas.

* Enheter får endast ansluta till eller upprätta vägar till välkända tjänster som de är peerkopplade med, t.ex. IoT Hub.

* Kommunikationsvägen mellan enheten och tjänsten eller mellan tjänsten och gatewayen skyddas på programprotokollnivå.

* Auktoriseringen och autentiseringen på systemnivå baseras på enhetsspecifika identiteter. De gör att autentiseringsuppgifter och behörigheter för åtkomst kan återkallas nästan omedelbart.

* För enheter som ansluter sporadiskt på grund av kapacitets- eller anslutningsbegränsningar fungerar dubbelriktad kommunikation genom att kommandon och meddelanden hålls kvar tills en enhet ansluter för att ta emot dem. IoT Hub underhåller enhetsspecifika köer för de kommandon som tjänsten skickar.

* Data om programmets nyttolast skyddas separat för säker överföring via gateways till en viss tjänst.

Mobilbranschen har använt tjänstassisterad kommunikation för att implementera push-meddelandetjänster som [Windows Push Notification Services][lnk-wns], [Google Cloud Messaging][lnk-google-messaging] och [Apple Push Notification Service][lnk-apple-push].

IoT Hub stöds över ExpressRoutes offentliga peering-sökväg.

## <a name="next-steps"></a>Nästa steg

Information om hur du börjar skriva kod och hur du kan köra några exempel finns i självstudien [Komma igång med IoT Hub][lnk-get-started].

Information om hur du skickar meddelanden från en enhet och tar emot dem från IoT Hub och hur du konfigurerar meddelandevägar finns i [Skicka och ta emot meddelanden med IoT Hub][lnk-send-messages].

Information om hur IoT Hub tillhandahåller standardbaserad enhetshantering som hjälper dig att hantera, konfigurera och uppdatera dina enheter finns i [Översikt över enhetshantering med IoT Hub][lnk-device-management].

Om du vill implementera klientprogram på många olika enhetsspecifika maskinvaruplattformar och operativsystem kan du använda enhets-SDK:er för Azure IoT. Enhets-SDK:erna innehåller bibliotek som gör det lätt att skicka telemetri till en IoT-hubb och att ta emot meddelanden från molnet till enheten. När du använder enhets-SDK:erna kan du välja mellan olika nätverksprotokoll för att kommunicera med IoT Hub. Mer information finns i [informationen om enhets-SDK:er][lnk-device-sdks].

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Service Assisted Communication (Tjänstassisterad kommunikation) – blogginlägg av Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-iotedge]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-iot-edge]: https://docs.microsoft.com/azure/iot-edge/
[lnk-send-messages]: iot-hub-devguide-messaging.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-security-ground-up]: iot-hub-security-ground-up.md
