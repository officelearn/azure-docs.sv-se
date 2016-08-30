<properties
 pageTitle="Översikt över Azure IoT Hub | Microsoft Azure"
 description="Översikt över tjänsten Azure IoT Hub: Vad är IoT Hub, enhetsanslutning, IoT-kommunikationsmönster (Internet of Things) och tjänstassisterat kommunikationsmönster?"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="06/06/2016"
 ms.author="dobett"/>

# Vad är Azure IoT Hub?

Välkommen till Azure IoT Hub. Den här artikeln innehåller en översikt över Azure IoT Hub och beskriver varför du bör använda den här tjänsten när du implementerar en IoT-lösning (Internet of Things).

Azure IoT Hub är en helt hanterad tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan flera miljoner IoT-enheter och som tillhandahåller serverdelen för lösningar av den här typen. Azure IoT Hub:

- Erbjuder tillförlitlig meddelandehantering i stor skala från enheten till molnet och från molnet till enheten.
- Skyddar kommunikationen med autentiseringsuppgifter och åtkomstkontroll för varje enhet.
- Tillhandahåller omfattande övervakning för händelser relaterade till enhetsanslutningar och enhetsidentiteter.
- Tillhandahåller enhetsbibliotek för de mest populära språken och plattformarna.

Artikeln [Jämförelse av IoT Hub och Event Hubs][lnk-compare] beskriver de viktigaste skillnaderna mellan de här två tjänsterna och visar fördelarna med att använda IoT Hub i dina IoT-lösningar.

![Azure IoT Hub som moln-gateway i IoT-lösningar][img-architecture]

> [AZURE.NOTE] En detaljerad beskrivning av IoT-arkitekturen finns i [Referensarkitektur för Microsoft Azure IoT][lnk-refarch].

## Utmaningar med IoT-enhetsanslutningar

IoT Hub och enhetsbiblioteken hjälper dig att på ett tillförlitligt och säkert sätt ansluta enheter till serverdelen i lösningen. IoT-enheter:

- Är ofta inbyggda system utan mänsklig operatör.
- Kan finnas på avlägsna platser, där fysisk åtkomst är väldigt dyr.
- Kan i vissa fall enbart nås via lösningens serverdel.
- Kan ha begränsade ström- och bearbetningsresurser.
- Kan ha oregelbunden, långsam eller dyr nätverksanslutning.
- Kan behöva använda patentskyddade, anpassade eller branschspecifika programprotokoll.
- Kan skapas med en stor mängd populära maskinvaru- och programvaruplattformar.

Förutom kraven ovan behöver alla IoT-lösningar också erbjuda skalbarhet, säkerhet och tillförlitlighet. Den resulterande uppsättningen anslutningskrav är svår och tidskrävande att implementera med traditionella tekniker, till exempel webbehållare och asynkrona meddelandeköer.

## Varför ska du använda Azure IoT Hub?

Azure IoT Hub hanterar utmaningarna med enhetsanslutningarna på följande sätt:

-   **Autentisering per enhet och säkra anslutningar**. Du kan etablera varje enhet med dess egna [säkerhetsnyckel][lnk-devguide-security] så att den kan ansluta till IoT Hub. [IoT Hub-identitetsregistret][lnk-devguide-identityregistry] lagrar enhtesidentiteter och nycklar i en lösning. Serverdelen i lösningen kan vitlista och svartlista enskilda enheter, vilket ger fullständig kontroll över enhetsåtkomsten.

-   **Övervaka enhetsanslutningsåtgärder**. Du kan få detaljerade åtgärdsloggar om enhetsidentitetshanteringsåtgärder och enhetsanslutningshändelser. Detta gör att din IoT-lösning lätt kan identifiera anslutningsproblem, t.ex. om enheter försöker ansluta med fel autentiseringsuppgifter, skickar meddelanden för ofta eller avvisar alla meddelanden från molnet till enheten.

-   **En omfattande uppsättning enhetsbibliotek**. [SDK:er för Azure IoT-enheter][lnk-device-sdks] är tillgängliga och stöds för en rad olika språk och plattformar – C för många Linux-distributioner, Windows och realtidsoperativsystem. SDK:erna för Azure IoT-enheter stöder även hanterade språk som C#, Java och JavaScript.

-   **IoT-protokoll och utökningsbarhet**. Om din lösning inte kan använda enhetsbiblioteken exponerar IoT Hub ett offentligt protokoll som gör att enheter kan använda MQTT v3.1.1-, HTTP 1.1- och AMQP 1.0-protokoll. Du kan också utöka IoT Hub om du vill skapa stöd för anpassade protokoll genom att:

    - Skapa en fält-gateway med [SDK för Azure IoT Gateway][lnk-gateway-sdk] som konverterar ditt anpassade protokoll till något av de tre protokollen som IoT Hub förstår. 
    - Anpassa [protokoll-gatewayen i Azure IoT][protocol-gateway], en komponent med öppen källkod som körs i molnet.

-   **Skalning**. Azure IoT Hub kan skalas till flera miljoner samtidiga anslutna enheter och flera miljoner händelser per sekund.

Dessa fördelar är tillgängliga med många kommunikationsmönster. Med IoT Hub kan du för närvarande implementera följande specifika kommunikationsmönster:

-   **Händelsebaserad inhämtning från enheten till molnet.** IoT Hub kan på ett mycket tillförlitligt sätt ta emot flera miljoner händelser i sekunden från dina enheter. Tjänsten kan sedan bearbeta dem i den heta banan (”hot path”) med hjälp av en händelsebearbetningsmotor. Den kan också lagra dem i den kalla banan (”cold path”) för analys. IoT Hub behåller händelsedata i upp till sju dagar för att garantera en tillförlitlig bearbetning och för att kunna hantera belastningstoppar.

-   **Tillförlitlig meddelandehantering från molnet till enheten (eller *kommandon*). ** Serverdelen i lösningen kan använda IoT Hub för att skicka meddelanden med garanti om leverans minst en gång till enskilda enheter. Varje meddelande har en egen TTL-inställning (Time To Live) och serverdelen kan begära både leverans- och giltighetstidskvitton. Detta garanterar full insyn i livscykeln för ett meddelande från molnet till enheten. Du kan sedan implementera affärslogik med åtgärder som körs på enheter.

-   **Överför filer och cachelagrade sensordata till molnet.** Dina enheter kan överföra filer till Azure Storage med hjälp av SAS-URI:er som hanteras automatiskt av IoT Hub. IoT Hub kan generera aviseringar när filer tas emot i molnet så att serverdelen kan behandla dem.

## Gateways

En gateway i en IoT-lösning är vanligtvis antingen en [protokoll-gateway][lnk-gateway] som har distribuerats i molnet eller en [fält-gateway][Ink-field-gateway] som har distribuerats lokalt med dina enheter. En protokoll-gateway utför protokollöversättning, t.ex. MQTT till AMQP. En fält-gateway kan köra analyser i gränsnätverket, fatta tidskänsliga beslut som kan minska svarstiderna, tillhandahålla enhetshanteringstjänster, genomdriva säkerhets- och sekretessbegränsningar samt även utföra protokollöversättning. Båda typerna av gateways fungerar som en mellanhand mellan dina enheter och IoT Hub.

En fält-gateway skiljer sig från en enkel trafikroutningsenhet (till exempel en NAT-enhet eller en brandvägg) eftersom den vanligtvis har en aktiv roll i hanteringen av åtkomst- och informationsflödet i din lösning.

En lösning kan omfatta både protokoll- och fält-gateways.

## Hur fungerar IoT Hub?

Azure IoT Hub implementerar mönstret för [tjänstassisterad kommunikation][lnk-service-assisted-pattern] för att hantera samverkan mellan dina enheter och serverdelen i lösningen. Målet med tjänstassisterad kommunikation är att upprätta tillförlitliga, dubbelriktade kommunikationsvägar mellan ett kontrollsystem, t.ex IoT Hub, och enheter för särskilda ändamål som har distribuerats i ett icke betrott fysiskt utrymme. Mönstret tillämpar följande principer:

- Säkerhet prioriteras högre än alla andra funktioner.
- Enheter accepterar inte oönskad nätverksinformation. En enhet upprättar alla anslutningar och vägar som ”endast utgående”. För att en enhet ska ta emot ett kommando från backend-servern måste enheten regelbundet initiera en anslutning för att söka efter kommandon som väntar på att bearbetas.
- Enheter får endast ansluta till eller upprätta vägar till välkända tjänster som de är peerkopplade med, t.ex. IoT Hub.
- Kommunikationsvägen mellan enheten och tjänsten eller mellan enheten och gatewayen skyddas på programprotokollnivå.
- Auktoriseringen och autentiseringen på systemnivå baseras på enhetsspecifika identiteter. De gör att autentiseringsuppgifter och behörigheter för åtkomst kan återkallas nästan omedelbart.
- Dubbelriktad kommunikation för enheter som ansluter sporadiskt på grund av kapacitets- eller anslutningsbegränsningar förenklas genom att kommandon och enhetsmeddelanden hålls kvar tills en enhet ansluter för att ta emot dem. IoT Hub underhåller enhetsspecifika köer för de kommandon som tjänsten skickar.
- Data om programmets nyttolast skyddas separat för säker överföring via gateways till en viss tjänst.

Mobilbranschen har med framgång använt det tjänstassisterade kommunikationsmönstret i extremt hög skala för att implementera push-meddelandetjänster som [Windows Push Notification Services][lnk-wns], [Google Cloud Messaging][lnk-google-messaging] och [Apple Push Notification Service][lnk-apple-push].

## Nästa steg

Mer information om Azure IoT Hub finns på följande länkar:

* [Komma igång med IoT Hub][lnk-get-started]
* [Ansluta din enhet][lnk-connect-device]
* [Behandla meddelanden från enheten till molnet][lnk-d2c]

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-d2c]: iot-hub-csharp-csharp-process-d2c.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Service Assisted Communication, blogginlägg om tjänstassisterad kommunikation av Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[Ink-field-gateway]: iot-hub-guidance.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk



<!--HONumber=jun16_HO2-->


