---
title: Översikt över Azure Resource Graph
description: Azure Resource Graph är en tjänst i Azure som möjliggör komplexa frågor till resurser i stor skala.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: d68183f4d0a928ac72f3f73ea5225ad174820cb7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162108"
---
# <a name="what-is-azure-resource-graph"></a>Vad är Azure Resource Graph

Azure Resource Graph är en tjänst i Azure som utformats för att utöka Azure Resurshantering genom att tillhandahålla effektiv och högpresterande resursutforskning med möjlighet att fråga i stor skala över alla prenumerationer och hanteringsgrupper så att du kan styra din miljö effektivt. De här frågorna tillhandahåller följande funktioner:

- Möjlighet att fråga resurser med komplex filtrering, gruppering och sortering av resursegenskaper.
- Möjlighet att upprepade gånger utforska resurser baserat på styrningskrav och konvertera resulterande uttryck till en principdefinition.
- Möjlighet att utvärdera effekten av att tillämpa principer i en omfattande molnmiljö.

I den här dokumentationen går vi igenom varje funktion i detalj.

> [!NOTE]
> Azure Resource Graph används av den nya bläddringsfunktionen för alla resurser i Azure Portal. Den har utformats för att hjälpa kunder som har behov av att hantera storskaliga miljöer.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hur kompletterar Resource Graph Resurshantering i Azure

Azure Resurshantering skickar för närvarande data till en begränsad resurscache som visar flera resursfält, särskilt då resursnamn, ID, typ, resursgrupp, prenumerationer och plats. Om du vill arbeta med fler resursegenskaper idag måste du anropa varje enskild resursprovider och begära egenskapsinformation om varje enskild resurs.

Du kan använda Azure Resource Graph för att få åtkomst till dessa egenskaper, och resursprovidrarna returnerar utan att behöva göra enskilda anrop till varje enskild resursprovider.

## <a name="the-query-language"></a>Frågespråket

Nu när du har en bättre förståelse för vad Azure Resource Graph är för något, så låt oss ta en titt på hur man konstruerar frågor.

Det är viktigt att förstå att Azure Resource Graph-frågespråket baseras på [frågespråket i Azure Data Explorer](../../data-explorer/data-explorer-overview.md).

Information om åtgärder och funktioner som kan användas med Azure Resource Graph finns i [Frågespråket i Resource Graph](./concepts/query-language.md). Information om hur man söker efter resurser finns i [Utforska resurser](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Behörigheter i Azure Resource Graph

Om du vill använda Resource Graph måste du ha behörighet via [rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md) (RBAC) med minst läsbehörighet till de resurser som du vill fråga. Om du inte har `read`-behörigheter för hanteringsgrupp, prenumeration, resursgrupp eller enskild resurs returneras inget resultat till Resource Graph-frågan.

## <a name="running-your-first-query"></a>Kör din första fråga

Resource Graph kan användas med både Azure CLI och Azure PowerShell. Frågekomponenten är strukturerad likadant oavsett vilket språk som används. Stöd för Azure Resource Graph är ännu inte tillgängligt som standard i någondera SDK, så ett tillägg eller en modul måste läsas in för att du ska ha tillgång till de nödvändiga kommandona.
Lär dig hur du aktiverar Resource Graph i [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension) och [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Nästa steg

- Kör din första fråga med [Azure CLI](first-query-azurecli.md)
- Kör din första fråga med [Azure PowerShell](first-query-powershell.md)
- Börja med [startfrågor](./samples/starter.md)
- Förbättra din förståelse med [avancerade frågor](./samples/advanced.md)