---
title: Översikt över Azure Resource Graph
description: Förstå hur Azure Resource Graph-tjänsten kan komplexa frågor efter resurser i stor skala.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/30/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: d76a5b32403bd14f18181580f891925130808922
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622804"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Översikt över Azure Resource Graph-tjänsten

Azure Resource Graph är en tjänst i Azure som utformats för att utöka Azure Resurshantering genom att tillhandahålla effektiv och högpresterande resursutforskning med möjlighet att fråga i stor skala över alla prenumerationer och hanteringsgrupper så att du kan styra din miljö effektivt. De här frågorna innehåller följande funktioner:

- Möjlighet att fråga resurser med komplex filtrering, gruppering och sortering av resursegenskaper.
- Möjlighet att upprepade gånger utforska resurser baserat på styrningskrav och konvertera resulterande uttryck till en principdefinition.
- Möjlighet att utvärdera effekten av att tillämpa principer i en omfattande molnmiljö.
- Möjligheten att [redogör för ändringar som gjorts i resursegenskaper](./how-to/get-resource-changes.md) (förhandsversion).

I den här dokumentationen går vi igenom varje funktion i detalj.

> [!NOTE]
> Azure Resource Graph används av Azure portal nya Bläddra ”alla resurser” upplevelse och Azure Policy [ändringshistorik](../policy/how-to/determine-non-compliance.md#change-history-preview).
> _Visual diff_. Det är utformat för att hjälpa kunder att hantera storskaliga miljöer.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hur kompletterar Resource Graph Resurshantering i Azure

Azure Resource Manager skickar för närvarande data till en begränsad resurscache som tillgängliggör flera resursfält, särskilt då resursnamn, ID, typ, resursgrupp, prenumerationer och plats. Tidigare krävde arbete med olika resursegenskaper anrop till varje enskild resursprovider och egenskapsinformation om begäran för varje resurs.

Du kan använda Azure Resource Graph för att få åtkomst till dessa egenskaper, och resursprovidrarna returnerar utan att behöva göra enskilda anrop till varje enskild resursprovider. En lista över resurstyper som stöds, leta efter en **Ja** i den [resurser för distributioner av fullständig](../../azure-resource-manager/complete-mode-deletion.md) tabell.

Med Azure Resource Graph kan du:

- Komma åt egenskaperna som returneras av resursprovidrar utan att behöva göra enskilda anrop till varje resursprovider.
- Visa de senaste 14 dagarna av ändringshistorik som gjorts resursen för att se vad som ändrats egenskaper och när. (förhandsversion)

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

Frågor till Resource Graph begränsas för att tillhandahålla den bästa upplevelsen svarstiden till alla kunder. Om din organisation vill använda Resource Graph API för storskaliga och vanligt förekommande frågor använder du portal-”feedback” från Resource Graph-sidan. Se till att ange ditt affärsfall och markera kryssrutan ”Microsoft kan kontakta mig via e-post om min feedback” så att teamet kan kontakta dig.

## <a name="running-your-first-query"></a>Kör din första fråga

Resursen Graph har stöd för Azure CLI, Azure PowerShell och Azure SDK för .NET. Frågan är strukturerad detsamma för varje språk. Lär dig hur du aktiverar Resource Graph i [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension) och [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Nästa steg

- Kör din första fråga med [Azure CLI](first-query-azurecli.md).
- Kör din första fråga med [Azure PowerShell](first-query-powershell.md).
- Börja med [Starter frågor](./samples/starter.md).
- Hjälpa dig att förstå med [avancerade frågor](./samples/advanced.md).