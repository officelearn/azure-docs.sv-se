---
title: Information för enhetsmetadata i lösningen för fjärrövervakning | Microsoft Docs
description: En beskrivning av den förkonfigurerade fjärrövervakningslösningen i Azure IoT och dess arkitektur.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 1b334769-103b-4eb0-a293-184f3d1ba9a3
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 4efea316c05f566add3e175bc5bb18842225ede3
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35758169"
---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Information för enhetsmetadata i den förkonfigurerade lösningen för fjärrövervakning

Azure IoT Suite förkonfigurerade fjärrövervakningslösningen visar en metod för att hantera enhetsmetadata. Den här artikeln beskriver tillvägagångssättet den här lösningen tar så att du kan förstå:

* Vilka enhetsmetadata lagras.
* Hur hanterar lösningen enhetsmetadata.

## <a name="context"></a>Kontext

Den förkonfigurerade lösningen använder [Azure IoT Hub] [ lnk-iot-hub] att aktivera dina enheter att skicka data till molnet. Information om enheter som lagras i tre olika platser:

| Plats | Information som lagras | Implementering |
| -------- | ------------------ | -------------- |
| Identitetsregistret | Enhets-id, autentiseringsnycklar, aktiverade tillstånd | Inbyggd i IoT Hub |
| Enhetstvillingar | Metadata: rapporterade egenskaper, önskade egenskaper, taggar | Inbyggd i IoT Hub |
| Cosmos DB | Kommandot och metoden historik | Anpassad för lösningen |

IoT-hubb innehåller ett [enhetsidentitetsregistret] [ lnk-identity-registry] att hantera åtkomst till en IoT-hubb och använder [enhetstvillingar] [ lnk-device-twin] att hantera enhetsmetadata. Det finns också en fjärransluten övervakning Lösningsspecifika *enhetsregistret* som lagrar kommandot och metoden historik. Lösningen för fjärrövervakning använder en [Cosmos DB] [ lnk-docdb] databasen för att implementera ett eget Arkiv för kommandot och metoden historik.

> [!NOTE]
> Den förkonfigurerade lösningen för fjärrövervakning behåller enhetsidentitetsregistret synkroniserade med informationen i Cosmos DB-databasen. Båda använder samma enhets-id för att unikt identifiera varje enhet som är ansluten till din IoT-hubb.

## <a name="device-metadata"></a>Enhetsmetadata

IoT Hub underhåller en [enhetstvillingen] [ lnk-device-twin] för de simulerade och fysiska enheter är anslutna till en lösning för fjärrövervakning. Lösningen använder enhetstvillingar för att hantera metadata som associeras med enheter. En enhetstvilling är ett JSON-dokument som underhålls av IoT Hub och lösningen använder IoT Hub-API för att interagera med enhetstvillingar.

Tre typer av metadata som lagras i enhetstvillingen:

- *Rapporterade egenskaper* skickas till en IoT-hubb av en enhet. I fjärrövervakningslösningen skickar simulerade enheterna skickar rapporterade egenskaper vid start och som svar på **ändra enhetstillstånd** kommandon och metoder. Du kan visa rapporterade egenskaper i den **enhetslistan** och **enhetsinformation** i lösningsportalen. Rapporterade egenskaper är skrivskyddade.
- *Önskade egenskaper* hämtas från IoT hub av enheter. Det är ansvar för enheten för att ge alla nödvändiga konfigurationsändringen på enheten. Det är också ansvar för enheten att rapportera ändringen till hubben som en rapporterad egenskap. Du kan ange ett önskat egenskapsvärde via lösningsportalen.
- *Taggar* finns bara i enhetstvillingen och aldrig ska synkroniseras med en enhet. Du kan ange taggvärden i lösningsportalen och använda dem när du filtrerar listan över enheter. Lösningen använder också en tagg för att identifiera ikonen som ska visas för en enhet i lösningsportalen.

Exempel rapporterade egenskaper från de simulerade enheterna omfattar tillverkare, modell, latitud och longitud. Simulerade enheter kan du också returnera listan med metoder som stöds som en rapporterad egenskap.

> [!NOTE]
> I koden för den simulerade enheten är det bara de önskade egenskaperna **Desired.Config.TemperatureMeanValue** och **Desired.Config.TelemetryInterval** som används till att uppdatera de rapporterade egenskaper som skickas tillbaka till IoT Hub. Alla andra förfrågningar om önskad egenskap ignoreras.

Enheten information metadata JSON-dokument lagras i registret Cosmos DB-databas för enheten har följande struktur:

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
> Enhetsinformation kan även innehålla metadata för att beskriva den telemetri som enheten skickar till IoT Hub. Lösningen för fjärrövervakning använder telemetri metadata för att anpassa hur instrumentpanelen visar [dynamisk telemetri][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Livscykel

När du skapar en enhet i lösningsportalen är skapar lösningen en post i Cosmos DB-databasen för att lagra kommandot och metoden historik. Lösningen skapar nu även en post för enheten i enhetsidentitetsregistret, vilket genererar de nycklar som enheten använder för att autentisera med IoT Hub. Det skapar också en enhetstvilling.

När en enhet först ansluter till lösningen skickar den rapporterade egenskaper och ett meddelande med enhetsinformation. Rapporterade egenskapsvärden sparas automatiskt i enhetstvillingen. De rapporterade egenskaperna omfattar enhetens tillverkare, modell, serienummer och en lista över metoder som stöds. Meddelandet med enhetsinformation som innehåller en lista över kommandon som enheten har stöd för, inklusive information om alla kommandoparametrar. När lösningen tar emot det här meddelandet, uppdaterar enhetsinformation i Cosmos DB-databasen.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Visa och redigera enhetsinformation i lösningsportalen

Enhetslistan i lösningsportalen visar följande enhetsegenskaper som kolumner som standard: **Status**, **DeviceId**, **tillverkare**, **modell Antal**, **serienummer**, **Firmware**, **plattform**, **Processor**, och  **Installerat RAM-minne**. Du kan anpassa kolumnerna genom att klicka på **Kolumnredigerare**. Enhetsegenskaperna **latitud** och **longitud** enhet plats i Bing-karta på instrumentpanelen.

![Kolumnredigeraren i listan över enheter][img-device-list]

I den **enhetsinformation** fönstret i lösningsportalen kan du redigera önskade egenskaper och taggar (rapporterade egenskaper är skrivskyddade).

![Informationspanelen][img-device-edit]

Du kan använda portalen för lösningen för att ta bort en enhet från din lösning. När du tar bort en enhet i lösningen tar du bort posten enheten från identitetsregistret och tar sedan bort enhetstvillingen. Lösningen tar också bort information relaterad till enheten från Cosmos DB-databasen. Innan du kan ta bort en enhet, måste du inaktivera den.

![Ta bort enhet][img-device-remove]

## <a name="device-information-message-processing"></a>Enheten information meddelandebehandling

Enhetsmeddelanden skickas av en enhet skiljer sig från telemetrimeddelanden. Enhetsinformationsmeddelanden omfattar de kommandon som en enhet kan svara på och eventuella kommandohistorik. IoT Hub själva har ingen information om de metadata som tillhör ett meddelande med enhetsinformation och bearbetar meddelandet på samma sätt som den bearbetar alla meddelanden för enhet-till-moln. I fjärrövervakningslösningen skickar en [Azure Stream Analytics] [ lnk-stream-analytics] jobb (ASA) läser meddelanden från IoT Hub. Den **DeviceInfo** stream analytics-jobbet filter för meddelanden som innehåller **”ObjectType”: ”DeviceInfo”** och vidarebefordrar dem till den **EventProcessorHost** värdinstans som körs i ett webbjobb. Logiken i den **EventProcessorHost** instans använder enhets-ID för att hitta Cosmos DB-posten för enheten och uppdatera posten.

> [!NOTE]
> Ett meddelande med enhetsinformation är ett standardmeddelande för enhet-till-moln. Lösningen skiljer mellan enhetsinformationsmeddelanden och telemetrimeddelanden med hjälp av ASA-frågor.

## <a name="next-steps"></a>Nästa steg

Nu är klar du lära dig hur du kan anpassa de förkonfigurerade lösningarna kan utforska du några av de andra funktionerna och funktionerna i de förkonfigurerade IoT Suite-lösningarna:

* [Översikt över förkonfigurerade lösning][lnk-predictive-overview]
* [Vanliga frågor och svar om IoT Suite][lnk-faq]
* [IoT-säkerhet från grunden][lnk-security-groundup]

<!-- Images and links -->
[img-device-list]: media/iot-suite-v1-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-v1-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-v1-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dynamic-telemetry]: iot-suite-v1-dynamic-telemetry.md

[lnk-predictive-overview]:../iot-accelerators/iot-accelerators-predictive-overview.md
[lnk-faq]: iot-suite-v1-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
