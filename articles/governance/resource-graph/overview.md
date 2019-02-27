---
title: Översikt över Azure Resource Graph
description: Azure Resource Graph är en tjänst i Azure som möjliggör komplexa frågor till resurser i stor skala.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 31b9405b7d4141890d786ab3d1cb70d11ef0c1b3
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267962"
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

Det är viktigt att förstå att Azure Resource Graph-frågespråket baseras på [Kusto-frågespråket](../../data-explorer/data-explorer-overview.md) som används av Azure Data Explorer.

Information om åtgärder och funktioner som kan användas med Azure Resource Graph finns i [Frågespråket i Resource Graph](./concepts/query-language.md). Information om hur man söker efter resurser finns i [Utforska resurser](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Behörigheter i Azure Resource Graph

Om du vill använda Resource Graph måste du ha rätt behörighet i [Rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md) (RBAC) med minst läsbehörighet till de resurser som du vill fråga. Utan minst `read`-behörigheter för Azure-objektet eller -objektgruppen returneras inte resultat.

## <a name="throttling"></a>Begränsning

Frågor till Resource Graph begränsas för att tillhandahålla den bästa upplevelsen svarstiden till alla kunder. Om din organisation vill använda Resource Graph API för storskaliga och vanligt förekommande frågor använder du portal-”feedback” från Resource Graph-sidan. Se till att ange ditt affärsfall och markera kryssrutan ”Microsoft kan kontakta mig via e-post om min feedback” så att teamet kan kontakta dig.

## <a name="running-your-first-query"></a>Kör din första fråga

Resource Graph kan användas med både Azure CLI och Azure PowerShell. Frågan har samma struktur för båda språken. Lär dig hur du aktiverar Resource Graph i [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension) och [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Nästa steg

- Kör din första fråga med [Azure CLI](first-query-azurecli.md)
- Kör din första fråga med [Azure PowerShell](first-query-powershell.md)
- Börja med [startfrågor](./samples/starter.md)
- Förbättra din förståelse med [avancerade frågor](./samples/advanced.md)