---
title: Förstå rolldefinitioner i RBAC för Azure-resurser | Microsoft-dokument
description: Lär dig mer om rolldefinitioner i rollbaserad åtkomstkontroll (RBAC) för finkornig åtkomsthantering av Azure-resurser.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: e4e4ac1b0a867130dd7b9e276db52e1ca1e72976
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062133"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Förstå rolldefinitioner för Azure-resurser

Om du försöker förstå hur en roll fungerar eller om du skapar din egen [anpassade roll för Azure-resurser](custom-roles.md)är det bra att förstå hur roller definieras. I den här artikeln beskrivs information om rolldefinitioner och några exempel.

## <a name="role-definition-structure"></a>Rolldefinitionsstruktur

En *rolldefinition* är en uppsättning behörigheter. Ibland kallas det helt enkelt för en *roll*. En rolldefinition listar de åtgärder som kan utföras, till exempel läsa, skriva och ta bort. Den kan också ange vilka åtgärder som inte kan utföras eller åtgärder relaterade till underliggande data. En rolldefinition har följande struktur:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

Åtgärder anges med strängar som har följande format:

- `{Company}.{ProviderName}/{resourceType}/{action}`

Den `{action}` del av en operationssträng anger vilken typ av åtgärder du kan utföra på en resurstyp. Du ser till exempel följande delsträngar `{action}`i:

| Delsträng för åtgärd    | Beskrivning         |
| ------------------- | ------------------- |
| `*` | Jokertecknet ger åtkomst till alla åtgärder som matchar strängen. |
| `read` | Aktiverar läsåtgärder (GET). |
| `write` | Aktiverar skrivåtgärder (PUT eller PATCH). |
| `action` | Aktiverar anpassade åtgärder som omstart av virtuella datorer (POST). |
| `delete` | Aktiverar borttagningsåtgärder (DELETE). |

Här är [deltagarrolldefinitionen](built-in-roles.md#contributor) i JSON-format. Jokertecknet (`*`) under `Actions` anger att huvudnamnet som tilldelats den här rollen kan utföra alla åtgärder, eller med andra ord sköta all administration. Det här inbegriper även åtgärder som definieras i framtiden när Azure lägger till nya resurstyper. Åtgärderna under `NotActions` dras bort från `Actions`. För rollen [Deltagare](built-in-roles.md#contributor) tar `NotActions` bort rollens möjlighet att hantera åtkomsten till resurser och även att ge åtkomst till resurser.

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

## <a name="management-and-data-operations"></a>Hantering och dataverksamhet

Rollbaserad åtkomstkontroll för hanteringsåtgärder anges `Actions` i `NotActions` egenskaperna och för en rolldefinition. Här är några exempel på hanteringsåtgärder i Azure:

- Hantera åtkomst till ett lagringskonto
- Skapa, uppdatera eller ta bort en blob-behållare
- Ta bort en resursgrupp och alla dess resurser

Hanteringsåtkomst ärvs inte till dina data förutsatt att behållarautentiseringsmetoden är inställd på "Azure AD-användarkonto" och inte "Åtkomstnyckel". Den här separationen förhindrar att`*`roller med jokertecken ( ) har obegränsad åtkomst till dina data. Om en användare till exempel har en [reader-roll](built-in-roles.md#reader) i en prenumeration kan de visa lagringskontot, men som standard kan de inte visa underliggande data.

Tidigare användes inte rollbaserad åtkomstkontroll för dataåtgärder. Auktoriseringen för dataåtgärder varierade mellan resursleverantörer. Samma rollbaserade auktoriseringsmodell för åtkomstkontroll som används för hanteringsåtgärder har utökats till dataåtgärder.

För att stödja dataåtgärder har nya dataegenskaper lagts till i rolldefinitionsstrukturen. Dataåtgärder anges i egenskaperna `DataActions` och `NotDataActions`. Genom att lägga till dessa dataegenskaper bibehålls separationen mellan hantering och data. På så sätt kan du förhindra att rolltilldelningar med jokertecken (`*`) plötsligt får åtkomst till data. Här är några dataåtgärder du kan ange i `DataActions` och `NotDataActions`:

- Läsa en lista med blobar i en container
- Skriva till en lagringsblob i en container
- Ta bort ett meddelande i en kö

Här är rolldefinitionen [för Storage Blob Data Reader,](built-in-roles.md#storage-blob-data-reader) som innehåller åtgärder i både `Actions` egenskaper och `DataActions` egenskaper. Med den här rollen kan du läsa blob-behållaren och även underliggande blob-data.

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Du kan bara lägga till dataåtgärder i egenskaperna `DataActions` och `NotDataActions`. Resursleverantörer identifierar vilka åtgärder som är `isDataAction` dataåtgärder genom att ange egenskapen till `true`. En lista över de `isDataAction` åtgärder `true`där den finns finns i [Resursprovideråtgärder](resource-provider-operations.md). Roller som inte har dataåtgärder behöver `DataActions` `NotDataActions` inte ha och egenskaper i rolldefinitionen.

Auktorisering för alla API-anrop för hanteringsåtgärder hanteras av Azure Resource Manager. API-anrop för dataåtgärd hanteras av antingen en resursprovider eller Azure Resource Manager.

### <a name="data-operations-example"></a>Exempel på dataåtgärder

För att bättre förstå hur hantering och dataåtgärder fungerar, låt oss överväga ett specifikt exempel. Alice har tilldelats rollen [Ägare](built-in-roles.md#owner) i prenumerationsomfånget. Bob har tilldelats rollen [Storage Blob Data Contributor](built-in-roles.md#storage-blob-data-contributor) i ett scope för lagringskonto. I följande diagram visas det här exemplet.

![Rollbaserad åtkomstkontroll har utökats för att stödja både hantering och dataåtgärder](./media/role-definitions/rbac-management-data.png)

[Ägarrollen](built-in-roles.md#owner) för Alice och rollen [Storage Blob Data Contributor](built-in-roles.md#storage-blob-data-contributor) för Bob har följande åtgärder:

Ägare

&nbsp;&nbsp;&nbsp;&nbsp;Åtgärder<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Storage Blob Data Contributor

&nbsp;&nbsp;&nbsp;&nbsp;Åtgärder<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataÅtgärder<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Eftersom Alice har en`*`jokertecken ( ) åtgärd i en prenumeration omfattning, deras behörigheter ärver ner så att de kan utföra alla hanteringsåtgärder. Alice kan läsa, skriva och ta bort behållare. Alice kan dock inte utföra dataåtgärder utan att vidta ytterligare åtgärder. Som standard kan alice till exempel inte läsa blobbar i en behållare. För att läsa blobbar måste Alice hämta lagringsåtkomstnycklarna och använda dem för att komma åt blobbar.

Bobs behörigheter är begränsade `Actions` till `DataActions` bara den och angivna rollen [Storage Blob Data Contributor.](built-in-roles.md#storage-blob-data-contributor) Baserat på rollen kan Bob utföra både hanterings- och dataåtgärder. Bob kan till exempel läsa, skriva och ta bort behållare i det angivna lagringskontot och även läsa, skriva och ta bort blobbar.

Mer information om hantering och dataplansäkerhet för lagring finns i [säkerhetsguiden](../storage/blobs/security-recommendations.md)för Azure Storage .

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Vilka verktyg stöder användning av RBAC för dataåtgärder?

Om du vill visa och arbeta med dataåtgärder måste du ha rätt versioner av verktygen eller SDK:erna:

| Verktyg  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 eller senare |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 eller senare |
| [Azure för .NET](/dotnet/azure/) | 2.8.0-förhandsvisning eller senare |
| [Azure SDK för Go](/azure/go/azure-sdk-go-install) | 15.0.0 eller senare |
| [Azure för Java](/java/azure/) | 1.9.0 eller senare |
| [Azure för Python](/azure/python/) | 0.40.0 eller senare |
| [Azure SDK för Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 eller senare |

Om du vill visa och använda dataåtgärderna i REST API måste du ange parametern **api-version** till följande version eller senare:

- 2018-07-01

## <a name="actions"></a>Åtgärder

Behörigheten `Actions` anger de hanteringsåtgärder som rollen tillåter att utföras. Det är en samling åtgärdssträngar som identifierar sekursabara åtgärder för Azure-resursleverantörer. Här är några exempel på hanteringsåtgärder `Actions`som kan användas i .

> [!div class="mx-tableFixed"]
> | Operationssträng    | Beskrivning         |
> | ------------------- | ------------------- |
> | `*/read` | Ger åtkomst till läsåtgärder för alla resurstyper för alla Azure-resursleverantörer.|
> | `Microsoft.Compute/*` | Ger åtkomst till alla åtgärder för alla resurstyper i Microsoft.Compute-resursprovidern.|
> | `Microsoft.Network/*/read` | Ger åtkomst till läsåtgärder för alla resurstyper i Microsoft.Network-resursleverantören.|
> | `Microsoft.Compute/virtualMachines/*` | Ger åtkomst till alla åtgärder för virtuella datorer och dess underordnade resurstyper.|
> | `microsoft.web/sites/restart/Action` | Ger åtkomst till omstart av en webbapp.|

## <a name="notactions"></a>NotActions

Behörigheten `NotActions` anger de hanteringsåtgärder som är `Actions`undantagna från den tillåtna . Använd `NotActions` behörigheten om den uppsättning åtgärder som du vill tillåta lättare definieras genom att utesluta begränsade åtgärder. Åtkomsten som beviljas av en roll (gällande behörigheter) beräknas `Actions` genom att de åtgärder som används subtraheras `NotActions` från åtgärderna.

> [!NOTE]
> Om en användare tilldelas en roll som `NotActions`utesluter en åtgärd i och tilldelas en andra roll som ger åtkomst till samma åtgärd, tillåts användaren att utföra åtgärden. `NotActions`är inte en neka regel - det är helt enkelt ett bekvämt sätt att skapa en uppsättning tillåtna åtgärder när specifika åtgärder måste uteslutas.
>

## <a name="dataactions"></a>DataÅtgärder

Behörigheten `DataActions` anger de dataåtgärder som rollen tillåter att utföras på dina data i det objektet. Om en användare till exempel har läst åtkomst till blob-data till ett lagringskonto kan de läsa blobbar i det lagringskontot. Här är några exempel på dataåtgärder `DataActions`som kan användas i .

> [!div class="mx-tableFixed"]
> | Operationssträng    | Beskrivning         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Returnerar en blob eller en lista med blobbar. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Returnerar resultatet av att skriva en blob. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Returnerar ett meddelande. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Returnerar ett meddelande eller resultatet av att ett meddelande skrivs eller tas bort. |

## <a name="notdataactions"></a>NotDataActions

Behörigheten `NotDataActions` anger de dataåtgärder som är `DataActions`undantagna från den tillåtna . Åtkomsten som beviljas av en roll (gällande behörigheter) beräknas `DataActions` genom att de åtgärder som används subtraheras `NotDataActions` från åtgärderna. Varje resursprovider tillhandahåller sina respektive uppsättning API:er för att uppfylla dataåtgärder.

> [!NOTE]
> Om en användare tilldelas en roll som utesluter `NotDataActions`en dataåtgärd i och tilldelas en andra roll som ger åtkomst till samma dataåtgärd, tillåts användaren att utföra den dataåtgärden. `NotDataActions`är inte en neka regel - det är helt enkelt ett bekvämt sätt att skapa en uppsättning tillåtna dataåtgärder när specifika dataåtgärder måste uteslutas.
>

## <a name="assignablescopes"></a>AssignableScopes

Egenskapen `AssignableScopes` anger de scope (hanteringsgrupper, prenumerationer eller resursgrupper) som har den här rolldefinitionen tillgänglig. Du kan göra rollen tillgänglig för tilldelning i endast hanteringsgrupper, prenumerationer eller resursgrupper som kräver den. Du måste använda minst en hanteringsgrupp, prenumeration eller resursgrupp.

Inbyggda roller `AssignableScopes` har angett rotomfattningsscopet (`"/"`). Rotomfånget anger att rollen är tillgänglig för tilldelning i alla scope. Exempel på giltiga assignable scopes är:

> [!div class="mx-tableFixed"]
> | Rollen är tillgänglig för tilldelning | Exempel |
> |----------|---------|
> | Ett abonnemang | `"/subscriptions/{subscriptionId1}"` |
> | Två abonnemang | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Resursgrupp för nätverk | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | En ledningsgrupp | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Ledningsgrupp och en prenumeration | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Alla scope (gäller endast inbyggda roller) | `"/"` |

Information om `AssignableScopes` anpassade roller finns i [Anpassade roller för Azure-resurser](custom-roles.md).

## <a name="next-steps"></a>Nästa steg

* [Inbyggda roller för Azure-resurser](built-in-roles.md)
* [Anpassade roller för Azure-resurser](custom-roles.md)
* [Azure Resource Manager-resursprovideråtgärder](resource-provider-operations.md)
