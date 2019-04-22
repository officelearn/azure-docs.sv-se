---
title: Förstå rolldefinitioner i RBAC för Azure-resurser | Microsoft Docs
description: Läs mer om rolldefinitioner med rollbaserad åtkomstkontroll (RBAC) för detaljerad åtkomsthantering för Azure-resurser.
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
ms.date: 02/09/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 7855c2bd45ba35ecb0ede5c60268e6446f37ed5a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58804538"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Förstå rolldefinitioner för Azure-resurser

Om du vill förstå hur en roll fungerar eller om du skapar dina egna [anpassad roll för Azure-resurser](custom-roles.md), är det bra att förstå hur roller definieras. Den här artikeln innehåller information om rolldefinitioner och innehåller några exempel.

## <a name="role-definition-structure"></a>Roll-definitionsstruktur

En *rolldefinition* är en uppsättning behörigheter. Ibland kallas det helt enkelt för en *roll*. En rolldefinition listar de åtgärder som kan utföras, till exempel läsa, skriva och ta bort. Det kan också lista de åtgärder som inte kan utföras eller åtgärder som rör underliggande data. En rolldefinition har följande struktur:

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

Den `{action}` delen av en sträng med åtgärden anger vilken typ av åtgärder du kan utföra på en resurstyp. Till exempel du ser följande delsträngar i `{action}`:

| Åtgärden delsträng    | Beskrivning         |
| ------------------- | ------------------- |
| `*` | Jokertecknet ger åtkomst till alla åtgärder som matchar strängen. |
| `read` | Aktiverar läsåtgärder (GET). |
| `write` | Aktiverar skrivåtgärder (PUT, POST och korrigering). |
| `delete` | Aktiverar borttagningsåtgärder (ta bort). |

Här är den [deltagare](built-in-roles.md#contributor) rolldefinition i JSON-format. Jokertecknet (`*`) åtgärden under `Actions` betyder att det huvudkonto som tilldelats den här rollen kan utföra alla åtgärder eller med andra ord, den kan hantera allt. Detta omfattar åtgärder som definierats i framtiden, som Azure lägger till nya resurstyper. Åtgärder under `NotActions` subtraheras från `Actions`. I fall med den [deltagare](built-in-roles.md#contributor) roll, `NotActions` tar bort den här rollen möjlighet att hantera åtkomst till resurser och också tilldela åtkomst till resurser.

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

## <a name="management-and-data-operations-preview"></a>Åtgärder för hantering och data (förhandsversion)

Rollbaserad åtkomstkontroll för hanteringsåtgärder har angetts i den `Actions` och `NotActions` egenskaperna för en rolldefinition. Här följer några exempel på åtgärder i Azure:

- Hantera åtkomst till ett lagringskonto
- Skapa, uppdatera eller ta bort en blob-behållare
- Ta bort en resursgrupp och alla dess resurser

Hanteringsåtkomst ärvs inte till dina data. Den här separationen förhindrar roller med jokertecken (`*`) från har obegränsad åtkomst till dina data. Exempel: om en användare har en [läsare](built-in-roles.md#reader) rollen på en prenumeration kan de visa storage-konto, men de kan inte visa underliggande data som standard.

Tidigare användes inte rollbaserad åtkomstkontroll för dataåtgärder. Auktorisering för dataåtgärder varierar över resursprovidrar. Samma rollbaserad auktoriseringsmodellen för åtkomstkontroll används för hanteringsåtgärder har utökats till dataåtgärder (för närvarande i förhandsversion).

Egenskaper för nya data har lagts till definitionsstruktur roll för att stödja åtgärder. Dataåtgärder anges i den `DataActions` och `NotDataActions` egenskaper. Genom att lägga till dessa egenskaper för data, underhålls avgränsningen mellan hanterings- och data. Detta förhindrar aktuella rolltilldelningar med jokertecken (`*`) från plötsligt att ha åtkomst till data. Här följer några dataåtgärder som kan anges i `DataActions` och `NotDataActions`:

- Läs en lista över blobarna i en behållare
- Skriv en lagringsblob i en behållare
- Ta bort ett meddelande i en kö

Här är den [Storage Blob Data-läsare](built-in-roles.md#storage-blob-data-reader) rolldefinition som innehåller åtgärder i både den `Actions` och `DataActions` egenskaper. Den här rollen kan du läsa blob-behållaren och den underliggande blob-data.

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

Åtgärder för endast kan läggas till i `DataActions` och `NotDataActions` egenskaper. Resursproviders identifiera vilka åtgärder som är dataåtgärder genom att ange den `isDataAction` egenskap `true`. Se en lista över åtgärderna där `isDataAction` är `true`, se [åtgärder för resursprovider](resource-provider-operations.md). Roller som inte har dataåtgärder inte måste ha `DataActions` och `NotDataActions` egenskaper i rolldefinitionen.

Auktorisering för alla management åtgärden API-anrop hanteras av Azure Resource Manager. Auktorisering för API-anrop för data åtgärden hanteras av en provider för nätverksresurser eller Azure Resource Manager.

### <a name="data-operations-example"></a>Exempel på åtgärder

För att bättre förstå hur åtgärder för hantering och data fungerar kan du anta att du ett exempel. Alice har tilldelats den [ägare](built-in-roles.md#owner) rollen prenumerationsområde. Bob har tilldelats den [Storage Blob Data-deltagare](built-in-roles.md#storage-blob-data-contributor) roll i ett omfång för storage-konto. Följande diagram visar det här exemplet.

![Rollbaserad åtkomstkontroll utökats och stöd för både hantering och åtgärder](./media/role-definitions/rbac-management-data.png)

Den [ägare](built-in-roles.md#owner) rollen för Alice och [Storage Blob Data-deltagare](built-in-roles.md#storage-blob-data-contributor) rollen för Bob har följande åtgärder:

Ägare

&nbsp;&nbsp;&nbsp;&nbsp;Åtgärder<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Storage Blob Data-deltagare

&nbsp;&nbsp;&nbsp;&nbsp;Åtgärder<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Eftersom Alice har ett jokertecken (`*`) åtgärden en prenumerationsomfånget ärver behörigheter ned för att aktivera henne att utföra alla hanteringsåtgärder. Alice kan läsa, skriva och ta bort behållare. Alice kan inte utföra dataåtgärder utan att utföra ytterligare steg. Som standard kan till exempel Alice läsa blobarna i en behållare. Om du vill läsa blobarna måste Alice hämta åtkomstnycklar för lagring och använda dem för att komma åt blobarna.

Bobs behörigheter är begränsade till bara den `Actions` och `DataActions` anges i den [Storage Blob Data-deltagare](built-in-roles.md#storage-blob-data-contributor) roll. Baserat på vilken roll, kan Bob utföra både hantering och åtgärder. Till exempel Bob kan läsa, skriva och ta bort behållare i det angivna lagringskontot och han kan också läsa, skriva och ta bort blobar.

Mer information om hantering och datasäkerhet plan för lagring finns i den [säkerhetsguiden för Azure Storage](../storage/common/storage-security-guide.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Vilka verktyg stöd med rollbaserad Åtkomstkontroll i dataåtgärder?

Om du vill visa och arbeta med dataåtgärder, måste du ha rätt versioner av verktyg och SDK: er:

| Verktyg  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 eller senare |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 eller senare |
| [Azure för .NET](/dotnet/azure/) | 2.8.0-Preview eller senare |
| [Azure SDK för Go](/go/azure/azure-sdk-go-install) | 15.0.0 eller senare |
| [Azure för Java](/java/azure/) | 1.9.0 eller senare |
| [Azure för Python](/python/azure) | 0.40.0 eller senare |
| [Azure SDK för Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 eller senare |

Om du vill visa och använda dataåtgärderna i REST-API, måste du ställa in den **api-versionen** parametern till följande version eller senare:

- 2018-01-01-preview

Azure-portalen tillåter även användare att söka efter och hantera innehållet i köer och Blob förhandsgranskningsupplevelsen för behållare via Azure AD. Att visa och hantera innehållet i en kö eller Blob-behållare klickar du på den **utforska data med hjälp av Azure AD-preview** för lagringskontot Översikt.

![Utforska köer och Blob-behållare med hjälp av Azure AD-förhandsversion](./media/role-definitions/rbac-dataactions-browsing.png)

## <a name="actions"></a>Åtgärder

Den `Actions` behörighet anger vilka hanteringsåtgärder som gör att rollen som ska utföras. Det är en samling med åtgärden strängar som identifierar skyddbara driften av Azure resource-leverantörer. Här följer några exempel på åtgärder som kan användas i `Actions`.

| Åtgärden sträng    | Beskrivning         |
| ------------------- | ------------------- |
| `*/read` | Beviljar åtkomst till Läs-och skrivåtgärder för alla typer av resurser för alla Azure-resurs-providers.|
| `Microsoft.Compute/*` | Beviljar åtkomst till alla åtgärder för alla typer av resurser i Microsoft.Compute-resursprovidern.|
| `Microsoft.Network/*/read` | Beviljar åtkomst till Läs-och skrivåtgärder för alla typer av resurser i Microsoft.Network-resursprovidern.|
| `Microsoft.Compute/virtualMachines/*` | Beviljar åtkomst till alla åtgärder för virtuella datorer och dess underordnade typer av resurser.|
| `microsoft.web/sites/restart/Action` | Beviljar åtkomst till att starta om en webbapp.|

## <a name="notactions"></a>NotActions

Den `NotActions` behörighet anger vilka hanteringsåtgärder som är undantagna från den tillåtna `Actions`. Använd den `NotActions` behörighet om uppsättning åtgärder som du vill tillåta definieras enklare genom att exkludera vissa åtgärder. Åtkomst har beviljats av en roll (gällande behörigheter) beräknas genom att subtrahera den `NotActions` åtgärder från den `Actions` åtgärder.

> [!NOTE]
> Om en användare har tilldelats en roll som inte omfattar en åtgärd i `NotActions`, och har tilldelats en andra roll som ger åtkomst till samma åtgärd som användaren har tillåtelse att utföra åtgärden. `NotActions` är inte en neka-regeln – det är bara ett praktiskt sätt att skapa en uppsättning tillåtna åtgärder när specifika åtgärder som ska undantas.
>

## <a name="dataactions-preview"></a>dataActions (förhandsversion)

Den `DataActions` behörighet anger dataåtgärder som gör att rollen som ska utföras för dina data inom objektet. Till exempel om en användare har läs-åtkomst för blob-data till ett lagringskonto, kan de läsa blobbar i det lagringskontot. Här följer några exempel på åtgärder som kan användas i `DataActions`.

| Åtgärden sträng    | Beskrivning         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Returnerar en blob eller bloblista. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Returnerar resultatet av att skriva en blob. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Returnerar ett meddelande. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Returnerar ett meddelande eller resultatet av att skriva eller ta bort ett meddelande. |

## <a name="notdataactions-preview"></a>notDataActions (förhandsversion)

Den `NotDataActions` behörighet anger de åtgärder som är undantagna från den tillåtna `DataActions`. Åtkomst har beviljats av en roll (gällande behörigheter) beräknas genom att subtrahera den `NotDataActions` åtgärder från den `DataActions` åtgärder. Varje resursprovider ger dess respektive API: er för att utföra dataåtgärder.

> [!NOTE]
> Om en användare har tilldelats en roll som inte omfattar en dataåtgärd i `NotDataActions`, och har tilldelats en andra roll som ger åtkomst till samma dataåtgärd tillåts användaren att utföra åtgärden data. `NotDataActions` är inte en neka-regeln – det är bara ett praktiskt sätt att skapa en uppsättning tillåtna åtgärder när specifika dataåtgärder som ska undantas.
>

## <a name="assignablescopes"></a>assignableScopes

Den `AssignableScopes` egenskap anger scope (prenumerationer, resursgrupper och resurser) att rollen är tillgänglig för tilldelning. Du kan göra rollen tilldelas i endast prenumerationer eller resursgrupper som kräver det, och inte oreda användarens upplevelse för resten av prenumerationer och resursgrupper. Du måste använda minst en prenumeration, resursgrupp eller resurs-ID.

Inbyggda roller har `AssignableScopes` inställd rotscopet (`"/"`). Rotscopet anger att rollen är tillgängliga för tilldelning i alla omfattningar. Exempel på giltiga tilldelningsbara omfång:

| Scenario | Exempel |
|----------|---------|
| Rollen är tillgängliga för tilldelning i en enda prenumeration | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Rollen är tillgängliga för tilldelning i två prenumerationer | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Rollen är tillgängliga för tilldelning endast i Nätverksresursgruppen | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Rollen är tillgängliga för tilldelning i alla omfattningar | `"/"` |

Information om `AssignableScopes` anpassade roller finns [anpassade roller för Azure-resurser](custom-roles.md).

## <a name="next-steps"></a>Nästa steg

* [Inbyggda roller för Azure-resurser](built-in-roles.md)
* [Anpassade roller för Azure-resurser](custom-roles.md)
* [Azure Resource Manager åtgärder för resursprovider](resource-provider-operations.md)
