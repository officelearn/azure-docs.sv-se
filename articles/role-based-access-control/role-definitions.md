---
title: Förstå roll definitioner i RBAC för Azure-resurser | Microsoft Docs
description: Lär dig mer om roll definitioner i rollbaserad åtkomst kontroll (RBAC) för detaljerad åtkomst hantering av Azure-resurser.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 3ff4b2cb6a59a35dc6da4748a7c7fbb4758a4fcf
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981007"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Förstå roll definitioner för Azure-resurser

Om du försöker förstå hur en roll fungerar eller om du skapar en egen [anpassad roll för Azure-resurser](custom-roles.md), är det bra att förstå hur roller definieras. I den här artikeln beskrivs information om roll definitioner och några exempel.

## <a name="role-definition-structure"></a>Roll definitions struktur

En *rolldefinition* är en uppsättning behörigheter. Ibland kallas det helt enkelt för en *roll*. En rolldefinition listar de åtgärder som kan utföras, till exempel läsa, skriva och ta bort. Den kan också ange vilka åtgärder som inte kan utföras eller åtgärder relaterade till underliggande data. En roll definition har följande struktur:

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

`{action}` delen av en åtgärds sträng anger vilken typ av åtgärder som du kan utföra på en resurs typ. Till exempel visas följande del strängar i `{action}`:

| Åtgärds del sträng    | Beskrivning         |
| ------------------- | ------------------- |
| `*` | Jokertecknet beviljar åtkomst till alla åtgärder som matchar strängen. |
| `read` | Aktiverar Läs åtgärder (GET). |
| `write` | Aktiverar Skriv åtgärder (placering eller korrigering). |
| `action` | Aktiverar anpassade åtgärder som starta om virtuella datorer (POST). |
| `delete` | Aktiverar borttagnings åtgärder (ta bort). |

Här är roll definitionen [deltagare](built-in-roles.md#contributor) i JSON-format. Jokertecknet (`*`) under `Actions` anger att huvudnamnet som tilldelats den här rollen kan utföra alla åtgärder, eller med andra ord sköta all administration. Det här inbegriper även åtgärder som definieras i framtiden när Azure lägger till nya resurstyper. Åtgärderna under `NotActions` dras bort från `Actions`. För rollen [Deltagare](built-in-roles.md#contributor) tar `NotActions` bort rollens möjlighet att hantera åtkomsten till resurser och även att ge åtkomst till resurser.

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

## <a name="management-and-data-operations"></a>Hanterings-och data åtgärder

Rollbaserad åtkomst kontroll för hanterings åtgärder anges i `Actions` och `NotActions` egenskaper för en roll definition. Här följer några exempel på hanterings åtgärder i Azure:

- Hantera åtkomst till ett lagrings konto
- Skapa, uppdatera eller ta bort en BLOB-behållare
- Ta bort en resurs grupp och alla dess resurser

Hanterings åtkomst ärvs inte till dina data förutsatt att autentiseringsmetoden för behållar metoden är inställd på "Azure AD-användarkonto" och inte "åtkomst nyckel". Denna separation förhindrar att roller med jokertecken (`*`) får obegränsad åtkomst till dina data. Om en användare till exempel har en [läsar](built-in-roles.md#reader) roll för en prenumeration kan de Visa lagrings kontot, men som standard kan de inte Visa underliggande data.

Tidigare användes inte rollbaserad åtkomst kontroll för data åtgärder. Auktorisering för data åtgärder som varierar mellan olika resurs leverantörer. Samma rollbaserade auktoriserings modell för åtkomst kontroll som används för hanterings åtgärder har utökats till data åtgärder.

Nya data egenskaper har lagts till i roll definitions strukturen för att stödja data åtgärder. Dataåtgärder anges i egenskaperna `DataActions` och `NotDataActions`. Genom att lägga till dessa data egenskaper upprätthålls separationen mellan hantering och data. På så sätt kan du förhindra att rolltilldelningar med jokertecken (`*`) plötsligt får åtkomst till data. Här är några dataåtgärder du kan ange i `DataActions` och `NotDataActions`:

- Läsa en lista med blobar i en container
- Skriva till en lagringsblob i en container
- Ta bort ett meddelande i en kö

Här är roll definitionen [Storage BLOB data Reader](built-in-roles.md#storage-blob-data-reader) , som innehåller åtgärder i både `Actions`-och `DataActions` egenskaper. Med den här rollen kan du läsa BLOB-behållaren och även underliggande BLOB-data.

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

Du kan bara lägga till dataåtgärder i egenskaperna `DataActions` och `NotDataActions`. Resurs leverantörer identifierar vilka åtgärder som är data åtgärder genom att ange `isDataAction` egenskapen till `true`. Om du vill se en lista över åtgärder där `isDataAction` `true`, se [Resource Provider-åtgärder](resource-provider-operations.md). Roller som inte har data åtgärder krävs inte för att ha `DataActions` och `NotDataActions` egenskaper i roll definitionen.

Auktorisering för alla API-anrop för hanterings åtgärder hanteras av Azure Resource Manager. Auktorisering för API-anrop för data åtgärder hanteras av antingen en resurs leverantör eller Azure Resource Manager.

### <a name="data-operations-example"></a>Exempel på data åtgärder

Vi rekommenderar ett särskilt exempel för att bättre förstå hur hanterings-och data åtgärder fungerar. Alice har tilldelats [ägar](built-in-roles.md#owner) rollen i prenumerations omfånget. Bob har tilldelats rollen [Storage BLOB data Contributor](built-in-roles.md#storage-blob-data-contributor) i ett lagrings konto omfång. Följande diagram visar det här exemplet.

![Rollbaserad åtkomst kontroll har utökats för att stödja både hanterings-och data åtgärder](./media/role-definitions/rbac-management-data.png)

[Ägar](built-in-roles.md#owner) rollen för Alice och rollen [Storage BLOB data Contributor](built-in-roles.md#storage-blob-data-contributor) för Bob har följande åtgärder:

Ägare

&nbsp;&nbsp;&nbsp;&nbsp;åtgärder<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Storage BLOB data-deltagare

&nbsp;&nbsp;&nbsp;&nbsp;åtgärder<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Eftersom Alice har en jokertecken (`*`) i ett prenumerations omfång, ärver deras behörigheter nedåt för att göra det möjligt för dem att utföra alla hanterings åtgärder. Alice kan läsa, skriva och ta bort behållare. Alice kan dock inte utföra data åtgärder utan att vidta ytterligare åtgärder. Som standard kan Alice till exempel inte läsa blobarna i en behållare. För att läsa blobarna måste Alice Hämta lagrings åtkomst nycklar och använda dem för att få åtkomst till Blobbarna.

Bobs behörigheter är begränsade till bara `Actions` och `DataActions` som anges i rollen [Storage BLOB data Contributor](built-in-roles.md#storage-blob-data-contributor) . Med hjälp av rollen kan Bob utföra både hanterings-och data åtgärder. Bob kan till exempel läsa, skriva och ta bort behållare i det angivna lagrings kontot och kan också läsa, skriva och ta bort blobbar.

Mer information om hantering och data Plans säkerhet för lagring finns i [säkerhets guiden för Azure Storage](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Vilka verktyg stöder användning av RBAC för data åtgärder?

Om du vill visa och arbeta med data åtgärder måste du ha rätt versioner av verktygen eller SDK: erna:

| Verktyg  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 eller senare |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 eller senare |
| [Azure för .NET](/dotnet/azure/) | 2.8.0 – för hands version eller senare |
| [Azure SDK för Go](/azure/go/azure-sdk-go-install) | 15.0.0 eller senare |
| [Azure för Java](/java/azure/) | 1.9.0 eller senare |
| [Azure för python](/azure/python/) | 0.40.0 eller senare |
| [Azure SDK för Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 eller senare |

Om du vill visa och använda data åtgärder i REST API måste du ange parametern för **API-version** till följande version eller senare:

- 2018-07-01

## <a name="actions"></a>Åtgärder

`Actions` behörighet anger vilka hanterings åtgärder som rollen kan utföra. Det är en samling åtgärds strängar som identifierar skydds bara åtgärder hos Azure Resource providers. Här följer några exempel på hanterings åtgärder som kan användas i `Actions`.

| Åtgärds sträng    | Beskrivning         |
| ------------------- | ------------------- |
| `*/read` | Ger åtkomst till Läs åtgärder för alla resurs typer för alla Azure Resource providers.|
| `Microsoft.Compute/*` | Beviljar åtkomst till alla åtgärder för alla resurs typer i Microsoft. Compute Resource-providern.|
| `Microsoft.Network/*/read` | Ger åtkomst till Läs åtgärder för alla resurs typer i Microsoft. Network Resource-providern.|
| `Microsoft.Compute/virtualMachines/*` | Ger åtkomst till alla åtgärder för virtuella datorer och dess underordnade resurs typer.|
| `microsoft.web/sites/restart/Action` | Beviljar åtkomst för att starta om en webbapp.|

## <a name="notactions"></a>NotActions

`NotActions` behörigheten anger de hanterings åtgärder som är undantagna från den tillåtna `Actions`. Använd `NotActions` behörighet om den uppsättning åtgärder som du vill tillåta är enklare att definiera genom att utesluta begränsade åtgärder. Åtkomsten som beviljats av en roll (gällande behörigheter) beräknas genom att subtrahera `NotActions` åtgärder från `Actions` åtgärder.

> [!NOTE]
> Om en användare tilldelas en roll som undantar en åtgärd i `NotActions`och tilldelas en andra roll som beviljar åtkomst till samma åtgärd, tillåts användaren utföra åtgärden. `NotActions` är inte en neka-regel – det är bara ett bekvämt sätt att skapa en uppsättning tillåtna åtgärder när vissa åtgärder behöver undantas.
>

## <a name="dataactions"></a>DataActions

`DataActions` behörighet anger de data åtgärder som rollen kan utföra för dina data i objektet. Om en användare till exempel har Läs BLOB-dataåtkomst till ett lagrings konto kan de läsa blobarna i det lagrings kontot. Här följer några exempel på data åtgärder som kan användas i `DataActions`.

| Åtgärds sträng    | Beskrivning         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Returnerar en BLOB eller en lista över blobbar. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Returnerar resultatet av att skriva en blob. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Returnerar ett meddelande. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Returnerar ett meddelande eller resultatet av att skriva eller ta bort ett meddelande. |

## <a name="notdataactions"></a>NotDataActions

`NotDataActions` behörigheten anger de data åtgärder som undantas från tillåtna `DataActions`. Åtkomsten som beviljats av en roll (gällande behörigheter) beräknas genom att subtrahera `NotDataActions` åtgärder från `DataActions` åtgärder. Varje resurs leverantör tillhandahåller sin respektive uppsättning API: er för att uppfylla data åtgärder.

> [!NOTE]
> Om en användare tilldelas en roll som undantar en data åtgärd i `NotDataActions`och tilldelas en andra roll som beviljar åtkomst till samma data åtgärd, tillåts användaren att utföra denna data åtgärd. `NotDataActions` är inte en neka-regel – det är bara ett bekvämt sätt att skapa en uppsättning tillåtna data åtgärder när vissa data åtgärder måste undantas.
>

## <a name="assignablescopes"></a>AssignableScopes

Egenskapen `AssignableScopes` anger omfattningarna (hanterings grupper, prenumerationer, resurs grupper eller resurser) som har den här roll definitionen tillgänglig. Du kan bara göra rollen tillgänglig för tilldelning i de hanterings grupper, prenumerationer eller resurs grupper som kräver det. Du måste använda minst en hanterings grupp, en prenumeration, en resurs grupp eller ett resurs-ID.

Inbyggda roller har `AssignableScopes` angetts till rot omfånget (`"/"`). Rot omfånget indikerar att rollen är tillgänglig för tilldelning i alla omfattningar. Exempel på giltiga tilldelnings bara omfång är:

| Rollen är tillgänglig för tilldelning | Exempel |
|----------|---------|
| En prenumeration | `"/subscriptions/{subscriptionId1}"` |
| Två prenumerationer | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
| Nätverks resurs grupp | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
| En hanterings grupp | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
| Hanterings grupp och en prenumeration | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
| Alla omfattningar (gäller endast inbyggda roller) | `"/"` |

Information om `AssignableScopes` för anpassade roller finns i [anpassade roller för Azure-resurser](custom-roles.md).

## <a name="next-steps"></a>Nästa steg

* [Inbyggda roller för Azure-resurser](built-in-roles.md)
* [Anpassade roller för Azure-resurser](custom-roles.md)
* [Åtgärder för Azure Resource Manager Resource Provider](resource-provider-operations.md)
