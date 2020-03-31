---
title: Skapa och hantera rolltilldelningar - Azure Digital Twins | Microsoft-dokument
description: Läs mer om hur du skapar och hanterar rolltilldelningar i Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1c83ca0abfd17db873bec62f0a0d052703862a45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110400"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Skapa och hantera rolltilldelningar i Azure Digital Twins

Azure Digital Twins använder rollbaserad åtkomstkontroll ([RBAC](./security-role-based-access-control.md)) för att hantera åtkomst till resurser.

## <a name="role-assignments-overview"></a>Översikt över rolltilldelningar

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

I tabellen nedan beskrivs varje attribut:

| Attribut | Namn | Krävs | Typ | Beskrivning |
| --- | --- | --- | --- | --- |
| roleId (rollId) | Identifierare för rolldefinition | Ja | String | Det unika ID:t för den önskade rolltilldelningen. Hitta rolldefinitioner och deras identifierare genom att fråga system-API:et eller granska tabellen nedan. |
| Objectid | Objektidentifierare | Ja | String | Ett Azure Active Directory-ID, tjänsthuvudobjekt-ID eller domännamn. Vad eller vem rolltilldelningen är tilldelad till. Rolltilldelningen måste formateras enligt den associerade typen. För `DomainName` objectIdType måste objectId börja `“@”` med tecknet. |
| objectIdType | Objektidentifierare | Ja | String | Den typ av objektidentifierare som används. Se **ObjectIdTypes som stöds** nedan. |
| path | Utrymmesväg | Ja | String | Den fullständiga åtkomstsökvägen `Space` till objektet. Ett exempel är `/{Guid}/{Guid}`. Om en identifierare behöver rolltilldelningen `"/"`för hela diagrammet anger du . Det här tecknet anger roten, men dess användning avskräcks. Följ alltid principen om lägsta privilegium. |
| tenantId (hyresgäst) | Klientidentifierare | Varierar | String | I de flesta fall ett Azure Active Directory-klient-ID. Otillåtna för `DeviceId` och `TenantId` ObjectIdTypes. Krävs `UserId` för `ServicePrincipalId` och ObjectIdTypes. Valfritt för DomainName ObjectIdType. |

### <a name="supported-role-definition-identifiers"></a>Rolldefinitionsidentifierare som stöds

Varje rolltilldelning associerar en rolldefinition med en entitet i din Azure Digital Twins-miljö.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Objektidentifierartyper som stöds

Tidigare introducerades **attributet objectIdType.**

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Rolltilldelningsoperationer

Azure Digital Twins stöder fullständiga *åtgärder skapa,* *läs*och *ta bort* för rolltilldelningar. *UPDATE-åtgärder* hanteras genom att lägga till rolltilldelningar, ta bort rolltilldelningar eller ändra noderna [Spatial Intelligence Graph](./concepts-objectmodel-spatialgraph.md) som rolltilldelningar ger åtkomst till.

[![Slutpunkter för rolltilldelning](media/security-roles/role-assignments.png)](media/security-roles/role-assignments.png#lightbox)

Den medföljande Swagger-referensdokumentationen innehåller ytterligare information om alla tillgängliga API-slutpunkter, begäransåtgärder och definitioner.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="grant-permissions-to-your-service-principal"></a>Bevilja behörigheter till tjänstens huvudnamn

Att bevilja behörigheter till tjänstens huvudnamn är ofta ett av de första stegen du tar när du arbetar med Azure Digital Twins. Det innebär följande:

1. Logga in på din Azure-instans via [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) eller [PowerShell](https://docs.microsoft.com/powershell/azure/).
1. Hämtar din huvudinformation för tjänsten.
1. Tilldela önskad roll till tjänstens huvudnamn.

Ditt program-ID levereras till dig i Azure Active Directory. Om du vill veta mer om hur du konfigurerar och etablerar en Azure Digital Twins i Active Directory läser du igenom [Snabbstarten](./quickstart-view-occupancy-dotnet.md).

När du har programmets ID kör du något av följande kommandon. I Azure CLI:

```azurecli
az login
az ad sp show --id <ApplicationId>
```

I Powershell:

```powershell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId <ApplicationId>
```

En användare med **rollen Admin** kan sedan tilldela rollen Space Administrator till en användare genom att göra en autentiserat HTTP POST-begäran till URL:en:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

Med följande JSON kropp:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

### <a name="retrieve-all-roles"></a>Hämta alla roller

[![Systemroller](media/security-roles/system-api.png)](media/security-roles/system-api.png#lightbox)

Om du vill visa alla tillgängliga roller (rolldefinitioner) gör du en autentiserat HTTP GET-begäran till:

```URL
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

### <a name="check-a-specific-role-assignment"></a>Kontrollera en viss rolltilldelning

Om du vill kontrollera en viss rolltilldelning gör du en autentiserat HTTP GET-begäran till:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Parametervärde** | **Obligatoriskt** |  **Typ** |  **Beskrivning** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | String |   ObjectId för UserId-objectIdType. |
| YOUR_PATH | True | String |   Den valda sökvägen att söka efter. |
| YOUR_ACCESS_TYPE |  True | String |   *Läsa,* *skapa,* *uppdatera*eller *ta bort* |
| YOUR_RESOURCE_TYPE | True | String |  *Enhet*, *DeviceBlobMetadata*, *DeviceExtendedProperty*, *ExtendedPropertyKey*, *ExtendedType*, *Endpoint*, *KeyStore*, *Matcher*, *Ontology*, *Rapport*, *RoleDefinition*, *Sensor*, *SensorExtendedProperty*, *Space*, *SpaceBlobMetadata*, *SpaceExtendedProperty*, *SpaceResource*, *SpaceRoleAssignment*, *System*, * UerDefinedFunction*, *Användare*, *UserBlobMetadata*eller *UserExtendedProperty* |

En lyckad begäran returnerar en boolesk `true` eller `false` för att ange om åtkomsttypen har tilldelats användaren för den angivna sökvägen och resursen.

### <a name="get-role-assignments-by-path"></a>Hämta rolltilldelningar efter sökväg

Om du vill hämta alla rolltilldelningar för en sökväg gör du en autentiserat HTTP GET-begäran till:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Värde | Ersätt med |
| --- | --- |
| YOUR_PATH | Den fullständiga vägen till utrymmet |

En lyckad begäran returnerar en JSON-matris med varje rolltilldelning som är associerad med den valda **sökvägen:**

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

### <a name="revoke-a-permission"></a>Återkalla en behörighet

Om du vill återkalla en behörighet från en mottagare tar du bort rolltilldelningen genom att göra en autentrad HTTP DELETE-begäran:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | **Id** för rolltilldelningen för att ta bort |

En lyckad DELETE-begäran returnerar en 204-svarsstatus. Verifiera borttagningen av rolltilldelningen genom [att kontrollera](#check-a-specific-role-assignment) om rolltilldelningen fortfarande finns kvar.

### <a name="create-a-role-assignment"></a>Skapa en rolltilldelning

Om du vill skapa en rolltilldelning gör du en autentiserat HTTP POST-begäran till URL:en:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

Kontrollera att JSON-brödtexten överensstämmer med följande schema:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

En lyckad begäran returnerar en 201-svarsstatus tillsammans med **id:et** för den nyligen skapade rolltilldelningen:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Exempel på konfiguration

Följande exempel visar hur du konfigurerar JSON-brödtexten i flera vanliga scenarier för rolltilldelning.

* **Exempel**: En användare behöver administrativ åtkomst till en våning i ett klientutrymme.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Exempel**: Ett program kör testscenarier som hånar enheter och sensorer.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Exempel:** Alla användare som ingår i en domän får läsbehörighet för mellanslag, sensorer och användare. Den här åtkomsten inkluderar motsvarande relaterade objekt.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Nästa steg

- Om du vill granska Azure Digital Twins rollbaserad åtkomstkontroll läser du [Roll-base-access-control](./security-authenticating-apis.md).

- Mer information om Azure Digital Twins API-autentisering läser du [API-autentisering](./security-authenticating-apis.md).
