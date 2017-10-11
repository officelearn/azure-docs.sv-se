---
title: "Enhetens information metadata i fjärranslutna övervakningslösning | Microsoft Docs"
description: "En beskrivning av den förkonfigurerade fjärrövervakningslösningen i Azure IoT och dess arkitektur."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1b334769-103b-4eb0-a293-184f3d1ba9a3
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: f8fd452806a0a0b98cf8e434c9bd55700083a6c5
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Enhetens information metadata i fjärråtkomst övervakning förkonfigurerade lösningen

Azure IoT Suite remote förkonfigurerade övervakningslösning visar en metod för att hantera enhetsmetadata. Den här artikeln beskrivs en metod som den här lösningen använder att du ska förstå:

* Vilka enhetens metadata lagras för lösningen.
* Hur hanterar lösningen enhetens metadata.

## <a name="context"></a>Kontext

Fjärråtkomst övervakning förkonfigurerade lösningen använder [Azure IoT Hub] [ lnk-iot-hub] att enheterna att skicka data till molnet. Lösningen lagrar information om enheter på tre olika platser:

| Plats | Information som lagras | Implementering |
| -------- | ------------------ | -------------- |
| Identitetsregistret | Enhets-id, autentiseringsnycklar, aktiverade tillstånd | Inbyggd i IoT-hubb |
| Enheten twins | Metadata: rapporterade egenskaper, önskade egenskaper, taggar | Inbyggd i IoT-hubb |
| Cosmos DB | Kommandot och metoden historik | Anpassad för lösning |

IoT-hubb innehåller en [enhetsidentitetsregistret] [ lnk-identity-registry] att hantera åtkomst till en IoT-hubb och använder [enhet twins] [ lnk-device-twin] att hantera enhetsmetadata. Det finns också en fjärransluten övervakning Lösningsspecifika *enhetsregistret* som lagrar kommandot och metoden historik. Fjärråtkomst övervakning lösningen använder en [Cosmos DB] [ lnk-docdb] databasen för att implementera ett eget Arkiv för kommandot och metoden historik.

> [!NOTE]
> Fjärråtkomst övervakning förkonfigurerade lösningen behåller enhetsidentitetsregistret synkroniserad med informationen i Cosmos-DB-databas. Både kan använda samma enhets-id för att unikt identifiera varje enhet som är ansluten till din IoT-hubb.

## <a name="device-metadata"></a>Enhetens metadata

IoT-hubb upprätthåller en [enheten dubbla] [ lnk-device-twin] för varje simulerade och fysisk enhet som är ansluten till en fjärransluten övervakningslösning. Lösningen använder enheten twins för att hantera metadata som associeras med enheter. Lösningen använder API: et för IoT-hubb kan interagera med enheten twins dubbla en enhet är en JSON-dokumentet som underhålls av IoT-hubb.

En enhet dubbla lagrar tre typer av metadata:

- *Rapporterade egenskaper* skickas till en IoT-hubb av en enhet. I den fjärranslutna övervakningslösning simulerade enheterna skickar rapporterade egenskaper vid start och som svar på **ändra enhetsstatus** kommandon och metoder. Du kan visa rapporterade egenskaper i den **enhetslistan** och **enhetsinformation** i lösningen portal. Rapporterat egenskaper är skrivskyddade.
- *Egenskaper för Desired* hämtas från IoT-hubben av enheter. Ansvarar för enheten för att ge alla nödvändiga konfigurationsändringen på enheten. Det är också ansvar för enheten att rapportera ändringen till hubben som rapporterades egenskap. Du kan ange en önskad egenskapsvärdet via portalen lösning.
- *Taggar* finns bara i enheten dubbla och aldrig synkroniseras med en enhet. Du kan ange värden i lösningen portal och använda dem när du filtrerar listan över enheter. Lösningen använder också en tagg för att identifiera ikonen som ska visas för en enhet i lösningen portal.

Exempel rapporterade egenskaper från de simulerade enheterna innehåller tillverkare, modellnummer, latitud och longitud. Simulerade enheter returnerar listan över metoder som stöds som en rapporterade egenskap.

> [!NOTE]
> I koden för den simulerade enheten är det bara de önskade egenskaperna **Desired.Config.TemperatureMeanValue** och **Desired.Config.TelemetryInterval** som används till att uppdatera de rapporterade egenskaper som skickas tillbaka till IoT Hub. Alla andra ändringsbegäranden för önskad egenskapen ignoreras.

En enhet information metadata JSON-dokumentet lagras i enheten registret Cosmos-DB-databasen har följande struktur:

```json
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

> [!NOTE]
> Information om en enhet kan också innehålla metadata för att beskriva telemetri enheten skickar till IoT-hubb. Fjärråtkomst övervakningslösning använder telemetri metadata för att anpassa hur instrumentpanelen visar [dynamiska telemetri][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Livscykel

När du först skapa en enhet i lösningen portal skapas en post i Cosmos-DB-databas för lagring av kommandot och metoden historik i lösningen. Lösningen skapas nu också en post för enheten i enhetsidentitetsregistret, vilket genererar nycklar som enheten använder för att autentisera med IoT-hubben. Dessutom skapas en delad enhet.

När en enhet först ansluter till lösningen skickar rapporterade egenskaper och informationsmeddelande för enheten. Rapporterat egenskapsvärden sparas automatiskt i dubbla för enheten. Rapporterat egenskaper innehåller enhetens tillverkare, modellnummer, serienummer och en lista över metoder som stöds. Enheten information meddelandet innehåller listan med kommandon som enheten stöder inklusive information om några parametrar. När lösningen får det här meddelandet, uppdaterar enhetsinformation i Cosmos-DB-databasen.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Visa och redigera enhetsinformation i lösningen portal

Listan över enheter i lösningen portal visas följande egenskaper för enheter som kolumner som standard: **Status**, **DeviceId**, **tillverkare**, **modellnummer**, **serienummer**, **Firmware**, **plattform**, **Processor**, och **installerade RAM**. Du kan anpassa kolumnerna genom att klicka på **kolumnen editor**. Egenskaper för enhet **latitud** och **longitud** enhet på plats i Bing Map på instrumentpanelen.

![Kolumnen redigeraren i listan över enheter][img-device-list]

I den **enhetsinformation** rutan i portalen lösning kan du redigera egenskaper och taggar (rapporterat egenskaper är skrivskyddade).

![Informationspenelen][img-device-edit]

Du kan använda portalen lösning för att ta bort en enhet från din lösning. När du tar bort en enhet lösningen enhet-posten tas bort från identitetsregistret och sedan tar bort enheten dubbla. Lösningen tar också bort information som rör enheten från Cosmos-DB-databasen. Innan du tar bort en enhet, måste du inaktivera den.

![Ta bort enheten][img-device-remove]

## <a name="device-information-message-processing"></a>Enheten information meddelandebehandling

Enheten informationsmeddelanden som skickats av en enhet skiljer sig från telemetri meddelanden. Enheten informationsmeddelanden innehåller de kommandon som en enhet kan svara på och eventuella tidigare kommandon. IoT-hubb själva känner inte till metadata i ett meddelande för enheten information och bearbetar meddelandet på samma sätt som den bearbetar alla meddelanden enhet till moln. I den fjärranslutna övervakningslösning en [Azure Stream Analytics] [ lnk-stream-analytics] (ASA) jobbet läser meddelanden från IoT-hubb. Den **DeviceInfo** stream analytics-jobbet filter för meddelanden som innehåller **”ObjectType”: ”DeviceInfo”** och vidarebefordrar dem till den **EventProcessorHost** värdinstans som körs i ett webbprogram. Logiken i den **EventProcessorHost** instans använder enhets-id för att hitta Cosmos-DB-post för enheten och uppdatera posten.

> [!NOTE]
> En enhet information meddelandet är en standard enhet till moln. Lösningen skiljer enheten informationsmeddelanden och telemetri meddelanden med hjälp av ASA frågor.

## <a name="next-steps"></a>Nästa steg

Du kan utforska några av de andra funktionerna och funktioner i IoT Suite förkonfigurerade lösningar nu du är klar med att lära dig hur du kan anpassa förkonfigurerade lösningar:

* [Förutsägande Underhåll förkonfigurerade lösning: översikt][lnk-predictive-overview]
* [Vanliga frågor och svar om IoT Suite][lnk-faq]
* [IoT-säkerhet från grunden][lnk-security-groundup]

<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
