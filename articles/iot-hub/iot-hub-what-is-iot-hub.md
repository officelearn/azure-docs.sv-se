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
ms.date: 06/16/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: d290ee8a3cee1242f14726b5cf7ca531a3f5830a
ms.contentlocale: sv-se
ms.lasthandoff: 06/20/2017


---
# <a name="overview-of-the-azure-iot-hub-service"></a>Översikt över tjänsten Azure IoT Hub

Välkommen till Azure IoT Hub. Den här artikeln innehåller en översikt över Azure IoT Hub och beskriver varför du bör använda den här tjänsten när du implementerar en IoT-lösning (Internet of Things). Azure IoT Hub är en helt hanterad tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan flera miljoner IoT-enheter och som tillhandahåller serverdelen för lösningar av den här typen. Azure IoT Hub:

* Tillhandahåller flera alternativ för kommunikation från enhet till moln och från moln till enhet, inklusive enkelriktade meddelanden, filöverföringar och begäran/svar-metoder.
* Tillhandahåller inbyggd deklarativ meddelanderoutning till andra Azure-tjänster.
* Tillhandahåller frågbart lager för enhetsmetadata och synkroniserad tillståndsinformation.
* Kan skydda kommunikation och åtkomstkontroll med säkerhetsnycklar eller X.509-certifikat för varje enhet.
* Tillhandahåller omfattande övervakning för händelser relaterade till enhetsanslutningar och enhetsidentiteter.
* Tillhandahåller enhetsbibliotek för de mest populära språken och plattformarna.

Artikeln [Jämförelse av IoT Hub och Event Hubs][lnk-compare] beskriver de viktigaste skillnaderna mellan de här två tjänsterna och visar fördelarna med att använda IoT Hub i dina IoT-lösningar.

Mer information om hur Azure och IoT Hub hjälper dig att skydda din IoT-lösning finns i [Internet of Things security from the ground up][lnk-security-ground-up] (Internet of Things-säkerhet från grunden).

![Azure IoT Hub som moln-gateway i IoT-lösningar][img-architecture]

> [!NOTE]
> En detaljerad beskrivning av IoT-arkitekturen finns i [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Referensarkitektur för Microsoft Azure IoT).

## <a name="iot-device-connectivity-challenges"></a>Utmaningar med IoT-enhetsanslutningar

IoT Hub och enhetsbiblioteken hjälper dig att på ett tillförlitligt och säkert sätt ansluta enheter till serverdelen i lösningen. IoT-enheter:

* Är ofta inbyggda system utan mänsklig operatör.
* Kan finnas på avlägsna platser där fysisk åtkomst är dyr.
* Kan i vissa fall enbart nås via lösningens serverdel.
* Kan ha begränsade ström- och bearbetningsresurser.
* Kan ha oregelbunden, långsam eller dyr nätverksanslutning.
* Kan behöva använda patentskyddade, anpassade eller branschspecifika programprotokoll.
* Kan skapas med en stor mängd populära maskinvaru- och programvaruplattformar.

Förutom kraven ovan behöver alla IoT-lösningar också erbjuda skalbarhet, säkerhet och tillförlitlighet. Den resulterande uppsättningen anslutningskrav är svår och tidskrävande att implementera med traditionella tekniker, till exempel webbehållare och asynkrona meddelandeköer.

## <a name="why-use-azure-iot-hub"></a>Varför ska du använda Azure IoT Hub?

Förutom ett flertal alternativ för kommunikation [från enhet till moln][lnk-d2c-guidance] och [från moln till enhet][lnk-c2d-guidance], inklusive meddelanden, filöverföringar och begäran/svar-metoder, kan Azure IoT Hub hantera enhetsanslutningar på följande sätt:

* **Enhetstvillingar**. Med hjälp av [enhetstvillingar][lnk-twins] kan du lagra, synkronisera och fråga efter enhetsmetadata och tillståndsinformation. Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd (metadata, konfigurationer och villkor). IoT Hub lagrar en enhetstvilling för varje enhet som du ansluter till IoT Hub.

* **Autentisering per enhet och säkra anslutningar**. Du kan etablera varje enhet med dess [säkerhetsnyckel][lnk-devguide-security] så att den kan ansluta till IoT Hub. [IoT Hub-identitetsregistret][lnk-devguide-identityregistry] lagrar enhetsidentiteter och nycklar i en lösning. Serverdelen i lösningen kan lägga till enskilda enheter på vitlistor eller svartlistor för att ge fullständig kontroll över enhetsåtkomsten.

* **Routa enhet-till-moln-meddelanden till Azure-tjänster som baseras på deklarativa regler**. Med IoT Hub kan du definiera meddelandevägar baserade på hanteringsregler och styra vart hubben ska skicka enhet-till-moln-meddelandena. Hanteringsreglerna kräver inte att du skriver någon kod, och de kan ersätta anpassade avsändare av postpåfyllningsmeddelanden.

* **Övervaka enhetsanslutningsåtgärder**. Du kan få detaljerade åtgärdsloggar om enhetsidentitetshanteringsåtgärder och enhetsanslutningshändelser. Denna övervakning gör att din IoT-lösning kan identifiera anslutningsproblem, t.ex. om enheter försöker ansluta med fel autentiseringsuppgifter, skickar meddelanden för ofta eller avvisar alla meddelanden från molnet till enheten.

* **En omfattande uppsättning enhetsbibliotek**. [SDK:er för Azure IoT-enheter][lnk-device-sdks] är tillgängliga och stöds för olika språk och plattformar – C för många Linux-distributioner, Windows och realtidsoperativsystem. SDK:erna för Azure IoT-enheter stöder även hanterade språk som C#, Java och JavaScript.

* **IoT-protokoll och utökningsbarhet**. Om din lösning inte kan använda enhetsbiblioteken exponerar IoT Hub ett offentligt protokoll som gör att enheter kan använda MQTT v3.1.1-, HTTP 1.1- och AMQP 1.0-protokoll. Du kan också utöka IoT Hub om du vill skapa stöd för anpassade protokoll genom att:

  * Skapa en fält-gateway med [Azure IoT Edge][lnk-iot-edge] som konverterar ditt anpassade protokoll till något av de tre protokollen som IoT Hub förstår.
  * Anpassa [protokoll-gatewayen i Azure IoT][protocol-gateway], en komponent med öppen källkod som körs i molnet.

* **Skalning**. Azure IoT Hub kan skalas till flera miljoner samtidiga anslutna enheter och flera miljoner händelser per sekund.

## <a name="gateways"></a>Gateways

En gateway i en IoT-lösning är vanligtvis antingen en [protokoll-gateway][lnk-iotedge] som har distribuerats i molnet eller en [fält-gateway][lnk-field-gateway] som har distribuerats lokalt med dina enheter. En protokoll-gateway utför protokollöversättning, t.ex. MQTT till AMQP. En fält-gateway kan köra analyser i gränsnätverket, fatta tidskänsliga beslut för att minska svarstiderna, tillhandahålla enhetshanteringstjänster, genomdriva säkerhets- och sekretessbegränsningar samt även utföra protokollöversättning. Båda typerna av gateways fungerar som en mellanhand mellan dina enheter och IoT Hub.

En fält-gateway skiljer sig från en enkel trafikroutningsenhet (till exempel en enhet för översättning av nätverksadresser eller en brandvägg) eftersom den vanligtvis har en aktiv roll i hanteringen av åtkomst- och informationsflödet i din lösning.

En lösning kan omfatta både protokoll- och fält-gateways.

## <a name="how-does-iot-hub-work"></a>Hur fungerar IoT Hub?

Azure IoT Hub implementerar [tjänstassisterad kommunikation][lnk-service-assisted-pattern] för att hantera interaktionen mellan dina enheter och serverdelen i lösningen. Målet med tjänstassisterad kommunikation är att upprätta tillförlitliga, dubbelriktade kommunikationsvägar mellan ett kontrollsystem, t.ex IoT Hub, och enheter för särskilda ändamål som har distribuerats i ett icke betrott fysiskt utrymme. Mönstret tillämpar följande principer:

* Säkerhet prioriteras högre än alla andra funktioner.

* Enheter accepterar inte oönskad nätverksinformation. En enhet upprättar alla anslutningar och vägar som ”endast utgående”. För att en enhet ska ta emot ett kommando från lösningens serverdel måste enheten regelbundet initiera en anslutning för att söka efter kommandon som väntar på att bearbetas.

* Enheter får endast ansluta till eller upprätta vägar till välkända tjänster som de är peerkopplade med, t.ex. IoT Hub.

* Kommunikationsvägen mellan enheten och tjänsten eller mellan enheten och gatewayen skyddas på programprotokollnivå.

* Auktoriseringen och autentiseringen på systemnivå baseras på enhetsspecifika identiteter. De gör att autentiseringsuppgifter och behörigheter för åtkomst kan återkallas nästan omedelbart.

* Dubbelriktad kommunikation för enheter som ansluter sporadiskt på grund av kapacitets- eller anslutningsbegränsningar förenklas genom att kommandon och enhetsmeddelanden hålls kvar tills en enhet ansluter för att ta emot dem. IoT Hub underhåller enhetsspecifika köer för de kommandon som tjänsten skickar.

* Data om programmets nyttolast skyddas separat för säker överföring via gateways till en viss tjänst.

Mobilbranschen har använt tjänstassisterad kommunikation i extremt hög skala för att implementera push-meddelandetjänster som [Windows Push Notification Services][lnk-wns], [Google Cloud Messaging][lnk-google-messaging] och [Apple Push Notification Service][lnk-apple-push].

IoT Hub stöds över ExpressRoutes offentliga peering-sökväg.

## <a name="next-steps"></a>Nästa steg

Information om hur du skickar meddelanden från en enhet och tar emot dem från IoT Hub och hur du konfigurerar meddelandevägar finns i [Skicka och ta emot meddelanden med IoT Hub][lnk-send-messages].

Information om hur IoT Hub tillhandahåller standardbaserad enhetshantering som hjälper dig att hantera, konfigurera och uppdatera dina enheter finns i [Översikt över enhetshantering med IoT Hub][lnk-device-management].

Om du vill implementera klientprogram på många olika enhetsspecifika maskinvaruplattformar och operativsystem kan du använda enhets-SDK:er för Azure IoT. Enhets-SDK:erna innehåller bibliotek som gör det lätt att skicka telemetri till en IoT-hubb och att ta emot meddelanden från molnet till enheten. När du använder enhets-SDK:erna kan du välja mellan olika nätverksprotokoll för att kommunicera med IoT Hub. Mer information finns i [informationen om enhets-SDK:er][lnk-device-sdks].

Information om hur du börjar skriva kod och hur du kan köra några exempel finns i självstudien [Komma igång med IoT Hub][lnk-get-started].

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
[lnk-iot-edge]: https://github.com/Azure/iot-edge
[lnk-send-messages]: iot-hub-devguide-messaging.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-security-ground-up]: iot-hub-security-ground-up.md

