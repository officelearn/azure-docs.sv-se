> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-simulated-device.md)
> 
> 

Den här genomgången av [exemplet på molnöverföring av simulerad enhet] visar hur du kan använda [Azure IoT Gateway SDK][lnk-sdk] för att skicka enhet-till-moln-telemetri till IoT Hub från simulerade enheter.

Den här genomgången omfattar:

1. **Arkitektur**: viktig arkitekturinformation om exemplet på molnöverföring av simulerad enhet.
2. **Skapa och kör**: stegen som krävs för att bygga och köra exemplet.

## <a name="architecture"></a>Arkitektur
Exemplet på molnöverföring av simulerad enhet visar hur du kan använda SDK för att skapa en gateway som skickar telemetri från simulerade enheter till en IoT Hub. De simulerade enheterna kan inte anslutas direkt till IoT Hub eftersom:

* Enheterna inte använder kommunikationsprotokoll som kan tolkas av IoT Hub.
* Enheterna inte är tillräckligt smarta för att komma ihåg identiteten som tilldelats dem av IoT Hub.

Gatewayen löser dessa problem för de simulerade enheterna på följande sätt:

* Gatewayen kan tolka protokollet som används av de simulerade enheterna, tar emot enhet-till-moln-telemetri från enheterna och vidarebefordra meddelandena till IoT Hub med ett protokoll som kan tolkas av IoT Hub.
* Gatewayen lagrar IoT Hub-identiteter åt de simulerade enheterna och fungerar som en proxy när de simulerade enheterna skickar meddelanden till IoT Hub.

Följande diagram visar huvudkomponenterna för exemplet, inklusive gatewaymodulerna:

![][1]

> [!NOTE]
> Moduler skickar inte meddelanden direkt till varandra. Modulerna publicerar meddelanden till en asynkron meddelandekö som levererar meddelandena till de andra modulerna med en prenumerationsmekanism som du ser i diagrammet nedan. Mer information finns i [Get started with the IoT Gateway SDK][lnk-gw-getstarted] (Komma igång med the IoT Gateway SDK).
> 
> 

### <a name="protocol-ingestion-module"></a>Modul för protokollinhämtning
Den här modulen är startpunkten för att hämta data från enheter, via gatewayen, och till molnet. I exemplet utför modulen fyra åtgärder:

1. Den skapar simulerade temperaturdata. Observera att om du använder verkliga enheter läser modulen data från de fysiska enheterna.
2. Den placerar simulerade temperaturdata i innehållet i ett meddelande.
3. Den lägger till en egenskap med en falsk MAC-adress till meddelandet som innehåller de simulerade temperaturdata.
4. Det gör meddelandet tillgängligt för nästa modul i kedjan.

> [!NOTE]
> Modulen som heter **Protocol X ingestion** (Protokoll X-inhämtning) i diagrammet ovan kallas **Simulated device** (Simulerad enhet) i källkoden.
> 
> 

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt;-&gt; IoT Hub ID-modul
Modulen söker efter meddelanden som innehåller en egenskap som innehåller MAC-adressen, tillagd av modulen för protokollinhämtning, för den simulerade enhetsappen. Om modulen hittar en sådan egenskap lägger den till ytterligare en egenskap med en IoT Hub-enhetsnyckel till meddelandet och gör sedan meddelandet tillgängligt för nästa modul i kedjan. Det är så exemplet kopplar IoT Hub-enhetsidentiteter till simulerade enheter. Utvecklaren konfigurerar mappningen mellan MAC-adresser och IoT Hub-identiteter manuellt som en del av modulkonfigurationen. 

> [!NOTE]
> I det här exemplet används en MAC-adress som en unik enhetsidentifierare och kopplar den till en IoT Hub-enhetsidentitet. Du kan emellertid skriva din egen modul som använder en annan unik identifierare. Du kan exempelvis ha enheter med unika serienummer eller telemetridata som har ett unikt enhetsnamn inbäddat i sig, så du kan använda dem för att fastställa IoT Hub-enhetsidentiteten.
> 
> 

### <a name="iot-hub-communication-module"></a>IoT Hub-kommunikationsmodul
Den här modulen tar meddelanden med en IoT Hub-enhetsidentitet som tilldelats av den föregående modulen och skickar meddelandets innehåll till IoT Hub med HTTP. HTTP är ett av de tre protokollen som kan tolkas av IoT Hub.

Istället för att starta en anslutning till IoT Hub för varje simulerad enhetsapp öppnar den här modulen en enda HTTP-anslutning från gatewayen till IoT-hubben och flerfaldigar anslutningar från alla simulerade enheter via den anslutningen. Det gör att en enda gateway kan ansluta många fler enheter, simulerade eller andra, än vad som skulle vara möjligt om en unik anslutning öppnades för varje enhet.

![][2]

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-simulated-selector/image1.png
[2]: media/iot-hub-gateway-sdk-simulated-selector/image2.png

<!-- Links -->
[exemplet på molnöverföring av simulerad enhet]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/simulated_device_cloud_upload/README.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md

<!--HONumber=Feb17_HO1-->


