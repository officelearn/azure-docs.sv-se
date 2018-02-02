---
title: "Azure händelse rutnätet schemat för IoT-hubb | Microsoft Docs"
description: "Referenssida för händelsen schemaformat och egenskaperna för IoT-hubb"
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 29ad1233a344c3085286c27cb925b2dc9fb41f7e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Azure händelse rutnätet Händelseschema för IoT-hubb

Den här artikeln innehåller egenskaperna och schemat för Azure IoT Hub-händelser. En introduktion till händelse scheman, se [Azure händelse rutnätet Händelseschema](event-schema.md). 

## <a name="available-event-types"></a>Tillgängliga händelsetyper

Azure IoT-hubb genererar följande händelse:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicerade när en enhet är registrerad på en IoT-hubb. |
| Microsoft.Devices.DeviceDeleted | Publicerade när en enhet tas bort från en IoT-hubb. | 

## <a name="example-event"></a>Exempel-händelse

Schemat för DeviceCreated och DeviceDeleted händelser har samma struktur. Det här exemplet händelse visas schemat för en händelse som aktiveras när en enhet är registrerad på en IoT-hubb:

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

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| id | sträng | Unik identifierare för händelsen. |
| Avsnittet | sträng | Fullständigt labbresurs sökvägen till händelsekällan. Det här fältet är skrivskyddat. Händelsen rutnätet innehåller det här värdet. |
| Ämne | sträng | Publisher-definierade sökvägen till ämnet för händelsen. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekälla. |
| EventTime | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| data | objekt | IoT-hubb händelsedata.  |
| dataVersion | sträng | Schemaversion av dataobjektets primärnycklar. Utgivaren definierar schemaversionen. |
| metadataVersion | sträng | Schemaversion för metadata för händelsen. Händelsen rutnätet definierar schemat för egenskaperna på den översta nivån. Händelsen rutnätet innehåller det här värdet. |

Innehållet i dataobjektet är olika för varje händelse utgivare. Dataobjekt som innehåller följande egenskaper för IoT-hubb händelser:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| hubName | sträng | Namnet på IoT-hubben där enheten skapas eller tas bort. |
| deviceId | sträng | Unik identifierare för enheten. Den här skiftlägeskänsliga strängen kan vara upp till 128 tecken långt och har stöd för ASCII-7-bitars alfanumeriska tecken och följande specialtecken: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| operationTimestamp | sträng | ISO8601 tidsstämpel för åtgärden. |
| opType | sträng | Händelsetypen som angetts för den här åtgärden med IoT-hubb: antingen `DeviceCreated` eller `DeviceDeleted`.
| dubbla | objekt | Information om enheten dubbla, som är moln represenation programmetadata för enheten. | 
| deviceID | sträng | Unik identifierare för enheten dubbla. | 
| ETag | sträng | En typ av information som beskriver innehållet i dubbla för enheten. Varje etag är säkert att vara unikt för varje enhet dubbla. | 
| status | sträng | Om enheten dubbla aktiveras eller inaktiveras. | 
| statusUpdateTime | sträng | Uppdatera ISO8601 tidsstämpel för senaste enhetens dubbla status. |
| connectionState | sträng | Om enheten är ansluten eller frånkopplad. | 
| lastActivityTime | sträng | ISO8601 tidsstämpel för den senaste aktiviteten. | 
| cloudToDeviceMessageCount | heltal | Antal moln till enhet meddelanden som skickas till den här enheten. | 
| AuthenticationType | sträng | Autentiseringstypen som används för den här enheten: antingen `SAS`, `SelfSigned`, eller `CertificateAuthority`. |
| X509Thumbprint | sträng | Certifikatets tumavtryck är ett unikt värde för x509 certifikat, används vanligtvis för att hitta ett visst certifikat i ett certifikatarkiv. Certifikatets tumavtryck genereras dynamiskt med hjälp av algoritmen SHA1 och fysiskt finns inte i certifikatet. | 
| primaryThumbprint | sträng | Primär tumavtrycket för x509 certifikat. |
| secondaryThumbprint | sträng | Sekundär tumavtrycket för x509 certifikat. | 
| version | heltal | Ett heltal som ökar varje gång enheten dubbla uppdateras. |
| önskad | objekt | En del av de egenskaper som kan skrivs bara av programmet backend- och läses av enheten. | 
| rapporterat | objekt | En del av de egenskaper som kan skrivs endast av enheten och läses av programmet serverdel. |
| lastUpdated | sträng | Uppdatera ISO8601 tidsstämpel för den senaste dubbla enhetsegenskap. | 

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure händelse rutnätet finns [vad är händelsen rutnätet?](overview.md)
* Mer information om hur IoT-hubb och händelsen rutnätet tillsammans, se [ta hänsyn till IoT-hubb händelser med händelse rutnätet för att utlösaråtgärder](../iot-hub/iot-hub-event-grid.md).