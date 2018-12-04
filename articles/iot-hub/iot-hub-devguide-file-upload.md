---
title: Förstå Azure IoT Hub filöverföringen | Microsoft Docs
description: Utvecklarguide – Använd filuppladdningsfunktionen mellan IoT Hub och hantera ladda upp filer från en enhet till en Azure storage blob-behållare.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: dobett
ms.openlocfilehash: 724f655229347aa73747476e6bb4837b30e0fc4f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839015"
---
# <a name="upload-files-with-iot-hub"></a>Ladda upp filer med IoT Hub

Enligt beskrivningen i den [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md) artikel, en enhet kan starta en uppladdning genom att skicka ett meddelande via en enhet-riktade slutpunkt (**/devices/ {deviceId} / filer**). När en enhet meddelas IoT Hub att ladda upp är klar, IoT Hub skickar ett filuppladdningsmeddelande för överföring via den **/messages/servicebound/filenotifications** service-slutpunkter.

I stället för förtroendeförmedling meddelanden via IoT Hub själva fungerar IoT Hub i stället som en dispatcher till ett associerat Azure Storage-konto. En enhet begär en token för storage från IoT Hub som är specifik för enheten vill ladda upp filen. Enheten använder SAS-URI för att överföra filen till lagring och när överföringen är klar enheten skickar ett meddelande om slutförande till IoT Hub. IoT Hub kontrollerar filöverföringen är klar och lägger sedan till ett filuppladdningsmeddelande för uppladdning till aviseringsslutpunkten tjänst för webbservergrupper på filen.

Innan du överför en fil till IoT Hub från en enhet, måste du konfigurera din hubb genom [associera ett Azure Storage](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub) kontot till den.

Enheten kan sedan [initiera ladda upp](iot-hub-devguide-file-upload.md#initialize-a-file-upload) och sedan [meddela IoT-hubb](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) när överföringen är klar. Du kan också när en enhet meddelas IoT Hub att överföringen är klar, tjänsten kan generera en [meddelande](iot-hub-devguide-file-upload.md#file-upload-notifications).

### <a name="when-to-use"></a>När du ska använda detta

Använda ladda upp filer för att skicka mediefiler och stora telemetri batchar har laddats upp av periodvis anslutna enheter eller komprimeras för att spara bandbredd.

Referera till [enhet till molnet kommunikation vägledning](iot-hub-devguide-d2c-guidance.md) om tveksam mellan med hjälp av rapporterade egenskaper, meddelanden från enheten till molnet eller ladda upp filen.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associera ett Azure Storage-konto med IoT Hub

Om du vill använda filuppladdningen inom, måste du först koppla ett Azure Storage-konto till IoT Hub. Du kan göra detta med antingen via Azure portal eller programmässigt via den [resursprovidern i IoT Hub REST API: er](/rest/api/iothub/iothubresource). När du har associerat ett Azure Storage-konto med din IoT-hubb, returnerar tjänsten en SAS-URI till en enhet när enheten startar en begäran för uppladdning av filen.

Den [ladda upp filer från din enhet till molnet med IoT Hub](iot-hub-csharp-csharp-file-upload.md) instruktionsguider ger en fullständig genomgång för filöverföring. Dessa instruktionsguider visar hur du använder Azure-portalen för att associera ett lagringskonto med en IoT-hubb.

> [!NOTE]
> Den [Azure IoT SDK: er](iot-hub-devguide-sdks.md) automatiskt hanterar hämtning av SAS-URI, överföra filen och meddela en överförda IoT-hubb.

## <a name="initialize-a-file-upload"></a>Initiera en uppladdning
IoT Hub har en slutpunkt specifikt för enheter att begära en SAS-URI för lagring att överföra en fil. Om du vill starta filöverföring enheten skickar en POST-begäran till `{iot hub}.azure-devices.net/devices/{deviceId}/files` med följande JSON-texten:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub returnerar följande uppgifter, som enheten använder för att ladda upp filen:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Föråldrad: initiera en filöverföring med GET

> [!NOTE]
> Det här avsnittet beskrivs föråldrade funktioner att få en SAS-URI från IoT Hub. Använd POST-metoden som beskrivs ovan.

IoT Hub har två REST-slutpunkter för filuppladdning, en för att hämta SAS-URI för lagring och den andra att meddela IoT-hubb för en överförda. Enheten startar Filöverföring genom att skicka en hämtning på IoT-hubben på `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. IoT hub returnerar:

* En SAS-URI för filen som ska laddas upp.

* Ett Korrelations-ID som ska användas när överföringen är klar.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Meddela IoT-hubb för en slutförd uppladdning

Enheten överförs filen till storage med hjälp av SDK: er för Azure Storage. När överföringen är klar, enheten skickar en POST-begäran till `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` med följande JSON-texten:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Värdet för `isSuccess` är ett booleskt värde som anger om filen har överförts. Statuskoden för `statusCode` är status för uppladdning av filen till lagring, och `statusDescription` motsvarar den `statusCode`.

## <a name="reference-topics"></a>Referensämnen:

Följande referens ger dig mer information om hur du överför filer från en enhet.

## <a name="file-upload-notifications"></a>Ladda upp filmeddelanden

När en enhet meddelas IoT Hub att ladda upp är klar, genererar du kan också ett meddelande i IoT Hub. Det här meddelandet innehåller och platsen för filen.

Enligt beskrivningen i [slutpunkter](iot-hub-devguide-endpoints.md), IoT-hubb levererar filen ladda upp meddelanden via en tjänst-riktade slutpunkt (**/messages/servicebound/fileuploadnotifications**) som meddelanden. Ta emot-semantik för filmeddelanden för uppladdning är desamma som för meddelanden från moln till enhet och har samma [meddelande livscykel](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle). Varje meddelande som hämtas från filen uppladdning aviseringsslutpunkten är en JSON-post med följande egenskaper:

| Egenskap  | Beskrivning |
| --- | --- |
| EnqueuedTimeUtc |Tidsstämpel som visar när meddelandet skapades. |
| DeviceId |**DeviceId** på den enhet som du laddade upp filen. |
| BlobUri |URI för den överförda filen. |
| BlobName |Namnet på den överförda filen. |
| lastUpdatedTime |Tidsstämpel som visar då filen senast uppdaterades. |
| BlobSizeInBytes |Storleken på den överförda filen. |

**Exempel**. Det här exemplet visar innehållet i en fil ladda upp meddelandet.

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

## <a name="file-upload-notification-configuration-options"></a>Konfigurationsalternativ för filen ladda upp meddelande

Varje IoT-hubb har följande konfigurationsalternativ för filen överföra meddelanden:

| Egenskap  | Beskrivning | Intervall- och standard |
| --- | --- | --- |
| **enableFileUploadNotifications** |Styr om meddelanden för uppladdning av filen skrivs till slutpunkten för fil-meddelanden. |Bool. Standard: SANT. |
| **fileNotifications.ttlAsIso8601** |Standard-TTL för filen ladda upp meddelanden. |ISO_8601 intervall upp till 48 H (minst 1 minut). Standard: 1 timme. |
| **fileNotifications.lockDuration** |Varaktighet för lås för filen ladda upp meddelanden kön. |5 och 300 sekunder (minst 5 sekunder). Standard: 60 sekunder. |
| **fileNotifications.maxDeliveryCount** |Maximalt antal leveranser för filen överför meddelandekö. |1 och 100. Standard: 100. |

## <a name="additional-reference-material"></a>Ytterligare referensmaterial

Andra referensavsnitten i IoT Hub developer guide inkluderar:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md) beskriver olika IoT hub-slutpunkter för körning och hanteringsåtgärder.

* [Begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskriver kvoter och begränsningar beteenden som gäller för IoT Hub-tjänsten.

* [Azure IoT-enheten och tjänsten SDK: er](iot-hub-devguide-sdks.md) visar en lista över olika språk SDK: er som du kan använda när du utvecklar appar för både enheten och tjänsten som interagerar med IoT Hub.

* [IoT Hub-frågespråk](iot-hub-devguide-query-language.md) beskriver frågespråk som du kan använda för att hämta information från IoT Hub om enhetstvillingar och jobb.

* [IoT Hub MQTT-support](iot-hub-mqtt-support.md) innehåller mer information om IoT Hub-stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu du har lärt dig hur du överför filer från enheter med hjälp av IoT Hub, kanske du är intresserad av i följande avsnitt för IoT Hub developer guide:

* [Hantera enhetsidentiteter i IoT Hub](iot-hub-devguide-identity-registry.md)

* [Styra åtkomst till IoT Hub](iot-hub-devguide-security.md)

* [Använda enhetstvillingar för att synkronisera tillstånd och konfigurationer](iot-hub-devguide-device-twins.md)

* [Anropa en direkt metod på en enhet](iot-hub-devguide-direct-methods.md)

* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

Se följande självstudie för IoT Hub för att prova några av de koncept som beskrivs i den här artikeln:

* [Ladda upp filer från enheter till molnet med IoT Hub](iot-hub-csharp-csharp-file-upload.md)