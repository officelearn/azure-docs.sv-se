---
title: Utgående och slutpunkter – Azure Digital Twins | Microsoft-dokument
description: Lär dig hur du skapar och egress-händelseslutpunkter i Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3803802a3d81655091d8be543ae9cb17221a98d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511577"
---
# <a name="egress-and-endpoints-in-azure-digital-twins"></a>Utgående och slutpunkter i Azure Digital Twins

Azure Digital *Twins-slutpunkter* representerar en meddelande- eller händelsemäklare i en användares Azure-prenumeration. Händelser och meddelanden kan skickas till Azure Event Hubs, Azure Event Grid och Azure Service Bus ämnen.

Händelser dirigeras till slutpunkter enligt fördefinierade routningsinställningar. Användare anger vilka *händelsetyper* varje slutpunkt kan få.

Mer information om händelser, routning och händelsetyper finns [i Routningshändelser och meddelanden i Azure Digital Twins](./concepts-events-routing.md).

## <a name="events"></a>Händelser

Händelser skickas av IoT-objekt (till exempel enheter och sensorer) för bearbetning av Azure-meddelande- och händelsemäklare. Händelser definieras av följande [azure event grid-händelseschemareferens](../event-grid/event-schema.md).

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
| id | sträng | Unik identifierare för händelsen. |
| Ämne | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| data | objekt | Händelsedata som är specifika för resursleverantören. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet kan inte skrivas. Event Grid ger det här värdet. |

Mer information om händelseschemat för Händelserutnätet:

- Granska [händelseschemareferensen för Azure Event Grid](../event-grid/event-schema.md).
- Läs [referensen För Azure EventGrid Node.js SDK EventGridEvent](https://docs.microsoft.com/javascript/api/@azure/eventgrid/eventgridevent?view=azure-node-latest).

## <a name="event-types"></a>Händelsetyper

Händelsetyper klassificerar händelsens art och anges i fältet **eventType.** Tillgängliga händelsetyper anges i följande lista:

- TopologyOperation
- UdfCustom (UdfCustom)
- SensorÄnd av
- SpaceChange (spacechange)
- DeviceMessage (Enhetsmessage)

Händelseformaten för varje händelsetyp beskrivs ytterligare i följande underavsnitt.

### <a name="topologyoperation"></a>TopologyOperation

**TopologiOperation** gäller för grafändringar. Ämnesegenskapen anger vilken typ av objekt som påverkas. **subject** Följande typer av objekt kan utlösa den här händelsen:

- Enhet
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType (Uttr.
- Keystore
- Rapport
- RoleDefinition
- Sensor
- SensorBlobMetadata
- SensorExtendedProperty
- Space
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource (utskälla)
- SpaceRoleAssignment
- System
- Användare
- UserBlobMetadata
- AnvändareExtendedProperty

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

### <a name="udfcustom"></a>UdfCustom (UdfCustom)

**UdfCustom** är en händelse som skickas av en användardefinierad funktion (UDF).
  
> [!IMPORTANT]  
> Den här händelsen måste uttryckligen skickas från UDF själv.

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

### <a name="sensorchange"></a>SensorÄnd av

**SensorChange** är en uppdatering av en sensors tillstånd baserat på telemetriförändringar.

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

### <a name="spacechange"></a>SpaceChange (spacechange)

**SpaceChange** är en uppdatering av ett blanksteg baserat på telemetriändringar.

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

### <a name="devicemessage"></a>DeviceMessage (Enhetsmessage)

Genom att använda **DeviceMessage**kan du ange en **EventHub-anslutning** som råa telemetrihändelser kan dirigeras till samt från Azure Digital Twins.

> [!NOTE]
> - **DeviceMessage** kan endast kombineras med **EventHub**. Du kan inte kombinera **DeviceMessage** med någon av de andra händelsetyperna.
> - Du kan bara ange en slutpunkt för kombinationen av typen **EventHub** eller **DeviceMessage**.

## <a name="configure-endpoints"></a>Konfigurera slutpunkter

Slutpunktshantering utövas via api:et för slutpunkter.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Följande exempel visar hur du konfigurerar slutpunkter som stöds.

>[!IMPORTANT]
> Var uppmärksam på attributet **eventTypes.** Den definierar vilka händelsetyper som hanteras av slutpunkten och bestämmer därmed dess routning.

En autentiserat HTTP POST-begäran mot:

```URL
YOUR_MANAGEMENT_API_URL/endpoints
```

- Händelsetyper för väg till servicebuss **SensorChange,** **SpaceChange**och **TopologyOperation:**

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
    | YOUR_NAMESPACE | Slutpunktens namnområde |
    | YOUR_PRIMARY_KEY | Den primära anslutningssträngen som används för att autentisera |
    | YOUR_SECONDARY_KEY | Den sekundära anslutningssträngen som används för att autentisera |
    | YOUR_TOPIC_NAME | Namnet på ditt anpassade ämne |

- Händelsetyper för väg till händelserutnät **SensorChange,** **SpaceChange**och **TopologyOperation:**

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
    | YOUR_PRIMARY_KEY | Den primära anslutningssträngen som används för att autentisera|
    | YOUR_SECONDARY_KEY | Den sekundära anslutningssträngen som används för att autentisera |
    | YOUR_TOPIC_NAME | Namnet på ditt anpassade ämne |

- Händelsetyper för väg till händelsehubbar **SensorChange,** **SpaceChange**och **TopologyOperation:**

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
    | YOUR_NAMESPACE | Slutpunktens namnområde |
    | YOUR_PRIMARY_KEY | Den primära anslutningssträngen som används för att autentisera |
    | YOUR_SECONDARY_KEY | Den sekundära anslutningssträngen som används för att autentisera |
    | YOUR_EVENT_HUB_NAME | Namnet på ditt evenemangsnav |

- Händelsetypen **DeviceMessage**för väg till händelsehubbar . Införandet av `EntityPath` i **anslutningenString** är obligatoriskt:

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
    | YOUR_NAMESPACE | Slutpunktens namnområde |
    | YOUR_PRIMARY_KEY | Den primära anslutningssträngen som används för att autentisera |
    | YOUR_SECONDARY_KEY | Den sekundära anslutningssträngen som används för att autentisera |
    | YOUR_EVENT_HUB_NAME | Namnet på ditt evenemangsnav |

> [!NOTE]  
> När en ny slutpunkt skapas kan det ta upp till 5 till 10 minuter att börja ta emot händelser vid slutpunkten.

## <a name="primary-and-secondary-connection-keys"></a>Primära och sekundära anslutningsnycklar

När en primär anslutningsnyckel blir obehörig försöker systemet automatiskt med den sekundära anslutningsnyckeln. Det ger en säkerhetskopia och ger möjlighet att smidigt autentisera och uppdatera den primära nyckeln via Endpoints API.

Om både de primära och sekundära anslutningsnycklarna är obehöriga går systemet in i en exponentiell väntetid på upp till 30 minuter. Händelser släpps på varje utlöst starttid.

När systemet är i back-off wait-tillstånd kan det ta upp till 30 minuter att uppdatera anslutningsnycklar via endpoints-API:et.

## <a name="unreachable-endpoints"></a>Slutpunkter som inte kan nås

När en slutpunkt inte kan nås går systemet in i en exponentiell starttid på upp till 30 minuter. Händelser släpps på varje utlöst starttid.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Azure Digital Twins Swagger](how-to-use-swagger.md).

- Läs mer om [routningshändelser och meddelanden](concepts-events-routing.md) i Azure Digital Twins.
