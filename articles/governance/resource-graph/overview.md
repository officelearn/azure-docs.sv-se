---
title: Översikt över Azure Resource Graph
description: Förstå hur Azure Resource Graph-tjänsten möjliggör komplexa frågor om resurser i stor skala.
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/06/2019
ms.topic: overview
ms.service: resource-graph
ms.openlocfilehash: bf54f1a96c6be7bbfb19770472752b3f958695c4
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976806"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Översikt över Azure Resource Graph-tjänsten

Azure Resource Graph är en tjänst i Azure som är utformad för att utöka Azures resurs hantering genom att tillhandahålla effektiv och genomförd resurs utforskning med möjlighet att fråga i skala över en specifik uppsättning prenumerationer så att du effektivt kan styra din miljö. De här frågorna innehåller följande funktioner:

- Möjlighet att fråga resurser med komplex filtrering, gruppering och sortering av resursegenskaper.
- Möjlighet att utforska resurser på ett iterativt, baserat på styrnings krav.
- Möjlighet att utvärdera effekten av att tillämpa principer i en omfattande molnmiljö.
- Möjlighet att [göra detalj ändringar i resurs egenskaper](./how-to/get-resource-changes.md) (för hands version).

I den här dokumentationen går vi igenom varje funktion i detalj.

> [!NOTE]
> Azure Resource Graph-befogenheter Azure Portal Sök fältet, den nya webbläsaren "alla resurser" och Azure Policy [ändrings historiken](../policy/how-to/determine-non-compliance.md#change-history-preview)
> _Visual diff_. Den är utformad för att hjälpa kunder att hantera storskaliga miljöer.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hur kompletterar Resource Graph Resurshantering i Azure

Azure Resource Manager stöder för närvarande frågor över grundläggande resurs fält, specifikt resurs namn, ID, typ, resurs grupp, prenumeration och plats. Resource Manager tillhandahåller också anläggningar för att anropa enskilda resurs leverantörer för detaljerade egenskaper en resurs i taget.

Du kan använda Azure Resource Graph för att få åtkomst till dessa egenskaper, och resursprovidrarna returnerar utan att behöva göra enskilda anrop till varje enskild resursprovider. Om du vill ha en lista över resurs typer som stöds letar du upp **Ja** i tabellen [resurser för komplett läges distribution](../../azure-resource-manager/complete-mode-deletion.md) . Ett alternativt sätt att se resurs typer som stöds är via [schema läsaren i Azure Resource Graph Explorer](./first-query-portal.md#schema-browser).

Med Azure Resource Graph kan du:

- Få åtkomst till egenskaperna som returneras av resurs leverantörer utan att behöva göra enskilda anrop till varje resurs leverantör.
- Visa de senaste 14 dagarna i ändrings historiken som har gjorts till resursen för att se vilka egenskaper som har ändrats och när. (förhandsversion)

## <a name="how-resource-graph-is-kept-current"></a>Hur resurs diagram hålls aktuellt

När en Azure-resurs uppdateras, kommer resurs grafen att meddelas via resurs hanteraren för ändringen.
Resurs diagram uppdaterar sedan sin databas. Resurs diagram utför också en vanlig _fullständig genomsökning_. Den här genomsökningen säkerställer att resurs diagram data är aktuella om det finns uteblivna meddelanden eller när en resurs uppdateras utanför Resource Manager.

## <a name="the-query-language"></a>Frågespråket

Nu när du har en bättre förståelse för vad Azure Resource Graph är, ska vi ta en titt på hur du skapar frågor.

Det är viktigt att förstå att Azure Resource Graph-frågespråket baseras på [Kusto-frågespråket](../../data-explorer/data-explorer-overview.md) som används av Azure Data Explorer.

Information om åtgärder och funktioner som kan användas med Azure Resource Graph finns i [Frågespråket i Resource Graph](./concepts/query-language.md).
Information om hur man söker efter resurser finns i [Utforska resurser](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Behörigheter i Azure Resource Graph

Om du vill använda Resource Graph måste du ha rätt behörighet i [Rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md) (RBAC) med minst läsbehörighet till de resurser som du vill fråga. Utan minst `read`-behörigheter för Azure-objektet eller -objektgruppen returneras inte resultat.

> [!NOTE]
> Resurs diagram använder de prenumerationer som är tillgängliga för ett huvud konto under inloggningen. Om du vill se resurser för en ny prenumeration som lagts till under en aktiv session måste huvud kontot uppdatera kontexten. Den här åtgärden sker automatiskt när du loggar ut och in igen.

Azure CLI och Azure PowerShell använder prenumerationer som användaren har åtkomst till. När du använder REST API direkt tillhandahålls prenumerations listan av användaren. Om användaren har åtkomst till någon av prenumerationerna i listan returneras frågeresultaten för de prenumerationer som användaren har åtkomst till. Detta är samma sak som när du anropar [resurs grupper – lista](/rest/api/resources/resourcegroups/list) \- du får resurs grupper som du har åtkomst till utan att du behöver ange att resultatet kan vara delvis.
Om det inte finns några prenumerationer i prenumerations listan som användaren har rätt behörighet till är svaret _403_ (förbjudet).

## <a name="throttling"></a>Begränsning

Som en kostnads fri tjänst begränsas frågor till resurs diagram för att ge bästa möjliga upplevelse och svars tid för alla kunder. Om din organisation vill använda resurs Graph API för storskaliga och frekventa frågor använder du portalen feedback från [Portal sidan resurs diagram](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Ange ditt affärs ärende och markera kryss rutan Microsoft kan skicka e-post om din feedback för att teamet ska kontakta dig.

Resurs diagram begränsar frågor på användar nivå. Tjänst svaret innehåller följande HTTP-huvuden:

- `x-ms-user-quota-remaining` (int): Den återstående resurs kvoten för användaren. Det här värdet mappar till antal frågor.
- `x-ms-user-quota-resets-after` (hh: mm: SS): Tids åtgången tills en användares kvot förbrukning återställs

Mer information finns i [rikt linjer för begränsade begär Anden](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Kör din första fråga

Azure Resource Graph Explorer, som är en del av Azure Portal, aktiverar resurs diagram frågor direkt i Azure Portal. Fäst resultaten som dynamiska diagram för att tillhandahålla dynamisk information i real tid till ditt Portal arbets flöde. Mer information finns i [första frågan med Azure Resource Graph Explorer](first-query-portal.md).

Resurs diagram stöder Azure CLI, Azure PowerShell, Azure SDK för .NET och mer. Frågan är strukturerad samma för varje språk. Lär dig hur du aktiverar resurs diagram med:

- [Azure Portal och resurs diagram Utforskaren](first-query-portal.md) 
- [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module)

## <a name="next-steps"></a>Nästa steg

- Kör din första fråga med [Azure CLI](first-query-azurecli.md).
- Kör din första fråga med [Azure PowerShell](first-query-powershell.md).
- Börja med [Start frågor](./samples/starter.md).
- Förbättra din förståelse med [avancerade frågor](./samples/advanced.md).