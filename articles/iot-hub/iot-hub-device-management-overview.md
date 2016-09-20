<properties
 pageTitle="Översikt över enhetshantering | Microsoft Azure"
 description="Översikt över enhetshantering i Azure IoT Hub: enhetstvillingar, enhetsfrågor, enhetsjobb"
 services="iot-hub"
 documentationCenter=""
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# Översikt över enhetshantering i Azure IoT Hub (förhandsversion)

Enhetshanteringen i Azure IoT Hub tillhandahåller standardbaserad IoT-enhetshantering som hjälper dig att hantera, konfigurera och uppdatera dina enheter via en fjärranslutning.

Det finns tre viktiga begrepp relaterade till enhetshanteringen i Azure IoT:

1.  **Enhetstvilling**: En representation av den fysiska enheten i IoT Hub.

2.  **Enhetsfrågor**: Används för att hitta enhetstvillingar och för att få en bild över flera enhetstvillingar. Du kan till exempel köra en fråga för att hitta alla enhetstvillingar med version 1.0 av den inbyggda programvaran.

3.  **Enhetsjobb**: En åtgärd som utförs på en eller flera fysiska enheter, till exempel uppdatering av den inbyggda programvaran, omstart och fabriksåterställning.

## Enhetstvilling

Enhetstvillingen är en representation av en fysisk enhet i Azure IoT. Objektet **Microsoft.Azure.Devices.Device** används för att representera enhetstvillingen.

![][img-twin]

Enhetstvillingen har följande komponenter:

1.  **Enhetsfält:** Enhetsfält är fördefinierade egenskaper som används för både meddelande- och enhetshantering i IoT Hub. Dessa hjälper IoT Hub att identifiera och ansluta med fysiska enheter. Enhetsfält synkroniseras inte till enheten och lagras endast i enhetstvillingen. Enhetsfält innehåller enhets-ID:t och autentiseringsinformation.

2.  **Enhetsegenskaper:** Enhetsegenskaper är en fördefinierad ordlista med egenskaper som beskriver den fysiska enheten. Den fysiska enheten är huvudobjektet för varje enhetsegenskap och är den auktoritära lagringsplatsen för alla tillhörande värden. En konsekvent representation av dessa egenskaper lagras slutligen i enhetstvillingen i molnet. Konsekvens och uppdateringar hanteras med synkroniseringsinställningar, som beskrivs i [Självstudiekurs: Använda enhetstvillingen][lnk-tutorial-twin]. Några exempel på enhetsegenskaper är versionen av den inbyggda programvaran, batterinivå och tillverkarens namn.

3.  **Tjänstegenskaper:** Tjänstegenskaper är **&lt;nyckel/värde-par&gt;** som utvecklaren lägger till i tjänstens egenskapsordlista. Dessa egenskaper utökar datamodellen för enhetstvillingen, så att du bättre kan beskriva enheten. Tjänstegenskaperna synkroniseras inte till enheten och lagras endast i enhetstvillingen i molnet. Ett exempel på en tjänstegenskap är **&lt;NextServiceDate, 11/12/2017&gt;**, som kan användas för att hitta enheter baserat på deras nästa underhållsdatum.

4.  **Taggar:** Taggar är en deluppsättning av tjänstegenskaperna som är godtyckliga strängar i stället för ordlisteegenskaper. De kan användas för att kommentera enhetstvillingar eller för att ordna enheter i grupper. Taggar synkroniseras inte till enheten och lagras endast i enhetstvillingen. Om din enhetstvilling till exempel representerar en fysisk lastbil kan du lägga till en tagg för varje typ av last som lastbilen transporterar – **äpplen**, **apelsiner** och **bananer**.

## Enhetsfrågor

I det förra avsnittet lärde du dig om enhetstvillingens olika komponenter. Nu ska vi förklara hur du hittar enhetstvillingar i IoT Hub-enhetsregistret baserat på enhetsegenskaper, tjänstegenskaper eller taggar. Ett exempel på när du kan använda en fråga är för att hitta enheter som behöver uppdateras. Du kan fråga efter alla enheter med en angiven version av den inbyggda programvaran och skicka resultatet till en specifik åtgärd (i IoT Hub kallat ett enhetsjobb, vilket förklaras i nästa avsnitt).

Du kan skicka frågor med hjälp av taggar och egenskaper:

-   Om du vill fråga efter enhetstvillingar med taggar skickar du en matris med strängar så returnerar frågan alla enheter som är taggade med alla dessa strängar.

-   Om du vill fråga efter enhetstvillingar med hjälp av tjänstegenskaper eller enhetsegenskaper kan du använda ett JSON-frågeuttryck. Exemplet nedan visar hur du kan fråga efter alla enheter med enhetsegenskapen med nyckeln **FirmwareVersion** och värdet **1.0**. Du kan se att **type** för egenskapen är **device**, vilket innebär att vi frågar baserat på enhetsegenskaper, inte tjänstegenskaper:

  ```
  {                           
      "filter": {                  
        "property": {                
          "name": "FirmwareVersion",   
          "type": "device"             
        },                           
        "value": "1.0",              
        "comparisonOperator": "eq",  
        "type": "comparison"         
      },                           
      "project": null,             
      "aggregate": null,           
      "sort": null                 
  }
  ```

## Enhetsjobb

Nästa begrepp inom enhetshantering är enhetsjobb, som gör det möjligt att samordna flera hanteringssteg på flera enheter.

För närvarande kan du välja mellan sex typer av enhetsjobb när du hanterar enheter i Azure IoT Hub (vi lägger till fler jobb allt eftersom våra kunder begär dem):

- **Uppdatering av inbyggd programvara**: Uppdaterar den inbyggda programvaran (eller operativsystemavbildningen) på den fysiska enheten.
- **Starta om**: Startar om den fysiska enheten.
- **Fabriksåterställning**: Återställer den inbyggda programvaran (eller operativsystemavbildningen) för den fysiska enheten till en säkerhetskopia med fabriksinställningarna som lagras på enheten.
- **Konfigurationsuppdatering**: Konfigurerar IoT Hub-klientagenten som körs på den fysiska enheten.
- **Läs enhetsegenskap**: Hämtar det senaste värdet för en enhetsegenskap på den fysiska enheten.
- **Skriv enhetsegenskap:** Ändrar en enhetsegenskap på den fysiska enheten.

Mer information om hur du använder respektive jobb finns i [API-dokumentationen för C\# och node.js][lnk-apidocs].

Ett jobb kan köras på flera enheter. När du startar ett jobb skapas ett tillhörande underordnat jobb för var och en av dessa enheter. Ett underordnat jobb körs på en enskild enhet. Varje underordnat jobb har en pekare till dess överordnade jobb. Det överordnade jobbet är bara en behållare för det underordnade jobbet. Det implementerar inte någon logik för att skilja mellan olika typer av enheter (t.ex uppdateringen av en Intel Edison till skillnad mot uppdateringen av en Raspberry Pi). Följande diagram illustrerar förhållandet mellan ett överordnat jobb, dess underordnade jobb och den associerade fysiska enheten.

![][img-jobs]

Du kan skicka frågor mot jobbhistoriken om du vill se statusen för jobb som du har startat. Du hittar några exempelfrågor i [vårt frågebibliotek][lnk-query-samples].

## Enhetsimplementering

Nu när vi har gått igenom begreppen på tjänstsidan ska vi gå vidare och se hur du skapar en hanterad fysisk enhet. Azure IoT Hub DM-klientbiblioteket hjälper dig att hantera dina IoT-enheter med Azure IoT Hub. ”Hanteringen” inbegriper åtgärder som omstart, fabriksåterställning och uppdatering av den inbyggda programvaran.  I nuläge tillhandahåller vi ett plattformsoberoende C-bibliotek, men stöd för andra språk kommer snart.  

DM-klientbiblioteket har två huvudsakliga uppgifter inom enhetshantering:

- Synkronisera egenskaper på den fysiska enheten med dess enhetstvilling i IoT Hub.
- Samordna enhetsjobb som skickas av IoT Hub till enheten.

Mer information om dessa uppgifter och om implementeringen på den fysiska enheten finns i [Introduktion till klientbiblioteket för C för enhetshantering i Azure IoT Hub][lnk-library-c].

## Nästa steg

Om du vill implementera klientprogram på många olika enhetsspecifika maskinvaruplattformar och operativsystem kan du använda enhets-SDK:er för IoT. Enhets-SDK:erna för IoT innehåller bibliotek som gör det lätt att skicka telemetri till en IoT-hubb och att ta emot kommandon från molnet till enheten. När du använder SDK:erna kan du välja mellan ett antal olika nätverksprotokoll för att kommunicera med IoT Hub. Mer information finns i [Information om enhets-SDK:er][lnk-device-sdks].

Om du vill lära dig mer om enhetshanteringsfunktionerna i Azure IoT Hub går du självstudiekursen [Komma igång med enhetshantering i Azure IoT Hub][lnk-get-started].

<!-- Images and links -->
[img-twin]: media/iot-hub-device-management-overview/image1.png
[img-jobs]: media/iot-hub-device-management-overview/image2.png
[img-client]: media/iot-hub-device-management-overview/image3.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-library-c]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-apidocs]: http://azure.github.io/azure-iot-sdks/
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks



<!--HONumber=sep16_HO1-->


