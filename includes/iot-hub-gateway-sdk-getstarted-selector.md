> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-get-started.md)

Den här artikeln innehåller en detaljerad genomgång av [Hello World-exempelkod][lnk-helloworld-exempel] för att illustrera grundläggande komponenter i arkitekturen för [Azure IoT Gateway SDK][lnk-gateway-sdk]. Exemplet använder Gateway-SDK för att skapa en enkel gateway som loggar ett meddelande med "hello world" i en fil var femte sekund.

Den här genomgången omfattar:

- **Koncept**: En översikt över de komponenter som ska utgöra en gateway som du skapar med Gateway-SDK.  
- **Hello World-exempelarkitektur**: Beskriver hur begrepp gäller för Hello World-exempel och hur komponenterna fungerar ihop.
- **Hur du skapar exemplet**: De steg som krävs för att skapa exemplet.
- **Hur du kör exemplet**: De steg som krävs för att köra exemplet. 
- **Vanliga utdata**: Ett exempel på utdata du kan förvänta dig när du kör exemplet.
- **Kodfragment**: En samling av kodfragment för att visa hur Hello World-exemplet implementerar nyckelkomponenter för gatewayen.

## Gateway SDK-begrepp

Innan du undersöker exempelkoden eller skapar en egen fält-gateway med Gateway-SDK, bör du förstå viktiga begrepp som ligger till grund för SDK-arkitektur.

### Moduler

Du skapar en gateway med Azure IoT Gateway SDK genom att skapa och montera *moduler*. Moduler använder *meddelanden* för att utbyta data med varandra. En modul får ett meddelande, utför en åtgärd på det, omvandlar det eventuellt till ett nytt meddelande och publicerar det sedan för andra moduler att bearbeta. Vissa moduler kan bara generera nya meddelanden och bearbetar aldrig inkommande meddelanden. En kedja av moduler skapar en databearbetningspipeline med varje modul som utför en omvandling av data vid en punkt i denna pipeline.

![][1]
 
SDK innehåller följande:

- Färdiga moduler som utför vanliga gateway-funktioner.
- Gränssnitt som utvecklare kan använda för att skriva anpassade moduler.
- Den infrastruktur som krävs för att distribuera och köra en uppsättning moduler.

SDK innehåller ett abstraktionslager som gör att du kan skapa gateways som ska köras på en mängd olika operativsystem och plattformar.

![][2]

### Meddelanden

Tänk på att även om moduler som skickar meddelanden till varandra är ett bekvämt sätt att få en överblick över hur en gateway fungerar, så återspeglar det inte korrekt vad som händer. Modulerna använder en asynkron meddelandekö för att kommunicera med varandra. De publicerar meddelanden till meddelandekön (bus, pubsub eller andra meddelandemönster) och låter sedan meddelandekön dirigera meddelandet till de moduler som är anslutna till den.

Modulerna använder funktionen **Broker_Publish** för att publicera ett meddelande till meddelandekön. Den asynkrona meddelandekön levererar meddelanden till en modul genom att anropa en återanropsfunktion. Ett meddelande består av en uppsättning nyckel-/värdeegenskaper och innehåll som skickas som ett block med minne.

![][3]

### Meddelandedirigering och filtrering

Det finns två sätt att dirigera meddelanden till rätt moduler. En uppsättning länkar kan skickas till den asynkrona meddelandekön så att meddelandekön känner till källan och mottagaren för varje modul, eller så kan modulen filtrera på meddelandets egenskaper. En modul ska bara agera på ett meddelande om meddelandet är avsett för modulen. Länkarna och meddelandefiltreringen skapar en effektiv meddelandepipeline.

## Arkitektur för Hello World-exempel

Hello World-exemplet illustrerar de begrepp som beskrivs i föregående avsnitt. Hello World-exemplet implementerar en gateway som har en pipeline som består av två moduler:

-   Modulen *hello world* skapar ett meddelande var femte sekund och skickar det till loggningsmodulen.
-   Modulen *loggning* skriver de meddelanden den tar emot till en fil.

![][4]

Enligt beskrivningen i föregående avsnitt, skickar modulen Hello World inte meddelanden direkt till loggningsmodueln var femte sekund. I stället publicerar den ett meddelande till den asynkrona meddelandekön var femte sekund.

Loggningsmodulen får meddelandet från den asynkrona meddelandekön och agerar på meddelandet genom att skriva meddelandets innehåll till en fil.

Loggningsmodulen förbrukar endast meddelanden från den asynkrona meddelandekön, den publicerar aldrig nya meddelanden till den asynkrona meddelandekön.

![][5]

Bilden ovan illustrerar arkitekturen i Hello World-exemplet och de relativa sökvägarna till källfilerna som implementerar olika delar av exemplet på [lagringsplatsen][lnk-gateway-sdk]. Utforska koden på egen hand eller använd kodfragmenten nedan som vägledning.

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-getstarted-selector/modules.png
[2]: media/iot-hub-gateway-sdk-getstarted-selector/modules_2.png
[3]: media/iot-hub-gateway-sdk-getstarted-selector/messages_1.png
[4]: media/iot-hub-gateway-sdk-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-gateway-sdk-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-exempel]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/hello_world
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

<!--HONumber=Sep16_HO4-->


