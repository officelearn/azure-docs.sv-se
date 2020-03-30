---
title: Azure Event Grid-schema för IoT Hub | Microsoft-dokument
description: Den här artikeln innehåller egenskaper och schema för Azure IoT Hub-händelser. Den visar tillgängliga händelsetyper, en exempelhändelse och händelseegenskaper.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: kgremban
ms.openlocfilehash: cfbd46ad961bd1dc914bae98e761cd83d445ff88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513039"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Azure Event Grid-händelseschema för IoT Hub

Den här artikeln innehåller egenskaper och schema för Azure IoT Hub-händelser. En introduktion till händelsescheman finns i [Azure Event Grid-händelseschema](event-schema.md). 

En lista över exempelskript och självstudier finns i [IoT Hub-händelsekälla](event-sources.md#iot-hub).

## <a name="available-event-types"></a>Tillgängliga händelsetyper

Azure IoT Hub avger följande händelsetyper:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Devices.DeviceSkapad | Publicerad när en enhet är registrerad på en IoT-hubb. |
| Microsoft.Devices.DeviceDeleted | Publicerad när en enhet tas bort från en IoT-hubb. | 
| Microsoft.Devices.DeviceConnected | Publicerad när en enhet är ansluten till en IoT-hubb. |
| Microsoft.Devices.DeviceDisconnected Microsoft.DevicesDisconnected Microsoft.Devices.DeviceDisconnected Microsoft.Devices. | Publicerad när en enhet kopplas från en IoT-hubb. | 
| Microsoft.Devices.DeviceTelemetry | Publicerat när ett telemetrimeddelande skickas till en IoT-hubb. |

Alla enhetshändelser utom enhetstelemetrihändelser är allmänt tillgängliga i alla regioner som stöds av Event Grid. Enhetstelemetrihändelsen är i offentlig förhandsversion och är tillgänglig i alla regioner utom östra USA, västra USA, Västeuropa, [Azure Government,](../azure-government/documentation-government-welcome.md) [Azure China 21Vianet](/azure/china/china-welcome)och [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="example-event"></a>Exempel händelse

Schemat för DeviceConnected och DeviceDisconnected-händelser har samma struktur. Den här exempelhändelsen visar schemat för en händelse som utlöses när en enhet är ansluten till en IoT-hubb:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

DeviceTelemetry-händelsen utlöses när en telemetrihändelse skickas till en IoT Hub. Nedan visas ett exempelschema för den här händelsen.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Schemat för DeviceCreated- och DeviceDeleted-händelser har samma struktur. Den här exempelhändelsen visar schemat för en händelse som utlöses när en enhet registreras i en IoT-hubb:

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
      "deviceEtag": "null",
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
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Händelseegenskaper

Alla händelser innehåller samma data på den översta nivån: 

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| id | sträng | Unik identifierare för händelsen. |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet kan inte skrivas. Event Grid ger det här värdet. |
| Ämne | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| data | objekt | IoT Hub-händelsedata.  |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

För alla IoT Hub-händelser innehåller dataobjektet följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| hubName (hubName) | sträng | Namn på IoT Hub där enheten skapades eller togs bort. |
| deviceId | sträng | Enhetens unika identifierare. Den här skiftlägeskänsliga strängen kan vara upp till 128 tecken lång och stöder ASCII 7-bitars alfanumeriska tecken plus följande specialtecken: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

Innehållet i dataobjektet är olika för varje händelseutgivare. 

För IoT-hubbhändelser med **enhetsansluten** och **frånkopplad enhet** innehåller dataobjektet följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| moduleId (modulId) | sträng | Modulens unika identifierare. Det här fältet är endast utdata för modulenheter. Den här skiftlägeskänsliga strängen kan vara upp till 128 tecken lång och stöder ASCII 7-bitars alfanumeriska tecken plus följande specialtecken: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | objekt | Händelseinformation om enhetsanslutningstillstånd
| sequenceNumber (sequenceNumber) | sträng | Ett nummer som hjälper till att ange ordningen på enhetsanslutna eller enhetsfrånkopplade händelser. Den senaste händelsen har ett sekvensnummer som är högre än föregående händelse. Detta antal kan ändras med mer än 1, men ökar strikt. Se [hur du använder sekvensnummer](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

För **enhetstelmetri-IoT** Hub-händelse innehåller dataobjektet meddelandet enhet till moln i [IoT-hubbmeddelandeformat](../iot-hub/iot-hub-devguide-messages-construct.md) och har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| body | sträng | Innehållet i meddelandet från enheten. |
| properties | sträng | Programegenskaper är användardefinierade strängar som kan läggas till i meddelandet. Dessa fält är valfria. |
| systemegenskaper | sträng | [Systemegenskaper](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) hjälper till att identifiera innehållet och källan till meddelandena. Enhetstelemetrimeddelandet måste vara i ett giltigt JSON-format med contentType inställd på JSON och contentEncoding inställt på UTF-8 i meddelandesystemets egenskaper. Om detta inte är inställt skriver IoT Hub meddelandena i bas 64-kodat format.  |

För **enhetsskapade** och **enhetsuttagna** IoT Hub-händelser innehåller dataobjektet följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| Twin | objekt | Information om enhetstvillingen, som är molnrepresentationen av metadata för programenheter. | 
| Deviceid | sträng | Den unika identifieraren för enhetstvillingen. | 
| Etag | sträng | En validerare för att säkerställa konsekvens i uppdateringar till en enhetstvilling. Varje etag är garanterat unik per enhet twin. |  
| deviceEtag| sträng | En validerare för att säkerställa konsekvens i uppdateringar av ett enhetsregister. Varje deviceEtag är garanterat unik per enhetsregister. |
| status | sträng | Om enhetstvillingen är aktiverad eller inaktiverad. | 
| statusUpdateTime | sträng | Tidsstämpeln ISO8601 för den senaste enhets twin statusuppdateringen. |
| connectionState (anslutningsstat) | sträng | Om enheten är ansluten eller frånkopplad. | 
| lastActivityTime | sträng | TIDsstämpeln ISO8601 för den senaste aktiviteten. | 
| cloudToDeviceMessageCount | heltal | Antal moln-till enhetsmeddelanden som skickas till den här enheten. | 
| authenticationType | sträng | Autentiseringstyp som används `SAS` `SelfSigned`för `CertificateAuthority`den här enheten: antingen , eller . |
| x509Thumbprint | sträng | Tumavtrycket är ett unikt värde för x509-certifikatet, som ofta används för att hitta ett visst certifikat i ett certifikatarkiv. Tumavtrycket genereras dynamiskt med SHA1-algoritmen och finns inte fysiskt i certifikatet. | 
| primärTumbtryck | sträng | Primärt tumavtryck för x509-certifikatet. |
| sekundärTavtryck | sträng | Sekundärt tumavtryck för x509-certifikatet. | 
| version | heltal | Ett heltal som ökas med ett varje gång enhetstvillingen uppdateras. |
| Önskad | objekt | En del av egenskaperna som bara kan skrivas av programmets backend och läsas av enheten. | 
| Rapporterade | objekt | En del av egenskaperna som bara kan skrivas av enheten och läsas av programmets backend. |
| lastUpdated | sträng | Tidsstämpeln ISO8601 för den senaste enhets twin-egenskapsuppdateringen. | 

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är Event Grid?](overview.md)
* Mer information om hur IoT Hub och Event Grid fungerar tillsammans finns i [Reagera på IoT Hub-händelser med hjälp av Event Grid för att utlösa åtgärder](../iot-hub/iot-hub-event-grid.md).