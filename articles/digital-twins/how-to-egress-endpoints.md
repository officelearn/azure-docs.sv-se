---
title: Utgående och slutpunkter i Azure Digital Twins | Microsoft Docs
description: Riktlinjer för hur du skapar slutpunkter med Azure Digital Twins.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: alinast
ms.openlocfilehash: e93811a56f934a95dde45633c4fb64312b3696df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60924813"
---
# <a name="egress-and-endpoints"></a>Utgående och slutpunkter

Azure Digital Twins *slutpunkter* representerar förhandlare meddelandet eller händelse inom en användares Azure-prenumeration. Händelser och meddelanden kan skickas till Azure Event Hubs, Azure Event Grid och Azure Service Bus-ämnen.

Händelser dirigeras till slutpunkterna enligt fördefinierade inställningar för routning. Användare kan ange vilka *händelsetyper* får varje slutpunkt.

Mer information om händelser, Routning och händelsetyper, referera till [routning händelser och meddelanden i Azure Digital Twins](./concepts-events-routing.md).

## <a name="events"></a>Händelser

Händelser som skickas av IoT-objekt (till exempel enheter och sensorer) ska bearbetas av Azure meddelandet och händelsen asynkrona meddelandeköer. Händelser som definieras av följande [Schemareferens i Azure Event Grid event](../event-grid/event-schema.md).

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Attribut | Typ | Beskrivning |
| --- | --- | --- |
| id | string | Unik identifierare för händelsen. |
| ämne | string | Publisher-definierade sökvägen till ämne för händelsen. |
| data | objekt | Händelsedata är specifika för resursprovidern. |
| Händelsetyp | string | En av typerna som registrerade händelsen för den här händelsekällan. |
| eventTime | string | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| dataVersion | string | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| metadataVersion | string | Schemaversion för händelsemetadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |
| ämne | string | Fullständig resurssökväg till händelsekällan. Det här fältet är inte skrivbar. Event Grid ger det här värdet. |

Mer information om Event Grid-Händelseschema:

- Granska den [Schemareferens i Azure Event Grid event](../event-grid/event-schema.md).
- Läs den [referens för Azure EventGrid Node.js SDK EventGridEvent](https://docs.microsoft.com/javascript/api/azure-eventgrid/eventgridevent?view=azure-node-latest).

## <a name="event-types"></a>Händelsetyper

Händelsetyper klassificera natur händelsen och ställs i den **händelsetyp** fält. Tillgängliga händelsetyper anges av i följande lista:

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

I följande underavsnitt beskrivs ytterligare händelseformatet för varje händelsetyp av.

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** gäller för ändringar i metadatadiagram. Den **ämne** egenskap anger typ av objekt som påverkas. Följande typer av objekt kan utlösa den här händelsen:

- Enhet
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- KeyStore
- Rapport
- RoleDefinition
- Sensor
- SensorBlobMetadata
- SensorExtendedProperty
- Rymd
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- System
- Användare
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>Exempel

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Värde | Ersätt med |
| --- | --- |
| YOUR_TOPIC_NAME | Namnet på ditt anpassade ämne |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** är en händelse som skickas av en användardefinierad funktion (UDF).
  
> [!IMPORTANT]  
> Den här händelsen måste uttryckligen skickas från UDF själva.

#### <a name="example"></a>Exempel

```JSON
{
  "id": "568fd394-380b-46fa-925a-ebb96f658cce",
  "subject": "UdfCustom",
  "data": {
    "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "ResourceType": "Space",
    "Payload": "\"Room is not available or air quality is poor\"",
    "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
  },
  "eventType": "UdfCustom",
  "eventTime": "2018-10-02T06:50:15.198Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Värde | Ersätt med |
| --- | --- |
| YOUR_TOPIC_NAME | Namnet på ditt anpassade ämne |

### <a name="sensorchange"></a>SensorChange

**SensorChange** är en uppdatering av en sensor tillstånd baserat på ändringar av telemetri.

#### <a name="example"></a>Exempel

```JSON
{
  "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
  "subject": "SensorChange",
  "data": {
    "Type": "Classic",
    "DataType": "Motion",
    "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "Value": "False",
    "PreviousValue": "True",
    "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
    "MessageType": "sensor",
    "Properties": {
      "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
      "ms-activity-id": "ct22YwXEGJ5u.605.0"
    }
  },
  "eventType": "SensorChange",
  "eventTime": "2018-04-17T17:46:18.5452993Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Värde | Ersätt med |
| --- | --- |
| YOUR_TOPIC_NAME | Namnet på ditt anpassade ämne |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** är en uppdatering av ett blanksteg tillstånd baserat på ändringar av telemetri.

#### <a name="example"></a>Exempel

```JSON
{
  "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
  "subject": "SpaceChange",
  "data": {
    "Type": null,
    "DataType": "AvailableAndFresh",
    "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "Value": "Room is not available or air quality is poor",
    "PreviousValue": null,
    "RawData": null,
    "transactionId": null,
    "EventTimestamp": null,
    "MessageType": null,
    "Properties": null,
    "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
  },
  "eventType": "SpaceChange",
  "eventTime": "2018-10-02T06:50:20.128Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Värde | Ersätt med |
| --- | --- |
| YOUR_TOPIC_NAME | Namnet på ditt anpassade ämne |

### <a name="devicemessage"></a>DeviceMessage

Med hjälp av **DeviceMessage**, kan du ange en **EventHub** anslutning som råtelemetridata händelser kan vidarebefordras samt från Azure Digital Twins.

> [!NOTE]
> - **DeviceMessage** är Kombinerbara endast med **EventHub**. Du kan inte kombinera **DeviceMessage** med andra typer av händelse.
> - Du kan ange endast en slutpunkt av kombinationen av typ **EventHub** eller **DeviceMessage**.

## <a name="configure-endpoints"></a>Konfigurera slutpunkter

Hantering av slutpunkten utnyttjas via API-slutpunkter.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Följande exempel visar hur du konfigurerar slutpunkter som stöds.

>[!IMPORTANT]
> Betala titta noga på **eventTypes** attribut. Den definierar vilken händelse typer hanteras av slutpunkten och därmed avgöra dess routning.

En autentiserad HTTP POST-begäran mot

```plaintext
YOUR_MANAGEMENT_API_URL/endpoints
```

- Vägen till Service Bus händelsetyper **SensorChange**, **SpaceChange**, och **TopologyOperation**:

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | Värde | Ersätt med |
    | --- | --- |
    | YOUR_NAMESPACE | Namnområdet för din slutpunkt |
    | YOUR_PRIMARY_KEY | Primär anslutningssträng som används för att autentisera |
    | YOUR_SECONDARY_KEY | Sekundär anslutningssträng som används för att autentisera |
    | YOUR_TOPIC_NAME | Namnet på ditt anpassade ämne |

- Vägen till Event Grid händelsetyper **SensorChange**, **SpaceChange**, och **TopologyOperation**:

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | Värde | Ersätt med |
    | --- | --- |
    | YOUR_PRIMARY_KEY | Primär anslutningssträng som används för att autentisera|
    | YOUR_SECONDARY_KEY | Sekundär anslutningssträng som används för att autentisera |
    | YOUR_TOPIC_NAME | Namnet på ditt anpassade ämne |

- Vägen till Event Hubs händelsetyper **SensorChange**, **SpaceChange**, och **TopologyOperation**:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Värde | Ersätt med |
    | --- | --- |
    | YOUR_NAMESPACE | Namnområdet för din slutpunkt |
    | YOUR_PRIMARY_KEY | Primär anslutningssträng som används för att autentisera |
    | YOUR_SECONDARY_KEY | Sekundär anslutningssträng som används för att autentisera |
    | YOUR_EVENT_HUB_NAME | Namnet på din event hub |

- Vägen till Event Hubs händelsetyp **DeviceMessage**. Inkludering av `EntityPath` i den **connectionString** är obligatorisk:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Värde | Ersätt med |
    | --- | --- |
    | YOUR_NAMESPACE | Namnområdet för din slutpunkt |
    | YOUR_PRIMARY_KEY | Primär anslutningssträng som används för att autentisera |
    | YOUR_SECONDARY_KEY | Sekundär anslutningssträng som används för att autentisera |
    | YOUR_EVENT_HUB_NAME | Namnet på din event hub |

> [!NOTE]  
> Det kan ta upp till 5 till 10 minuter för att börja ta emot händelser vid slutpunkten vid skapandet av en ny slutpunkt.

## <a name="primary-and-secondary-connection-keys"></a>Av primära och sekundära anslutningsnycklar

När en primär anslutningsnyckel blir obehörig, försöker den sekundära anslutningsnyckeln automatiskt i systemet. Som ger en säkerhetskopia och tillåter möjlighet att autentisera ett smidigt sätt och uppdatera den primära nyckeln via API-slutpunkter.

Om både de primära och sekundära anslutningen nycklarna har inte behörighet, anges en exponentiell backoff väntetid på upp till 30 minuter. Händelser ignoreras på varje utlösta backoff väntetid.

När systemet är i ett backoff vänta tillstånd, uppdatera anslutningar nycklar via API-slutpunkter kan ta upp till 30 minuter ska börja gälla.

## <a name="unreachable-endpoints"></a>Kan inte nås slutpunkter

När en slutpunkt blir oåtkomlig, anges en exponentiell backoff väntetid på upp till 30 minuter. Händelser ignoreras på varje utlösta backoff väntetid.

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du använder Azure Digital Twins Swagger](how-to-use-swagger.md).

- Läs mer om [routning händelser och meddelanden](concepts-events-routing.md) i Azure Digital Twins.
