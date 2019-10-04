---
title: Skapa och hantera roll tilldelningar – Azure Digitals flätar | Microsoft Docs
description: Lär dig mer om att skapa och hantera roll tilldelningar i Azure Digitals flätas.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: lyhughes
ms.custom: seodec18
ms.openlocfilehash: 9a9f3398df099eca7d83b38595364956e6b3b76b
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827706"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Skapa och hantera roll tilldelningar i Azure Digitals flätas

Azure Digitals flätar använder rollbaserad åtkomst kontroll ([RBAC](./security-role-based-access-control.md)) för att hantera åtkomst till resurser.

## <a name="role-assignments-overview"></a>Översikt över roll tilldelningar

Varje roll tilldelning överensstämmer med följande definition:

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

| Attribut | Name | Obligatorisk | Typ | Beskrivning |
| --- | --- | --- | --- | --- |
| roleId | Identifierare för roll definition | Ja | Sträng | Unikt ID för den önskade roll tilldelningen. Hitta roll definitioner och deras identifierare genom att fråga system-API: et eller granska tabellen nedan. |
| objekt-ID | Objekt identifierare | Ja | Sträng | Ett Azure Active Directory-ID, tjänstens huvud objekt-ID eller domän namn. Vad eller vem roll tilldelningen är tilldelad till. Roll tilldelningen måste vara formaterad enligt dess associerade typ. För objectIdType måste ObjectID börja `“@”` med-symbolen. `DomainName` |
| objectIdType | Typ av objekt identifierare | Ja | Sträng | Typ av objekt identifierare som används. Se **ObjectIdTypes som stöds** nedan. |
| path | Sökväg till Space | Ja | Sträng | Fullständig åtkomst Sök väg till `Space` objektet. Ett exempel är `/{Guid}/{Guid}`. Om en identifierare behöver roll tilldelningen för hela grafen anger `"/"`du. Det här alternativet anger roten, men användningen rekommenderas inte. Följ alltid principen om minsta behörighet. |
| tenantId | Klient-ID | Varierar | Sträng | I de flesta fall är ett Azure Active Directory klient-ID. Tillåts inte för `DeviceId` och `TenantId` ObjectIdTypes. Krävs för `UserId` och `ServicePrincipalId` ObjectIdTypes. Valfritt för domän namn ObjectIdType. |

### <a name="supported-role-definition-identifiers"></a>Identifierare för roll definition som stöds

Varje roll tilldelning associerar en roll definition med en entitet i din Azure digital-miljö.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Typer av objekt identifierare som stöds

Tidigare introducerades attributet **objectIdType** .

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Roll tilldelnings åtgärder

Azure Digitals dubbla har stöd för fullständiga åtgärder för att *skapa*, *läsa*och *ta bort* roll tilldelningar. *Uppdaterings* åtgärder hanteras genom att lägga till roll tilldelningar, ta bort roll tilldelningar eller ändra de [diagram över spatial information](./concepts-objectmodel-spatialgraph.md) som roll tilldelningarna ger åtkomst till.

[@no__t tilldelnings slut punkter för 1Role](media/security-roles/roleassignments.png)](media/security-roles/roleassignments.png#lightbox)

Den tillhandahållna Swagger Reference-dokumentationen innehåller ytterligare information om alla tillgängliga API-slutpunkter, begär ande åtgärder och definitioner.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="grant-permissions-to-your-service-principal"></a>Bevilja behörighet till tjänstens huvud namn

Att bevilja behörighet till tjänstens huvud namn är ofta ett av de första stegen du ska vidta när du arbetar med digitala Digital-objekt i Azure. Den har följande:

1. Logga in på Azure-instansen via [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) eller [PowerShell](https://docs.microsoft.com/powershell/azure/).
1. Hämtar information om tjänstens huvud namn.
1. Tilldela den önskade rollen till tjänstens huvud namn.

Ditt program-ID anges till dig i Azure Active Directory. Läs igenom [snabb](./quickstart-view-occupancy-dotnet.md)starten om du vill veta mer om att konfigurera och tillhandahålla ett digitalt Azure-innehåll i Active Directory.

När du har program-ID: t kör du ett av följande kommandon. I Azure CLI:

```azurecli
az login
az ad sp show --id <ApplicationId>
```

I PowerShell:

```powershell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId <ApplicationId>
```

En användare med **Administratörs** rollen kan sedan tilldela rollen disk utrymmes administratör till en användare genom att göra en autentiserad HTTP POST-begäran till URL: en:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Med följande JSON-brödtext:

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

[![System roller](media/security-roles/system.png)](media/security-roles/system.png#lightbox)

Om du vill visa en lista över alla tillgängliga roller (roll definitioner) gör du en autentiserad HTTP GET-begäran till:

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

### <a name="check-a-specific-role-assignment"></a>Kontrol lera en speciell roll tilldelning

Om du vill kontrol lera en speciell roll tilldelning gör du en autentiserad HTTP GET-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Parameter värde** | **Kunna** |  **Typ** |  **Beskrivning** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  Sant | Sträng |   ObjectId för UserId-objectIdType. |
| YOUR_PATH | Sant | Sträng |   Den valda sökvägen för att kontrol lera åtkomsten för. |
| YOUR_ACCESS_TYPE |  Sant | Sträng |   *Läsa*, *skapa*, *Uppdatera*eller *ta bort* |
| YOUR_RESOURCE_TYPE | Sant | Sträng |  *Enhet*, *DeviceBlobMetadata*, *DeviceExtendedProperty*, *ExtendedPropertyKey*, *ExtendedType*, *slut punkt*, nyckel *lager*, *matchnings*enhet, *Ontology*, *rapport*,  *Roll definitions*, *sensor*, *SensorExtendedProperty*, *Space*, *SpaceBlobMetadata*, *SpaceExtendedProperty*, *SpaceResource*, *SpaceRoleAssignment*, *system* , *UerDefinedFunction*, *användare*, *UserBlobMetadata*eller *UserExtendedProperty* |

En lyckad begäran kommer att returnera ett `true` booleskt `false` värde eller ange om åtkomst typen har tilldelats användaren för den angivna sökvägen och resursen.

### <a name="get-role-assignments-by-path"></a>Hämta roll tilldelningar efter sökväg

Om du vill hämta alla roll tilldelningar för en sökväg gör du en autentiserad HTTP GET-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Value | Ersätt med |
| --- | --- |
| YOUR_PATH | Den fullständiga sökvägen till utrymmet |

En lyckad begäran returnerar en JSON-matris med varje roll tilldelning som är associerad med den valda **Sök vägs** parametern:

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

Om du vill återkalla en behörighet från en mottagare tar du bort roll tilldelningen genom att göra en autentiserad HTTP DELETE-begäran:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | **ID** för den roll tilldelning som ska tas bort |

En lyckad BORTTAGNINGs förfrågan returnerar en 204-svars status. Verifiera borttagningen av roll tilldelningen genom att [kontrol lera](#check-a-specific-role-assignment) om roll tilldelningen fortfarande innehåller.

### <a name="create-a-role-assignment"></a>Skapa en roll tilldelning

Skapa en roll tilldelning genom att göra en autentiserad HTTP POST-begäran till URL: en:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Kontrol lera att JSON-innehållet överensstämmer med följande schema:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

En lyckad begäran returnerar en 201 svars status tillsammans med **ID: t** för den nyligen skapade roll tilldelningen:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Exempel på konfigurationer

Följande exempel visar hur du konfigurerar din JSON-brödtext i flera vanliga scenarier för roll tilldelning.

* **Exempel**: En användare behöver administrativ åtkomst till en våning i ett klient utrymme.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Exempel**: Ett program kör test scenarier som modellerar enheter och sensorer.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Exempel**: Alla användare som ingår i en domän får Läs behörighet för utrymmen, sensorer och användare. Den här åtkomsten innehåller motsvarande relaterade objekt.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Nästa steg

- För att granska Azure Digitals med rollbaserad åtkomst kontroll, Läs [roll-grundläggande-åtkomst-kontroll](./security-authenticating-apis.md).

- Läs [API-autentisering](./security-authenticating-apis.md)om du vill veta mer om Azure Digitals dubbla API-autentisering.
