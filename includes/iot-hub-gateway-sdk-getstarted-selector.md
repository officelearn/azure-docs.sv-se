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

SDK innehåller ett abstraktionslager som gör att du kan skapa gatewayar som ska köras på en mängd olika operativsystem och plattformar.

![][2]

### Meddelanden

Tänk på att även om moduler som skickar meddelanden till varandra är ett bekvämt sätt att få en överblick över hur en gateway fungerar, så återspeglar det inte korrekt vad som händer. Moduler använder en meddelandebuss för att kommunicera med varandra, de publicerar meddelanden till bussen och bussen skickar meddelanden till alla moduler som är anslutna till bussen.

En modul använder funktionen **MessageBus_Publish** för att publicera ett meddelande med meddelandebussen. Meddelandebussen levererar meddelanden till en modul genom att aktivera en återanropsfunktion. Ett meddelande består av en uppsättning nyckel-/värdeegenskaper och innehåll som skickas som ett block med minne.

![][3]

Varje modul ansvarar för att filtrera meddelanden eftersom meddelandebussen använder en sändningsmekanism för att leverera meddelandena till alla moduler som är anslutna till den. En modul bör endast tillämpas på meddelanden som är avsedda för den. Meddelandefiltreringen skapar effektivt en meddelandepipeline. En modul filtrerar vanligtvis meddelanden som tas emot med meddelandeegenskaper för att identifiera meddelanden som den ska bearbeta.

## Arkitektur för Hello World-exempel

Hello World-exemplet illustrerar de begrepp som beskrivs i föregående avsnitt. Hello World-exemplet implementerar en gateway som har en pipeline som består av två moduler:

-   Modulen *hello world* skapar ett meddelande var femte sekund och skickar det till loggningsmodulen.
-   Modulen *loggning* skriver de meddelanden den tar emot till en fil.

![][4]

Enligt beskrivningen i föregående avsnitt, skickar modulen Hello World inte meddelanden direkt till loggningsmodueln var femte sekund. I stället publicerar den ett meddelande med meddelandebusen var femte sekund.

Loggningsmodulen tar emot meddelandet från meddelandebussen och kontrollerar dess egenskaper i ett filter. Om loggningsmodulen anger att den ska bearbeta meddelandet, skriver den innehållet i meddelandet till en fil.

Loggningsmodulen förbrukar endast meddelanden från meddelandebussen, den publicerar aldrig nya meddelanden till bussen.

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


<!--HONumber=sep16_HO1-->


