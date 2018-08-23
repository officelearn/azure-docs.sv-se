---
title: Schema för Azure Event Grid för IoT Hub | Microsoft Docs
description: Referenssidan för händelse schemaformat och egenskaperna för IoT Hub
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 08/17/2018
ms.author: kgremban
ms.openlocfilehash: 4bb33eae53d31701b66d13cb4e810b1a0b8a4b0b
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42055619"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Azure Event Grid-Händelseschema för IoT Hub

Den här artikeln innehåller egenskaperna och schema för Azure IoT Hub-händelser. En introduktion till Händelsescheman i [Azure Event Grid Händelseschema](event-schema.md). 

En lista över exempel på skript och självstudier finns i [IoT Hub-händelsekälla](event-sources.md#iot-hub).

## <a name="available-event-types"></a>Tillgängliga händelsetyper

Azure IoT Hub genererar följande händelsetyper:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicerade när en enhet registreras till en IoT hub. |
| Microsoft.Devices.DeviceDeleted | Publicerade när en enhet tas bort från en IoT-hubb. | 

## <a name="example-event"></a>Exempel-händelse

Schemat för DeviceCreated och DeviceDeleted händelser har samma struktur. Den här exempelhändelse visar schemat för en händelse som aktiveras när en enhet har registrerats till en IoT hub:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
    "opType": "DeviceCreated"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Egenskaper för händelse

Alla händelser som innehåller samma översta data: 

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| id | sträng | Unik identifierare för händelsen. |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet är skrivskyddat. Event Grid ger det här värdet. |
| Ämne | sträng | Publisher-definierade sökvägen till ämne för händelsen. |
| Händelsetyp | sträng | En av typerna som registrerade händelsen för den här händelsekällan. |
| eventTime | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| data | objekt | IoT Hub händelsedata.  |
| dataVersion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| metadataVersion | sträng | Schemaversion för händelsemetadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Innehållet i dataobjektet är olika för varje händelse-utgivare. För IoT Hub-händelser innehåller dataobjektet följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| HubName | sträng | Namnet på IoT Hub där enheten skapas eller tas bort. |
| deviceId | sträng | Unik identifierare för enheten. Den här skiftlägeskänsliga strängar kan innehålla upp till 128 tecken och har stöd för ASCII 7 bitar alfanumeriska tecken och följande specialtecken: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| operationTimestamp | sträng | ISO8601-tidsstämpel för åtgärden. |
| opType | sträng | Vilken typ av händelse som angetts för den här åtgärden av IoT Hub: antingen `DeviceCreated` eller `DeviceDeleted`.
| enhetstvilling | objekt | Information om enhetstvillingen, vilket är molnet represenation programmetadata för enheten. | 
| deviceID | sträng | Den unika identifieraren för enhetstvillingen. | 
| ETag | sträng | En del av informationen som beskriver innehållet i enhetstvillingen. Varje etag garanteras vara unikt per enhetstvillingen. | 
| status | sträng | Om enhetstvillingen är aktiverat eller inaktiverat. | 
| statusUpdateTime | sträng | Uppdatera ISO8601-tidsstämpel för senaste enhetens twin status. |
| connectionState | sträng | Om enheten är ansluten eller frånkopplad. | 
| lastActivityTime | sträng | ISO8601-tidsstämpel för senaste aktivitet. | 
| cloudToDeviceMessageCount | heltal | Antal moln till enhetsmeddelanden som skickas till den här enheten. | 
| authenticationType | sträng | Autentiseringstypen som används för den här enheten: antingen `SAS`, `SelfSigned`, eller `CertificateAuthority`. |
| x509Thumbprint | sträng | Tumavtrycket är ett unikt värde för x509 certifikat, som ofta används för att hitta ett visst certifikat i ett certifikatarkiv. Tumavtrycket genereras dynamiskt med hjälp av algoritmen SHA1 och fysiskt finns inte i certifikatet. | 
| primaryThumbprint | sträng | Primära tumavtrycket för x509 certifikat. |
| secondaryThumbprint | sträng | Sekundära tumavtrycket för x509 certifikat. | 
| version | heltal | Ett heltal som ökas med 1 varje gång enheten twin uppdateras. |
| önskad | objekt | En del av de egenskaper som kan skrivas endast med program backend- och läsas av enheten. | 
| rapporterat | objekt | En del av de egenskaper som kan skrivs endast av enheten och läses av programmet backend-server. |
| lastUpdated | sträng | Uppdatera ISO8601-tidsstämpel för den senaste twin enhetsegenskapen. | 

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [vad är Event Grid?](overview.md)
* Mer information om hur IoT Hub och Event Grid fungerar tillsammans, se [reagera på IoT Hub-händelser med Event Grid för att utlösaråtgärder](../iot-hub/iot-hub-event-grid.md).