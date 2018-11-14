---
title: Arbeta med index sökvägar i Azure Cosmos DB
description: Översikt över index sökvägar i Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: 0515397fb9ab0f05b4c763a2b05e9d986960bd91
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629298"
---
# <a name="index-paths-in-azure-cosmos-db"></a>Sökvägar för index i Azure Cosmos DB

Genom att använda index sökvägar i Azure Cosmos DB kan välja du att inkludera eller exkludera en specifik sökväg från indexering. Välja specifika sökvägar erbjuder förbättrade skrivprestanda och lagring med lägre index för scenarier där du vet att frågemönstren. Indexera sökvägar start med rot (`/`) jokertecken operator och vanligtvis slutar inte med den `?` jokertecken operator. Det här mönstret anger att det finns flera möjliga värden för prefixet. Till exempel för att hantera frågan `SELECT * FROM Families F WHERE F.familyName = "Andersen"`, måste du inkludera ett index sökvägen för `/familyName/?` i behållarens index principen.

Index sökvägar kan också använda den `*` jokertecken operatör att ange beteendet för sökvägar rekursivt under prefixet. Till exempel `/payload/*` kan användas för att undanta allt under egenskapen nyttolast från indexering.

## <a name="common-patterns-for-index-paths"></a>Vanliga mönster för index sökvägar

Här följer vanliga mönster för att ange index sökvägar:

| **Sökväg** | **Beskrivning/användningsfall** |
| ---------- | ------- |
| /   | Standardsökvägen för samlingen. Rekursiva och gäller för hela dokumentträdet.|
| / prop /?  | Index-sökväg som krävs för att leverera frågor som liknar följande (med Hash- eller typer, respektive):<br><br>Välj från samlingen-c WHERE c.prop = ”value”<br><br>Välj från samlingen-c WHERE c.prop > 5<br><br>Välj samling c ORDER BY c.prop  |
| / prop / *  | Index sökväg för alla sökvägar under den angivna etiketten. Fungerar med följande frågor<br><br>Välj från samlingen-c WHERE c.prop = ”value”<br><br>Välj från samlingen-c WHERE c.prop.subprop > 5<br><br>Välj från samlingen-c WHERE c.prop.subprop.nextprop = ”value”<br><br>Välj samling c ORDER BY c.prop |
| / sammanställer / [] /?  | Index sökväg krävs för att hantera iteration och delta i frågor mot matriser av skalärer som [”a”, ”b”, ”c”]:<br><br>Välj tagg från taggen i collection.props var taggen = ”value”<br><br>Välj tagg från samlingen c JOIN-tagg i c.props där tagga > 5  |
| /Props/ [] /subprop/? | Index sökväg krävs för att hantera iteration och JOIN-frågor mot matriser av objekt som [{subprop: ”a”}, {subprop: ”b”}]:<br><br>Välj tagg från taggen i collection.props var tag.subprop = ”value”<br><br>Välj tagg från samlingen c JOIN-tagg i c.props var tag.subprop = ”value” |
| / prop/subprop /? | Index-sökväg som krävs för att leverera frågor (med Hash- eller typer, respektive):<br><br>Välj från samlingen-c WHERE c.prop.subprop = ”value”<br><br>Välj från samlingen-c WHERE c.prop.subprop > 5  |

När du ställer in anpassat index sökvägar, så måste du ange Standardregeln för indexering för hela artikeln, enligt den särskilda sökvägen `/*`.

## <a name="next-steps"></a>Nästa steg

Läs mer om indexering i Azure Cosmos DB i följande artiklar:

- [Översikt över indexering](index-overview.md)
- [Indexeringsprinciper i Azure Cosmos DB](indexing-policies.md)
- [Index-typer i Azure Cosmos DB](index-types.md)
