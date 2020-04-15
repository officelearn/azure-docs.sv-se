---
title: Översikt över Azure Resource Graph
description: Förstå hur Azure Resource Graph-tjänsten möjliggör komplexa frågor om resurser i stor skala över prenumerationer och klienter.
ms.date: 03/02/2020
ms.topic: overview
ms.openlocfilehash: f5c091f60faedb76e3ca6cd68505c06f51be21b6
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381520"
---
# <a name="what-is-azure-resource-graph"></a>Vad är Azure Resource Graph?

Azure Resource Graph är en tjänst i Azure som är utformad för att utöka Azure Resource Management genom att tillhandahålla effektiv och högpresterande resursutforskning med möjlighet att fråga i stor skala över en viss uppsättning prenumerationer så att du effektivt kan styra din miljö. De här frågorna innehåller följande funktioner:

- Möjlighet att fråga resurser med komplex filtrering, gruppering och sortering av resursegenskaper.
- Förmåga att iterativt utforska resurser baserat på styrningskrav.
- Möjlighet att utvärdera effekten av att tillämpa principer i en omfattande molnmiljö.
- Möjlighet att [detalj ändringar som gjorts i resursegenskaper](./how-to/get-resource-changes.md) (förhandsgranskning).

I den här dokumentationen går vi igenom varje funktion i detalj.

> [!NOTE]
> Azure Resource Graph driver Azure-portalens sökfält, den nya bläddra bland "Alla resurser" erfarenhet och Azure [Policy's Change historia](../policy/how-to/determine-non-compliance.md#change-history-preview)
> _visuella diff_. Den är utformad för att hjälpa kunderna att hantera storskaliga miljöer.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hur kompletterar Resource Graph Resurshantering i Azure

Azure Resource Manager stöder för närvarande frågor över grundläggande resursfält, särskilt - Resursnamn, ID, Typ, Resursgrupp, Prenumeration och Plats. Resource Manager tillhandahåller också faciliteter för att anropa enskilda resursleverantörer för detaljerade egenskaper en resurs i taget.

Du kan använda Azure Resource Graph för att få åtkomst till dessa egenskaper, och resursprovidrarna returnerar utan att behöva göra enskilda anrop till varje enskild resursprovider. En lista över resurstyper som stöds finns i [referensen för tabellen och resurstypen](./reference/supported-tables-resources.md). Ett alternativt sätt att se resurstyper som stöds är via [azure resource graph explorer-schemabläddraren](./first-query-portal.md#schema-browser).

Med Azure Resource Graph kan du:

- Få tillgång till de egenskaper som returneras av resursleverantörer utan att behöva ringa enskilda samtal till varje resursprovider.
- Visa de senaste 14 dagarnas ändringshistorik som gjorts för resursen för att se vilka egenskaper som har ändrats och när. (förhandsversion)

## <a name="how-resource-graph-is-kept-current"></a>Så här hålls Resursdiagrammet aktuellt

När en Azure-resurs uppdateras meddelas Resource Graph av Resource Manager om ändringen.
Resource Graph uppdaterar sedan sin databas. Resource Graph gör också en vanlig _fullständig genomsökning_. Den här genomsökningen säkerställer att Resource Graph-data är aktuella om det finns missade meddelanden eller när en resurs uppdateras utanför Resource Manager.

> [!NOTE]
> Resource Graph `GET` använder ett till det senaste API:et som inte förhandsgranskas för varje resursprovider för att samla in egenskaper och värden. Det kan leda till att den förväntade egenskapen inte är tillgänglig. I vissa fall har API-versionen som används åsidosatts för att tillhandahålla mer aktuella eller allmänt använda egenskaper i resultaten. Se exempel på [Visa API-version för varje resurstyp](./samples/advanced.md#apiversion) för en fullständig lista i din miljö.

## <a name="the-query-language"></a>Frågespråket

Nu när du har en bättre förståelse för vad Azure Resource Graph är, låt oss dyka in i hur du konstruerar frågor.

Det är viktigt att förstå att Azure Resource Graph-frågespråket baseras på [Kusto-frågespråket](/azure/data-explorer/data-explorer-overview) som används av Azure Data Explorer.

Information om åtgärder och funktioner som kan användas med Azure Resource Graph finns i [Frågespråket i Resource Graph](./concepts/query-language.md).
Information om hur man söker efter resurser finns i [Utforska resurser](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Behörigheter i Azure Resource Graph

Om du vill använda Resource Graph måste du ha rätt behörighet i [Rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md) (RBAC) med minst läsbehörighet till de resurser som du vill fråga. Utan minst `read`-behörigheter för Azure-objektet eller -objektgruppen returneras inte resultat.

> [!NOTE]
> Resource Graph använder de prenumerationer som är tillgängliga för ett huvudnamn under inloggningen. Om du vill se resurser för en ny prenumeration som lagts till under en aktiv session måste huvudmannen uppdatera kontexten. Den här åtgärden inträffar automatiskt när du loggar ut och in igen.

Azure CLI och Azure PowerShell använder prenumerationer som användaren har åtkomst till. När du använder REST API direkt tillhandahålls prenumerationslistan av användaren. Om användaren har åtkomst till någon av prenumerationerna i listan returneras frågeresultaten för de prenumerationer som användaren har åtkomst till. Det här beteendet är detsamma som när du [anropar resursgrupper – Lista](/rest/api/resources/resourcegroups/list) \- får du resursgrupper som du har åtkomst till utan någon indikation på att resultatet kan vara partiellt.
Om det inte finns några prenumerationer i prenumerationslistan som användaren har lämpliga rättigheter till är svaret en _403_ (Forbidden).

## <a name="throttling"></a>Begränsning

Som en kostnadsfri tjänst begränsas frågor till Resource Graph för att ge bästa möjliga upplevelse- och svarstid för alla kunder. Om din organisation vill använda API:et för resursdiagram för storskaliga och frekventa frågor använder du portalen Feedback från [portalsidan för Resursdiagram](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Ange ditt affärsfall och välj kryssrutan "Microsoft kan skicka e-post till dig om din feedback" så att teamet kan kontakta dig.

Resursdiagram begränsar frågor på användarnivå. Servicesvaret innehåller följande HTTP-huvuden:

- `x-ms-user-quota-remaining`(int): Den återstående resurskvoten för användaren. Det här värdet mappar till frågeantal.
- `x-ms-user-quota-resets-after`(hh:mm:ss): Tidslängden tills en användares kvotförbrukning återställs

Mer information finns i [Vägledning för begränsade begäranden](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Kör din första fråga

Azure Resource Graph Explorer, en del av Azure-portalen, gör det möjligt att köra Resource Graph-frågor direkt i Azure-portalen. Fäst resultaten som dynamiska diagram för att ge dynamisk information i realtid till portalarbetsflödet. Mer information finns i [Första frågan med Azure Resource Graph Explorer](first-query-portal.md).

Resource Graph stöder Azure CLI, Azure PowerShell, Azure SDK för .NET med mera. Frågan är strukturerad på samma sätt för varje språk. Lär dig hur du aktiverar Resursdiagram med:

- [Utforskaren för Azure-portal och Resursdiagram](first-query-portal.md) 
- [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module)

## <a name="next-steps"></a>Nästa steg

- Kör din första fråga med hjälp av [Azure-portalen](first-query-portal.md).
- Kör din första fråga med [Azure CLI](first-query-azurecli.md).
- Kör din första fråga med [Azure PowerShell](first-query-powershell.md).