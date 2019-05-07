---
title: Översikt över Azure Resource Graph
description: Förstå hur Azure Resource Graph-tjänsten kan komplexa frågor efter resurser i stor skala.
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 45d5cf7c4235d10e136cc96364d52aa4319bbf79
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65137780"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Översikt över Azure Resource Graph-tjänsten

Azure Resource Graph är en tjänst i Azure som utformats för att utöka Azure Resource Management genom att tillhandahålla effektiv och högpresterande resource undersökning möjlighet att fråga i stor skala över en viss uppsättning prenumerationer så att du effektivt kan styra din miljö. De här frågorna innehåller följande funktioner:

- Möjlighet att fråga resurser med komplex filtrering, gruppering och sortering av resursegenskaper.
- Möjlighet att utforska resurser baserat på styrningskrav upprepade gånger.
- Möjlighet att utvärdera effekten av att tillämpa principer i en omfattande molnmiljö.
- Möjligheten att [redogör för ändringar som gjorts i resursegenskaper](./how-to/get-resource-changes.md) (förhandsversion).

I den här dokumentationen går vi igenom varje funktion i detalj.

> [!NOTE]
> Azure Resource Graph Driver sökfältet i Azure portal, ny Bläddra ”alla resurser” upplevelse och Azure Policy [ändringshistorik](../policy/how-to/determine-non-compliance.md#change-history-preview)
> _visual diff_. Det är utformat för att hjälpa kunder att hantera storskaliga miljöer.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hur kompletterar Resource Graph Resurshantering i Azure

Azure Resource Manager stöder för närvarande frågor över grundläggande fält, särskilt - resurs namn, ID, typ, resursgrupp, prenumeration och plats. Resource Manager finns också funktioner för att anropa enskilda resursprovidrar för detaljerade egenskaper en resurs åt gången.

Du kan använda Azure Resource Graph för att få åtkomst till dessa egenskaper, och resursprovidrarna returnerar utan att behöva göra enskilda anrop till varje enskild resursprovider. En lista över resurstyper som stöds, leta efter en **Ja** i den [resurser för distributioner av fullständig](../../azure-resource-manager/complete-mode-deletion.md) tabell.

Med Azure Resource Graph kan du:

- Komma åt egenskaperna som returneras av resursprovidrar utan att behöva göra enskilda anrop till varje resursprovider.
- Visa de senaste 14 dagarna av ändringshistorik som gjorts resursen för att se vad som ändrats egenskaper och när. (förhandsversion)

## <a name="how-resource-graph-is-kept-current"></a>Hur Resource Graph uppdateras

När en Azure-resurs uppdateras meddelas Resource Graph av Resource Manager för ändringen.
Resursen Graph uppdateras sedan databasen. Resursen Graph har också en vanlig _fullständig genomsökning_. Skanningen garanterar att resursen diagramdata är aktuella vid missade meddelanden eller när en resurs uppdateras utanför Resource Manager.

## <a name="the-query-language"></a>Frågespråket

Nu när du har en bättre förståelse för vad Azure Resource Graph är för något, så låt oss ta en titt på hur man konstruerar frågor.

Det är viktigt att förstå att Azure Resource Graph-frågespråket baseras på [Kusto-frågespråket](../../data-explorer/data-explorer-overview.md) som används av Azure Data Explorer.

Information om åtgärder och funktioner som kan användas med Azure Resource Graph finns i [Frågespråket i Resource Graph](./concepts/query-language.md).
Information om hur man söker efter resurser finns i [Utforska resurser](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Behörigheter i Azure Resource Graph

Om du vill använda Resource Graph måste du ha rätt behörighet i [Rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md) (RBAC) med minst läsbehörighet till de resurser som du vill fråga. Utan minst `read`-behörigheter för Azure-objektet eller -objektgruppen returneras inte resultat.

> [!NOTE]
> Resursen används prenumerationer som är tillgängliga för ett huvudnamn under inloggningen. Om du vill se resurser till en ny prenumeration som har lagts till under en aktiv session, måste huvudkontot som uppdatera kontexten. Den här åtgärden sker automatiskt när logga ut och in igen.

## <a name="throttling"></a>Begränsning

Som en kostnadsfri tjänst begränsas frågor till resursen Graph för att tillhandahålla den bästa tidpunkten för upplevelse och svar för alla kunder. Om din organisation vill använda Graph-API-resurs för storskaliga och vanliga frågor, kan du använda portalen ”Feedback” från sidan Resource Graph. Se till att ange ditt affärsfall och markera kryssrutan ”Microsoft kan kontakta mig via e-post om min feedback” så att teamet kan kontakta dig.

Resursen Graph begränsar på klientnivån. Tjänsten åsidosätter och anger den `x-ms-ratelimit-remaining-tenant-reads` svarshuvud att ange återstående frågar tillgängliga av användare i klienten. Resursen Graph återställer kvoten var femte sekund i stället för varje timme. Mer information finns i [begränsning resurshanteraren begär](../../azure-resource-manager/resource-manager-request-limits.md).

## <a name="running-your-first-query"></a>Kör din första fråga

Resursen Graph har stöd för Azure CLI, Azure PowerShell och Azure SDK för .NET. Frågan är strukturerad detsamma för varje språk. Lär dig hur du aktiverar Resource Graph i [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension) och [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Nästa steg

- Kör din första fråga med [Azure CLI](first-query-azurecli.md).
- Kör din första fråga med [Azure PowerShell](first-query-powershell.md).
- Börja med [Starter frågor](./samples/starter.md).
- Hjälpa dig att förstå med [avancerade frågor](./samples/advanced.md).