---
title: Utgående och slutpunkter i Azure Digital Twins | Microsoft Docs
description: Riktlinjer för hur du skapar slutpunkter med Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c09ee84cda5f0a9747d3ee1f8f1b37d1323f2cc2
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212258"
---
# <a name="egress-and-endpoints"></a>Utgående och slutpunkter

Azure Digital Twins stöder begreppet _slutpunkter_ där varje slutpunkt representerar en meddelande/händelsekoordinator i användarens Azure-prenumeration. Händelser och meddelanden kan skickas till **Händelsehubb**, **Event Grid**, och **Service Bus-ämnen**.

Händelser skickas till slutpunkter i enlighet med fördefinierade inställningar för routning: användaren kan ange vilken slutpunkt som ska ta emot någon av följande händelser: **TopologyOperation**, **UdfCustom**, **SensorChange**, **SpaceChange**, eller **DeviceMessage**.

En grundläggande förståelse för händelser som Routning och händelsetyper finns [routning händelser och meddelanden](concepts-events-routing.md).

## <a name="event-types-description"></a>Händelsebeskrivning typer

Här följer händelseformatet för varje händelse:

- **TopologyOperation**

  Gäller för ändringar i metadatadiagram. Den *ämne* egenskap anger typ av objekt som påverkas. Typer av objekt som kan utlösa den här händelsen är: **enhet**, **DeviceBlobMetadata**, **DeviceExtendedProperty**, **ExtendedPropertyKey**, **ExtendedType**, **KeyStore**, **rapporten**, **RoleDefinition**, **Sensor**, **SensorBlobMetadata**, **SensorExtendedProperty**, **utrymme**, **SpaceBlobMetadata**,  **SpaceExtendedProperty**, **SpaceResource**, **SpaceRoleAssignment**, **System**, **användaren**, **UserBlobMetadata**, **UserExtendedProperty**.

  Exempel:

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
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Anpassade attributets namn | Ersätt med |
    | --- | --- |
    | *yourTopicName* | Namnet på ditt anpassade ämne |

- **UdfCustom**

  En händelse som skickas av en användardefinierad funktion (UDF). 
  
  > [!IMPORTANT]
  > Den här händelsen har uttryckligen skickas från UDF själva.

  Exempel:

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
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Anpassade attributets namn | Ersätt med |
    | --- | --- |
    | *yourTopicName* | Namnet på ditt anpassade ämne |

- **SensorChange**

  En uppdatering av en sensor tillstånd baserat på ändringar av telemetri.

  Exempel:

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
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Anpassade attributets namn | Ersätt med |
    | --- | --- |
    | *yourTopicName* | Namnet på ditt anpassade ämne |

- **SpaceChange**

  En uppdatering av ett blanksteg tillstånd baserat på ändringar av telemetri.

  Exempel:

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
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Anpassade attributets namn | Ersätt med |
    | --- | --- |
    | *yourTopicName* | Namnet på ditt anpassade ämne |

- **DeviceMessage**

  Låter dig ange en **EventHub** anslutning som råtelemetridata händelser kan vidarebefordras samt från Azure Digital Twins.

> [!NOTE]
> - **DeviceMessage** är Kombinerbara endast med **EventHub**; du kan inte kombinera **DeviceMessage** med andra typer av händelse.
> - Du kommer att kunna ange endast en slutpunkt av kombinationen av typ **EventHub** eller **DeviceMessage**.

## <a name="configuring-endpoints"></a>Konfigurera slutpunkter

Hantering av slutpunkten utnyttjas via API-slutpunkter. Här följer några exempel om hur du konfigurerar de olika slutpunkterna som stöds. Vara särskilt uppmärksam på matrisen för typer som den definierar routning för slutpunkten:

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- Dirigera till **Service Bus** händelsetyper: **SensorChange**, **SpaceChange**, **TopologyOperation**

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourTopicName"
  }
  ```

    | Anpassade attributets namn | Ersätt med |
    | --- | --- |
    | *yourNamespace* | Namnområdet för din slutpunkt |
    | *yourPrimaryKey* | Primär anslutningssträng som används för att autentisera |
    | *yourSecondaryKey* | Sekundär anslutningssträng som används för att autentisera |
    | *yourTopicName* | Namnet på ditt anpassade ämne |

- Dirigera till **Event Grid** händelsetyper: **SensorChange**, **SpaceChange**, **TopologyOperation**

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "yourPrimaryKey",
    "secondaryConnectionString": "yourSecondaryKey",
    "path": "yourTopicName.westus-1.eventgrid.azure.net"
  }
  ```

    | Anpassade attributets namn | Ersätt med |
    | --- | --- |
    | *yourPrimaryKey* | Primär anslutningssträng som används för att autentisera|
    | *yourSecondaryKey* | Sekundär anslutningssträng som används för att autentisera |
    | *yourTopicName* | Namnet på ditt anpassade ämne |

- Dirigera till **Händelsehubb** händelsetyper: **SensorChange**, **SpaceChange**, **TopologyOperation**

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourEventHubName"
  }
  ```

    | Anpassade attributets namn | Ersätt med |
    | --- | --- |
    | *yourNamespace* | Namnområdet för din slutpunkt |
    | *yourPrimaryKey* | Primär anslutningssträng som används för att autentisera |
    | *yourSecondaryKey* | Sekundär anslutningssträng som används för att autentisera |
    | *yourEventHubName* | Namnet på din **Event Hub** |

- Dirigera till **Händelsehubb** Händelsetyp: **DeviceMessage**. Inkludering av `EntityPath` i den **connectionString** är obligatoriskt.

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey;EntityPath=yourEventHubName",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey;EntityPath=yourEventHubName",
    "path": "yourEventHubName"
  }
  ```

    | Anpassade attributets namn | Ersätt med |
    | --- | --- |
    | *yourNamespace* | Namnområdet för din slutpunkt |
    | *yourPrimaryKey* | Primär anslutningssträng som används för att autentisera |
    | *yourSecondaryKey* | Sekundär anslutningssträng som används för att autentisera |
    | *yourEventHubName* | Namnet på din **Event Hub** |

> [!NOTE]
> Det kan ta upp till 5 till 10 minuter för att börja ta emot händelser vid slutpunkten vid skapandet av en ny slutpunkt.

## <a name="primary-and-secondary-connection-keys"></a>Av primära och sekundära anslutningsnycklar

När en primär anslutningsnyckel blir obehörig, försöker den sekundära anslutningsnyckeln automatiskt i systemet. Som ger en säkerhetskopia och tillåter möjlighet att autentisera ett smidigt sätt och uppdatera den primära nyckeln via API-slutpunkter.

Om både de primära och sekundära anslutningen nycklarna har inte behörighet, anges systemet en exponentiell backoff väntetid på upp till 30 minuter. Händelser kommer att tas bort på varje utlösta backoff väntetid.

När systemet är i ett backoff vänta tillstånd, uppdatera anslutningar nycklar via API-slutpunkter kan ta upp till 30 minuter ska börja gälla.

## <a name="unreachable-endpoints"></a>Kan inte nås slutpunkter

När en slutpunkt blir oåtkomlig anger systemet en exponentiell backoff väntetid på upp till 30 minuter. Händelser kommer att tas bort på varje utlösta backoff väntetid.

## <a name="next-steps"></a>Nästa steg

Läs hur du använder Azure Digital Twins Swagger [Azure Digital Twins Swagger](how-to-use-swagger.md).

Mer information om routning-händelser och meddelanden i Azure Digital Twins [routning händelser och meddelanden](concepts-events-routing.md).
