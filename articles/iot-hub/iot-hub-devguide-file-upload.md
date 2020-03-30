---
title: Förstå Azure IoT Hub-filöverföring | Microsoft-dokument
description: Utvecklarguide – använd filöverföringsfunktionen i IoT Hub för att hantera överföring av filer från en enhet till en Azure storage blob-behållare.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: 35e10c0f9babca7719ff496e7068ad1564670fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209162"
---
# <a name="upload-files-with-iot-hub"></a>Ladda upp filer med IoT Hub

Som beskrivs i [IoT Hub-slutpunkter artikeln,](iot-hub-devguide-endpoints.md) en enhet kan starta en filuppladdning genom att skicka ett meddelande via en enhetsinriktad slutpunkt (**/devices/{deviceId}/files**). När en enhet meddelar IoT Hub att en överföring är klar skickar IoT Hub ett meddelande om filöverföring via **tjänstnamnet /messages/servicebound/filenotifications.**

I stället för att förmedla meddelanden via själva IoT Hub fungerar IoT Hub istället som avsändare till ett associerat Azure Storage-konto. En enhet begär en lagringstoken från IoT Hub som är specifik för filen som enheten vill överföra. Enheten använder SAS URI för att ladda upp filen till lagring, och när överföringen är klar skickar enheten ett meddelande om slutförande till IoT Hub. IoT Hub kontrollerar att filöverföringen är klar och lägger sedan till ett meddelande om filöverföring i slutpunkten för tjänstbehörande filmeddelanden.

Innan du laddar upp en fil till IoT Hub från en enhet måste du konfigurera hubben genom att [associera ett Azure Storage-konto](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub) till den.

Enheten kan sedan [initiera en uppladdning](iot-hub-devguide-file-upload.md#initialize-a-file-upload) och sedan [meddela IoT-hubben](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) när överföringen är klar. När en enhet meddelar IoT Hub att överföringen är klar kan tjänsten eventuellt generera ett [meddelande](iot-hub-devguide-file-upload.md#file-upload-notifications).

### <a name="when-to-use"></a>När du ska använda detta

Använd filöverföring för att skicka mediefiler och stora telemetribatchar som laddats upp av periodvis anslutna enheter eller komprimerade för att spara bandbredd.

Se [kommunikationsvägledning för enhet till moln](iot-hub-devguide-d2c-guidance.md) om du är osäker på hur du använder rapporterade egenskaper, meddelanden från enhet till moln eller filöverföring.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associera ett Azure Storage-konto med IoT Hub

Om du vill använda filöverföringsfunktionen måste du först länka ett Azure Storage-konto till IoT Hub. Du kan slutföra den här uppgiften antingen via Azure-portalen eller programmässigt via [IoT Hub-resursprovidern REST API:er](/rest/api/iothub/iothubresource). När du har associerat ett Azure Storage-konto med din IoT-hubb returnerar tjänsten en SAS URI till en enhet när enheten startar en begäran om filöverföring.

[Ladda upp filer från din enhet till molnet med IoT Hub-hjälpguider](iot-hub-csharp-csharp-file-upload.md) ger en fullständig genomgång av filöverföringsprocessen. De här hjälpguiderna visar hur du använder Azure-portalen för att associera ett lagringskonto med en IoT-hubb.

> [!NOTE]
> [Azure IoT-SDK:erna](iot-hub-devguide-sdks.md) hanterar automatiskt hämtning av SAS URI, överför filen och meddelar IoT Hub om en slutförd överföring.

## <a name="initialize-a-file-upload"></a>Initiera en filöverföring
IoT Hub har en slutpunkt specifikt för enheter att begära en SAS URI för lagring för att ladda upp en fil. För att starta filöverföringsprocessen skickar enheten `{iot hub}.azure-devices.net/devices/{deviceId}/files` en POST-begäran till med följande JSON-brödtext:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub returnerar följande data, som enheten använder för att ladda upp filen:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Inaktuella: initiera en filuppladdning med en GET

> [!NOTE]
> I det här avsnittet beskrivs inaktuella funktioner för hur du får en SAS URI från IoT Hub. Använd den POST-metod som beskrivits tidigare.

IoT Hub har två REST-slutpunkter för att stödja filöverföring, en för att få SAS URI för lagring och den andra för att meddela IoT-hubben för en slutförd överföring. Enheten startar filöverföringsprocessen genom att skicka en GET `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`till IoT-hubben på . IoT-hubben returnerar:

* En SAS URI som är specifik för filen som ska överföras.

* Ett korrelations-ID som ska användas när överföringen är klar.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Meddela IoT Hub om en slutförd filöverföring

Enheten överför filen till lagring med hjälp av Azure Storage SDK:er. När uppladdningen är klar skickar enheten `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` en POST-begäran till med följande JSON-brödtext:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Värdet `isSuccess` för är en boolesk som anger om filen har överförts. Statuskoden för `statusCode` är statusen för överföringen av filen `statusDescription` till lagring `statusCode`och motsvarar .

## <a name="reference-topics"></a>Referensämnen:

Följande referensavsnitt ger dig mer information om hur du överför filer från en enhet.

## <a name="file-upload-notifications"></a>Meddelanden om filuppladdning

När en enhet meddelar IoT Hub att en överföring är klar genererar IoT Hub ett meddelande. Det här meddelandet innehåller filens namn och lagringsplats.

Som förklaras i [Slutpunkter](iot-hub-devguide-endpoints.md)levererar IoT Hub filöverföringsmeddelanden via en tjänstinriktad slutpunkt (**/messages/servicebound/fileuploadnotifications**) som meddelanden. Mottagningssanföringen för filöverföringsmeddelanden är desamma som för meddelanden från molnet till enheten och har samma livscykel för [meddelanden](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle). Varje meddelande som hämtas från slutpunkten för filöverföringsmeddelandet är en JSON-post med följande egenskaper:

| Egenskap | Beskrivning |
| --- | --- |
| EnqueuedTimeUtc |Tidsstämpel som anger när meddelandet skapades. |
| DeviceId |**DeviceId** för enheten som laddade upp filen. |
| BlobUri (loburi) |URI för den uppladdade filen. |
| BlobName (BlobName) |Namnet på den uppladdade filen. |
| LastUpdatedTime |Tidsstämpel som anger när filen senast uppdaterades. |
| BlobSizeInBytes |Storleken på den uppladdade filen. |

**Exempel**. I det här exemplet visas brödtexten i ett meddelande om filöverföring.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Konfigurationsalternativ för meddelande om filöverföring

Varje IoT-hubb har följande konfigurationsalternativ för filöverföringsmeddelanden:

| Egenskap | Beskrivning | Intervall och standard |
| --- | --- | --- |
| **enableFileUploadNotifications** |Styr om filöverföringsmeddelanden skrivs till slutpunkten för filmeddelanden. |Bool. Standard: Sant. |
| **fileNotifications.ttlAsIso8601** |Standard-TTL för filöverföringsmeddelanden. |ISO_8601 intervall upp till 48H (minst 1 minut). Standard: 1 timme. |
| **fileNotifications.lockDuration** |Lås varaktighet för filöverföringsaviseringskön. |5 till 300 sekunder (minst 5 sekunder). Standard: 60 sekunder. |
| **fileNotifications.maxDeliveryCount** |Maximalt leveransantal för meddelandekön för filöverföring. |1 till 100. Standard: 100. |

Du kan ange dessa egenskaper på din IoT-hubb med Azure-portalen, Azure CLI eller PowerShell. Mer information finns i ämnena under [Konfigurera filöverföring](iot-hub-configure-file-upload.md).

## <a name="additional-reference-material"></a>Ytterligare referensmaterial

Andra referensavsnitt i utvecklarhandboken för IoT Hub är:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md) beskriver de olika IoT-hubbslutpunkterna för körnings- och hanteringsåtgärder.

* [Begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskriver de kvoter och begränsningsbeteenden som gäller för IoT Hub-tjänsten.

* [Azure IoT-enhet och tjänst SDK visar](iot-hub-devguide-sdks.md) de olika språk SDK:er som du kan använda när du utvecklar både enhets- och tjänstappar som interagerar med IoT Hub.

* [IoT Hub-frågespråket](iot-hub-devguide-query-language.md) beskriver det frågespråk som du kan använda för att hämta information från IoT Hub om dina enhetstvillingar och jobb.

* [IoT Hub MQTT-stöd](iot-hub-mqtt-support.md) ger mer information om IoT Hub-stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du laddar upp filer från enheter med IoT Hub, du kan vara intresserad av följande IoT Hub developer guide ämnen:

* [Hantera enhetsidentiteter i IoT Hub](iot-hub-devguide-identity-registry.md)

* [Styra åtkomst till IoT Hub](iot-hub-devguide-security.md)

* [Använda enhetstvillingar för att synkronisera tillstånd och konfigurationer](iot-hub-devguide-device-twins.md)

* [Anropa en direkt metod på en enhet](iot-hub-devguide-direct-methods.md)

* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

Information om hur du provar några av de begrepp som beskrivs i den här artikeln finns i följande IoT Hub-självstudiekurs:

* [Så här laddar du upp filer från enheter till molnet med IoT Hub](iot-hub-csharp-csharp-file-upload.md)