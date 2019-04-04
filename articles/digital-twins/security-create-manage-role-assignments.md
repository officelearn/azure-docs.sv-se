---
title: Skapa och hantera rolltilldelningar i Azure Digital Twins | Microsoft Docs
description: Skapa och hantera rolltilldelningar i Azure Digital Twins.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: lyrana
ms.custom: seodec18
ms.openlocfilehash: 72155799971760e9ddc93746dceafb1ea554d88b
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905315"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Skapa och hantera rolltilldelningar i Azure Digital Twins

Azure Digital Twins använder rollbaserad åtkomstkontroll ([RBAC](./security-role-based-access-control.md)) att hantera åtkomst till resurser.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="role-assignments-overview"></a>Översikt över webbserverroll tilldelningar

Varje rolltilldelning överensstämmer med följande definition:

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

Tabellen nedan beskrivs alla attribut:

| Attribut | Namn | Krävs | Typ | Beskrivning |
| --- | --- | --- | --- | --- |
| roleId | Identifierare för rollen arbetsflödesdefinition | Ja | String | Unikt ID för den önskade rolltilldelningen. Hitta roll- och deras ID genom att fråga System-API: et eller granskar tabellen nedan. |
| objekt-ID | Objekt-ID: | Ja | String | ID för Azure Active Directory, service principal objekt-ID eller domännamn. Vad eller vem rolltilldelningen har tilldelats. Rolltilldelningen måste vara formaterad enligt dess associerade typ. För den `DomainName` objectIdType, objectId måste börja med den `“@”` tecken. |
| objectIdType | Typ av identifierare | Ja | String | Vilken typ av objekt-ID som används. Se **stöds ObjectIdTypes** nedan. |
| sökväg | Utrymme sökväg | Ja | String | Fullständig åtkomst till sökvägen till den `Space` objekt. Ett exempel är `/{Guid}/{Guid}`. Om en identifierare som behöver rolltilldelningen för hela diagrammet, ange `"/"`. Det här tecknet betecknar roten, men dess användning rekommenderas inte. Alltid följa principen för lägsta behörighet. |
| tenantId | Klient-ID | Varierar | String | I de flesta fall en Azure Active Directory-klient-ID. Tillåts inte för `DeviceId` och `TenantId` ObjectIdTypes. Krävs för `UserId` och `ServicePrincipalId` ObjectIdTypes. Valfritt för DomainName ObjectIdType. |

### <a name="supported-role-definition-identifiers"></a>Identifierare för definition av roll som stöds

Varje rolltilldelning associerar en rolldefinition med en entitet i din digitala Twins för Azure-miljö.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Identifieraren objekttyper som stöds

Tidigare den **objectIdType** attributet introducerades.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Rollen tilldelningsåtgärden

Azure Digital Twins stöder fullständig *skapa*, *läsa*, och *ta bort* åtgärder för rolltilldelningar. *Uppdatera* operations hanteras genom att lägga till rolltilldelningar, ta bort rolltilldelningar eller ändra den [Spatial Intelligence Graph](./concepts-objectmodel-spatialgraph.md) noder som rolltilldelningar ge åtkomst till.

![Rollslutpunkter för tilldelning][1]

Den angivna Swagger-referensdokumentationen innehåller ytterligare information om alla tillgängliga API-slutpunkter, begäran åtgärder och definitioner.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

<div id="grant"></div>

### <a name="grant-permissions-to-your-service-principal"></a>Ge behörigheter till tjänstens huvudnamn

Bevilja behörighet till tjänstens huvudnamn är ofta en av de första stegen som du behöver vidta när du arbetar med Azure Digital Twins. Det innebär:

1. Logga in på Azure-instansen via PowerShell.
1. Hämtar din information om tjänstens huvudnamn.
1. Tilldela rollen till tjänstens huvudnamn.

Program-ID har angetts till dig i Azure Active Directory. Om du vill veta mer om hur du konfigurerar och etablera en Azure Digital Twins i Active Directory, Läs igenom den [snabbstarten](./quickstart-view-occupancy-dotnet.md).

När du har program-ID, kör du följande PowerShell-kommandon:

```shell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId  <ApplicationId>
```

En användare med den **Admin** rollen kan sedan tilldela utrymme administratörsroll till en användare genom att göra en autentiserad HTTP POST-begäran till URL:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Med följande JSON-texten:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

<div id="all"></div>

### <a name="retrieve-all-roles"></a>Hämta alla roller

![Roller för platssystem][2]

Om du vill visa alla tillgängliga roller (rolldefinitioner), gör du en autentiserad HTTP GET-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

En lyckad begäran returnerar en JSON-matris med poster för varje roll som kan tilldelas:

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

<div id="check"></div>

### <a name="check-a-specific-role-assignment"></a>Kontrollera en viss rolltilldelning

Om du vill kontrollera specifika rolltilldelningen, gör du en autentiserad HTTP GET-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Parametervärde** | **Krävs** |  **Typ** |  **Beskrivning** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | String |   ObjectId för användar-ID-objectIdType. |
| YOUR_PATH | True | String |   Den valda sökvägen att kontrollera åtkomst för. |
| YOUR_ACCESS_TYPE |  True | String |   Åtkomsttyp att söka efter. |
| YOUR_RESOURCE_TYPE | True | String |  Resursen att kontrollera. |

En lyckad begäran returnerar ett booleskt värde `true` eller `false` att indikera om vilken åtkomst har tilldelats till användaren för den angivna sökvägen och resursen.

### <a name="get-role-assignments-by-path"></a>Hämta rolltilldelningar efter sökväg

Om du vill hämta alla rolltilldelningar för en sökväg, gör du en autentiserad HTTP GET-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Värde | Ersätt med |
| --- | --- |
| YOUR_PATH | Den fullständiga sökvägen till området |

En lyckad begäran returnerar en JSON-matris med varje rolltilldelning som är associerade med den valda **sökväg** parameter:

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>Återkalla behörighet

Om du vill återkalla en behörigheter från en mottagare tar du bort rolltilldelningen genom att göra en autentiserad HTTP DELETE-begäran:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | Den **id** för att ta bort rolltilldelningen |

En lyckad DELETE-begäran returnerar 204 svarsstatusen. Kontrollera borttagningen av rolltilldelningen av [kontrollerar](#check) om rolltilldelningen fortfarande innehåller.

### <a name="create-a-role-assignment"></a>Skapa en rolltilldelning

Om du vill skapa en rolltilldelning, gör du en autentiserad HTTP POST-begäran till URL:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Kontrollera att JSON-texten överensstämmer med följande schema:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

En lyckad begäran ska returnera statusen 201-svaret tillsammans med den **id** nyligen skapade rolltilldelningen:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Konfigurationsexempel

Följande exempel visar hur du konfigurerar din JSON-brödtexten i scenarierna för vanligt förekommande rolltilldelning.

* **Exempel**: En användare behöver administrativ åtkomst till en våning utrymme för en klient.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Exempel**: Ett program körs testscenarier simulerade enheter och sensorer.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Exempel**: Alla användare som tillhör en domän får läsbehörighet för blanksteg, sensorer och användare. Den här åtkomsten innehåller motsvarande relaterade objekt.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Nästa steg

- Om du vill granska Azure Digital Twins roll –--åtkomstkontroll läsa [roll-bas-access-control](./security-authenticating-apis.md).

- Om du vill veta mer om Azure Digital Twins API-autentisering kan du läsa [API-autentisering](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/security-roles/roleassignments.png
[2]: media/security-roles/system.png
