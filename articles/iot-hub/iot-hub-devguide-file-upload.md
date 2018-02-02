---
title: "Förstå Azure IoT Hub filöverföringen | Microsoft Docs"
description: "Utvecklarhandbok - Använd funktionen filen överför i IoT-hubb för att hantera överföring av filer från en enhet till en Azure storage blob-behållare."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a0427925-3e40-4fcd-96c1-2a31d1ddc14b
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 7bf1ba333f36dcfa8959320566bcb771f37cfe22
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="upload-files-with-iot-hub"></a>Överföra filer med IoT-hubb

Detaljerad i den [IoT-hubbslutpunkter] [ lnk-endpoints] artikel, en enhet kan initiera en filöverföring genom att skicka ett meddelande via en enhet riktade slutpunkt (**/devices/ {deviceId} / filer**). När en enhet meddelar IoT-hubb som en överföringen är klar, IoT-hubb skickas ett meddelande för filen överför via den **/messages/servicebound/filenotifications** service-riktade slutpunkt.

I stället för förtroendeförmedling meddelanden via IoT-hubb själva fungerar IoT-hubb i stället som en dispatcher till ett associerat Azure Storage-konto. En enhet begär ett storage-token från IoT-hubb som är specifik för enheten som ska ladda upp filen. Enheten använder SAS-URI för att överföra filen till lagring och när överföringen är klar enheten skickar ett meddelande om slutförande till IoT-hubb. IoT-hubb kontrollerar filöverföringen har slutförts och sedan lägger till ett meddelande för överföring av filen aviseringsslutpunkten service-riktade filen.

Innan du överför en fil till IoT-hubb från en enhet måste du konfigurera din hubb av [associera en Azure Storage] [ lnk-associate-storage] kontot till den.

Enheten kan sedan [initiera en överföring] [ lnk-initialize] och sedan [meddela IoT-hubb] [ lnk-notify] när överföringen är klar. Du kan också när en enhet meddelar IoT-hubb att överföringen är klar, tjänsten kan generera en [meddelande][lnk-service-notification].

### <a name="when-to-use"></a>När du ska använda detta

Använda filuppladdning för att skicka mediefiler och stora telemetri batchar har laddats upp av periodvis anslutna enheter eller komprimerade för att spara bandbredd.

Referera till [enhet till moln kommunikation vägledning] [ lnk-d2c-guidance] om osäkra mellan att använda rapporterade egenskaper, meddelanden från enhet till moln eller ladda upp filen.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associera ett Azure Storage-konto med IoT-hubb

Om du vill använda funktionen överför filen måste du först länka ett Azure Storage-konto till IoT-hubben. Du kan göra detta med hjälp av den [Azure-portalen][lnk-management-portal], eller programmässigt via den [IoT-hubb resursprovidern REST API: er][lnk-resource-provider-apis]. När du har associerat ett Azure Storage-konto med IoT-hubben, returnerar tjänsten en SAS-URI till en enhet när enheten initierar en begäran för överföring av filen.

> [!NOTE]
> Den [Azure IoT SDK] [ lnk-sdks] automatiskt hanterar hämta SAS-URI, överföra filen och meddela en överförda IoT-hubb.


## <a name="initialize-a-file-upload"></a>Initiera en filöverföring
IoT-hubben har en slutpunkt för enheter att begära en SAS-URI för att överföra en fil. Om du vill initiera filöverföring enheten skickar en POST-begäran till `{iot hub}.azure-devices.net/devices/{deviceId}/files` med följande JSON-meddelandetext:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT-hubb returnerar följande uppgifter, som enheten använder för att överföra filen:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Föråldrad: initiera en filöverföring med GET

> [!NOTE]
> Det här avsnittet beskrivs föråldrade funktioner att få en SAS-URI från IoT-hubb. Använda POST-metoden som beskrivs ovan.

IoT-hubben har två REST-slutpunkter som stöd för filöverföring, en för att hämta SAS-URI för lagring och den andra för att meddela IoT-hubb för en överförda. Enheten initierar Filöverföring genom att skicka GET till IoT-hubb på `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. IoT-hubben returnerar:

* En SAS-URI för filen ska överföras.
* En Korrelations-ID som ska användas när överföringen har slutförts.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Meddela IoT-hubb slutförda filöverföringen

Enheten är ansvarig för att överföra filen till lagring med hjälp av Azure Storage SDK: erna. När överföringen är klar, enheten skickar en POST-begäran till `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` med följande JSON-meddelandetext:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Värdet för `isSuccess` är en boolesk som representerar om filen har överförts. Statuskoden för `statusCode` är status för överföring av filen till lagring, och `statusDescription` motsvarar den `statusCode`.

## <a name="reference-topics"></a>Referensinformation:

Följande referensavsnitt ge mer information om hur du överför filer från en enhet.

## <a name="file-upload-notifications"></a>Filen överför meddelanden

När en enhet meddelar IoT-hubb som en överföringen är klar, du kan också genererar ett meddelande som innehåller namn och lagring platsen för filen IoT-hubb.

Enligt beskrivningen i [slutpunkter][lnk-endpoints], IoT-hubb levererar filen överför meddelanden via en slutpunkt för service-riktade (**/messages/servicebound/fileuploadnotifications**) som meddelanden. Receive-semantik för filen överför meddelanden är desamma som för meddelanden moln till enhet och har samma [meddelandet livscykel][lnk-lifecycle]. Varje meddelande som hämtas från filen överför aviseringsslutpunkten är en JSON-post med följande egenskaper:

| Egenskap | Beskrivning |
| --- | --- |
| EnqueuedTimeUtc |Tidsstämpel som visar när meddelandet har skapats. |
| DeviceId |**DeviceId** på den enhet som laddats upp filen. |
| BlobUri |URI för den överförda filen. |
| BlobName |Namnet på den överförda filen. |
| LastUpdatedTime |Tidsstämpel som anger när filen senast uppdaterades. |
| BlobSizeInBytes |Storleken på den överförda filen. |

**Exempel**. Det här exemplet visar innehållet i en fil överför meddelandet.

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

## <a name="file-upload-notification-configuration-options"></a>Konfigurationsalternativ för filen överför meddelande

Varje IoT-hubb visar följande konfigurationsalternativ för filen överför meddelanden:

| Egenskap | Beskrivning | Intervall och standard |
| --- | --- | --- |
| **enableFileUploadNotifications** |Kontrollerar om filen överför meddelanden skrivs till filen meddelanden slutpunkten. |Bool. Standard: True. |
| **fileNotifications.ttlAsIso8601** |Standard-TTL för filen överför meddelanden. |ISO_8601 intervall upp till 48 H (minst 1 minut). Standard: 1 timme. |
| **fileNotifications.lockDuration** |Lås varaktighet för filen överför meddelanden kön. |5 till 300 sekunder (minst 5 sekunder). Standard: 60 sekunder. |
| **fileNotifications.maxDeliveryCount** |Leverans av maximalt antal för filen överför aviseringskön. |1 och 100. Standard: 100. |

## <a name="additional-reference-material"></a>Ytterligare referensmaterialet

Andra referensavsnitten i utvecklarhandboken för IoT-hubben är:

* [IoT-hubbslutpunkter] [ lnk-endpoints] beskriver de olika slutpunkter som varje IoT-hubb visar för körning och hanteringsåtgärder.
* [Begränsning och kvoter] [ lnk-quotas] beskriver kvoter och begränsning beteenden som tillämpas på tjänsten IoT-hubb.
* [Azure IoT-enheten och tjänsten SDK] [ lnk-sdks] Listar olika språk SDK: er som du kan använda när du utvecklar appar för både enheten och tjänsten som interagerar med IoT-hubben.
* [IoT-hubb frågespråket] [ lnk-query] beskriver frågespråk som du kan använda för att hämta information från IoT-hubb om enheten twins och jobb.
* [Stöd för IoT-hubb MQTT] [ lnk-devguide-mqtt] ger mer information om stöd för IoT-hubb för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu du har lärt dig hur du överför filer från enheter med hjälp av IoT-hubb, kan du är intresserad av i följande avsnitt för IoT-hubb developer-guide:

* [Hantera identiteter för enheten i IoT-hubb][lnk-devguide-identities]
* [Kontrollera åtkomsten till IoT-hubb][lnk-devguide-security]
* [Använd twins för enheten för att synkronisera tillstånd och konfigurationer][lnk-devguide-device-twins]
* [Anropa en metod som är direkt på en enhet][lnk-devguide-directmethods]
* [Schema-jobb på flera enheter][lnk-devguide-jobs]

Om du vill prova några av de begrepp som beskrivs i den här artikeln finns i följande IoT-hubb kursen:

* [Hur du överför filer från enheter till molnet med IoT-hubb][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
