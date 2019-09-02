---
title: Utgående och slut punkter i Azure Digitals flätar | Microsoft Docs
description: Rikt linjer för hur du skapar slut punkter med Azure Digitals dubbla.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: alinast
ms.openlocfilehash: 3c33992ce3c130d6c06e0709a9c4ddcab4fff159
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013945"
---
# <a name="egress-and-endpoints"></a>Utgående och slut punkter

Azure Digitals dubbla *slut punkter* representerar ett meddelande eller händelse koordinator i en användares Azure-prenumeration. Händelser och meddelanden kan skickas till Azure Event Hubs, Azure Event Grid och Azure Service Bus ämnen.

Händelser dirigeras till slut punkter enligt fördefinierade inställningar för routning. Användare anger vilka *händelse typer* som varje slut punkt kan ta emot.

Mer information om händelser, Routning och händelse typer finns [i routning av händelser och meddelanden i Azure Digitals](./concepts-events-routing.md).

## <a name="events"></a>Events

Händelser skickas av IoT-objekt (till exempel enheter och sensorer) för bearbetning av Azure-meddelanden och händelse hanterare. Händelser definieras av följande [Azure Event Grid händelse schema referens](../event-grid/event-schema.md).

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

| Attribut | type | Beskrivning |
| --- | --- | --- |
| id | string | Unikt ID för händelsen. |
| subject | string | Publisher-definierad sökväg till händelsens ämne. |
| data | object | Händelse data som är speciella för resurs leverantören. |
| eventType | string | En av de registrerade händelse typerna för den här händelse källan. |
| eventTime | string | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| dataVersion | string | Dataobjektets schemaversion. Utgivaren definierar schema versionen. |
| metadataVersion | string | Schemaversion för händelsemetadata. Event Grid definierar schemat för de högsta nivå egenskaperna. Event Grid ger det här värdet. |
| subject | string | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |

Mer information om händelse schema för Event Grid:

- Granska [Azure Event Grid händelse schema referens](../event-grid/event-schema.md).
- Läs [EventGridEvent-referens för Azure EventGrid Node. js SDK](https://docs.microsoft.com/javascript/api/azure-eventgrid/eventgridevent?view=azure-node-latest).

## <a name="event-types"></a>Händelse typer

Händelse typer klassificerar händelsens karaktär och anges i fältet **eventType** . Tillgängliga händelse typer anges i följande lista:

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

Händelse formaten för varje händelse typ beskrivs närmare i följande underavsnitt.

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** gäller för Graph-ändringar. Egenskapen **subject** anger vilken typ av objekt som påverkas. Följande typer av objekt kan utlösa den här händelsen:

- Enhet
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- KeyStore
- Rapport
- Roll definitions
- Sensor
- SensorBlobMetadata
- SensorExtendedProperty
- Blanksteg
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

| Value | Ersätt med |
| --- | --- |
| YOUR_TOPIC_NAME | Namnet på ditt anpassade ämne |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** är en händelse som skickas av en användardefinierad funktion (UDF).
  
> [!IMPORTANT]  
> Den här händelsen måste skickas explicit från själva UDF-filen.

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

| Value | Ersätt med |
| --- | --- |
| YOUR_TOPIC_NAME | Namnet på ditt anpassade ämne |

### <a name="sensorchange"></a>SensorChange

**SensorChange** är en uppdatering av sensorns status baserat på telemetri-ändringar.

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

| Value | Ersätt med |
| --- | --- |
| YOUR_TOPIC_NAME | Namnet på ditt anpassade ämne |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** är en uppdatering av ett utrymmes tillstånd baserat på telemetri ändringar.

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

| Value | Ersätt med |
| --- | --- |
| YOUR_TOPIC_NAME | Namnet på ditt anpassade ämne |

### <a name="devicemessage"></a>DeviceMessage

Genom att använda **DeviceMessage**kan du ange en **EventHub** -anslutning till vilken oberedda telemetri-händelser kan dirigeras från digitala Azure-nätverk.

> [!NOTE]
> - **DeviceMessage** är endast kombin med **EventHub**. Du kan inte kombinera **DeviceMessage** med någon av de andra händelse typerna.
> - Du kan bara ange en slut punkt för kombinationen av typen **EventHub** eller **DeviceMessage**.

## <a name="configure-endpoints"></a>Konfigurera slutpunkter

Slut punkts hantering utnyttjas genom slut punkts-API: et.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Följande exempel visar hur du konfigurerar slut punkter som stöds.

>[!IMPORTANT]
> Betala noga noga med **eventTypes** -attributet. Den definierar vilka händelse typer som hanteras av slut punkten och bestämmer därför dess routning.

En autentiserad HTTP POST-begäran mot:

```plaintext
YOUR_MANAGEMENT_API_URL/endpoints
```

- Dirigera till Service Bus händelse typer **SensorChange**, **SpaceChange**och **TopologyOperation**:

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

    | Value | Ersätt med |
    | --- | --- |
    | YOUR_NAMESPACE | Namn området för din slut punkt |
    | YOUR_PRIMARY_KEY | Den primära anslutnings sträng som används för att autentisera |
    | YOUR_SECONDARY_KEY | Sekundär anslutnings sträng som används för att autentisera |
    | YOUR_TOPIC_NAME | Namnet på ditt anpassade ämne |

- Dirigera till Event Grid händelse typer **SensorChange**, **SpaceChange**och **TopologyOperation**:

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

    | Value | Ersätt med |
    | --- | --- |
    | YOUR_PRIMARY_KEY | Den primära anslutnings sträng som används för att autentisera|
    | YOUR_SECONDARY_KEY | Sekundär anslutnings sträng som används för att autentisera |
    | YOUR_TOPIC_NAME | Namnet på ditt anpassade ämne |

- Dirigera till Event Hubs händelse typer **SensorChange**, **SpaceChange**och **TopologyOperation**:

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

    | Value | Ersätt med |
    | --- | --- |
    | YOUR_NAMESPACE | Namn området för din slut punkt |
    | YOUR_PRIMARY_KEY | Den primära anslutnings sträng som används för att autentisera |
    | YOUR_SECONDARY_KEY | Sekundär anslutnings sträng som används för att autentisera |
    | YOUR_EVENT_HUB_NAME | Namnet på händelsehubben |

- Dirigera till Event Hubs händelse typ **DeviceMessage**. Inkludering av `EntityPath` i **ConnectionString** är obligatoriskt:

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

    | Value | Ersätt med |
    | --- | --- |
    | YOUR_NAMESPACE | Namn området för din slut punkt |
    | YOUR_PRIMARY_KEY | Den primära anslutnings sträng som används för att autentisera |
    | YOUR_SECONDARY_KEY | Sekundär anslutnings sträng som används för att autentisera |
    | YOUR_EVENT_HUB_NAME | Namnet på händelsehubben |

> [!NOTE]  
> När en ny slut punkt skapas kan det ta upp till 5 till 10 minuter att börja ta emot händelser vid slut punkten.

## <a name="primary-and-secondary-connection-keys"></a>Primära och sekundära anslutnings nycklar

När en primär anslutnings nyckel blir obehörig, försöker systemet automatiskt att använda den sekundära anslutnings nyckeln. Det ger en säkerhets kopia och gör det möjligt att på ett smidigt sätt autentisera och uppdatera den primära nyckeln via slut punkts-API: et.

Om både den primära och sekundära anslutnings nyckeln är otillåten, anger systemet en exponentiell vänte tid på upp till 30 minuter. Händelser släpps på varje utlöst vänte tid.

När systemet är i vänte läge på vänte läge kan det ta upp till 30 minuter att uppdatera anslutnings nycklar via slut punkts-API: et.

## <a name="unreachable-endpoints"></a>Otillgängliga slut punkter

När en slut punkt blir otillgänglig kan systemet ange en exponentiell vänte tid på upp till 30 minuter. Händelser släpps på varje utlöst vänte tid.

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du använder Azure Digitals dubbla Swagger](how-to-use-swagger.md).

- Lär dig mer om att [routa händelser och meddelanden](concepts-events-routing.md) i Azure Digitals.
