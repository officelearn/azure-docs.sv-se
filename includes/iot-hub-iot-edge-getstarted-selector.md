> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-get-started.md)
> 
> 

Den här artikeln innehåller en detaljerad genomgång av [Hello World-exempelkoden][lnk-helloworld-sample] och illustrerar grundläggande komponenter i [Azure IoT Edge][lnk-iot-edge]-arkitekturen. I exemplet används Azure IoT Edge för att skapa en enkel gateway som loggar ett meddelande med ”hello world” i en fil var femte sekund.

Den här genomgången omfattar:

* **Hello World exempelarkitektur**: Beskriver hur [Azure IoT kant arkitektur begrepp] [ lnk-edge-concepts] gäller Hello World-exempel och hur komponenterna fungerar ihop.
* **Hur du skapar exemplet**: De steg som krävs för att skapa exemplet.
* **Hur du kör exemplet**: De steg som krävs för att köra exemplet. 
* **Vanliga utdata**: Ett exempel på utdata du kan förvänta dig när du kör exemplet.
* **Kodfragment**: en samling med kodstycken att visa hur Hello World-exempel implementerar gateway nyckelkomponenterna IoT kant.


## <a name="hello-world-sample-architecture"></a>Arkitektur för Hello World-exempel
Hello World-exemplet illustrerar de begrepp som beskrivs i föregående avsnitt. Hello World-exempel implementerar en IoT-gräns-gatewayen som har en pipeline som består av två IoT kant moduler:

* Modulen *hello world* skapar ett meddelande var femte sekund och skickar det till loggningsmodulen.
* Modulen *loggning* skriver de meddelanden den tar emot till en fil.

![Arkitekturen i Hello World-exemplet som skapats med Azure IoT Edge][4]

Enligt beskrivningen i föregående avsnitt, skickar modulen Hello World inte meddelanden direkt till loggningsmodueln var femte sekund. I stället publicerar den ett meddelande till den asynkrona meddelandekön var femte sekund.

Loggningsmodulen får meddelandet från den asynkrona meddelandekön och agerar på meddelandet genom att skriva meddelandets innehåll till en fil.

Loggningsmodulen förbrukar endast meddelanden från den asynkrona meddelandekön, den publicerar aldrig nya meddelanden till den asynkrona meddelandekön.

![Hur agenten dirigerar meddelanden mellan moduler i Azure IoT Edge][5]

Föregående bild visar Hello World-exempel och de relativa sökvägarna till källfilerna som implementerar olika delar av provet i av arkitekturen för den [databasen][lnk-iot-edge]. Utforska koden på egen hand eller Använd kodavsnitten i den här artikeln som vägledning.

<!-- Images -->
[4]: media/iot-hub-iot-edge-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-iot-edge-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/iot-edge/tree/master/samples/hello_world
[lnk-iot-edge]: https://github.com/Azure/iot-edge
[lnk-edge-concepts]: ../articles/iot-hub/iot-hub-iot-edge-overview.md