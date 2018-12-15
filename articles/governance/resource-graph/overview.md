---
title: Översikt över Azure Resource Graph
description: Azure Resource Graph är en tjänst i Azure som möjliggör komplexa frågor till resurser i stor skala.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/22/2018
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: ed64f6317fefb9e82dbe14e806499965d926d434
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316207"
---
# <a name="what-is-azure-resource-graph"></a>Vad är Azure Resource Graph

Azure Resource Graph är en tjänst i Azure som utformats för att utöka Azure Resurshantering genom att tillhandahålla effektiv och högpresterande resursutforskning med möjlighet att fråga i stor skala över alla prenumerationer och hanteringsgrupper så att du kan styra din miljö effektivt. De här frågorna innehåller följande funktioner:

- Möjlighet att fråga resurser med komplex filtrering, gruppering och sortering av resursegenskaper.
- Möjlighet att upprepade gånger utforska resurser baserat på styrningskrav och konvertera resulterande uttryck till en principdefinition.
- Möjlighet att utvärdera effekten av att tillämpa principer i en omfattande molnmiljö.

I den här dokumentationen går vi igenom varje funktion i detalj.

> [!NOTE]
> Azure Resource Graph används av den nya bläddringsfunktionen för alla resurser i Azure Portal. Den har utformats för att hjälpa kunder som har behov av att hantera storskaliga miljöer.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hur kompletterar Resource Graph Resurshantering i Azure

Azure Resource Manager skickar för närvarande data till en begränsad resurscache som tillgängliggör flera resursfält, särskilt då resursnamn, ID, typ, resursgrupp, prenumerationer och plats. Tidigare krävde arbete med olika resursegenskaper anrop till varje enskild resursprovider och egenskapsinformation om begäran för varje resurs.

Du kan använda Azure Resource Graph för att få åtkomst till dessa egenskaper, och resursprovidrarna returnerar utan att behöva göra enskilda anrop till varje enskild resursprovider.

## <a name="the-query-language"></a>Frågespråket

Nu när du har en bättre förståelse för vad Azure Resource Graph är för något, så låt oss ta en titt på hur man konstruerar frågor.

Det är viktigt att förstå att Azure Resource Graph-frågespråket baseras på [frågespråket i Azure Data Explorer](../../data-explorer/data-explorer-overview.md).

Information om åtgärder och funktioner som kan användas med Azure Resource Graph finns i [Frågespråket i Resource Graph](./concepts/query-language.md). Information om hur man söker efter resurser finns i [Utforska resurser](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Behörigheter i Azure Resource Graph

Om du vill använda Resource Graph måste du ha rätt behörighet i [Rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md) (RBAC) med minst läsbehörighet till de resurser som du vill fråga. Utan minst `read`-behörigheter för Azure-objektet eller -objektgruppen returneras inte resultat.

## <a name="running-your-first-query"></a>Kör din första fråga

Resource Graph kan användas med både Azure CLI och Azure PowerShell. Frågan har samma struktur för båda språken. Lär dig hur du aktiverar Resource Graph i [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension) och [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Nästa steg

- Kör din första fråga med [Azure CLI](first-query-azurecli.md)
- Kör din första fråga med [Azure PowerShell](first-query-powershell.md)
- Börja med [startfrågor](./samples/starter.md)
- Förbättra din förståelse med [avancerade frågor](./samples/advanced.md)