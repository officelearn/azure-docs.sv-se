---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80272609"
---
| Datatyp | Funktioner som tillåts i lambda uttryck med`any` | Funktioner som tillåts i lambda uttryck med`all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Allt `search.ismatch` utom och`search.ismatchscoring` | Samma |
| `Collection(Edm.String)` | Jämförelser med `eq` eller`search.in` <br/><br/> Kombinera underuttryck med`or` | Jämförelser med `ne` eller`not search.in()` <br/><br/> Kombinera underuttryck med`and` |
| `Collection(Edm.Boolean)` | Jämförelser med `eq` eller`ne` | Samma |
| `Collection(Edm.GeographyPoint)` | Använda `geo.distance` `lt` med eller`le` <br/><br/> `geo.intersects` <br/><br/> Kombinera underuttryck med`or` | Använda `geo.distance` `gt` med eller`ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Kombinera underuttryck med`and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Jämförelser med `eq` `ne`, `lt` `gt`, `le`, , eller`ge` <br/><br/> Kombinera jämförelser med andra underuttryck med`or` <br/><br/> Kombinera jämförelser utom `ne` med andra deluttryck med hjälp av`and` <br/><br/> Uttryck med kombinationer `and` `or` av och i [disjunktiv normalform (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Jämförelser med `eq` `ne`, `lt` `gt`, `le`, , eller`ge` <br/><br/> Kombinera jämförelser med andra underuttryck med`and` <br/><br/> Kombinera jämförelser utom `eq` med andra deluttryck med hjälp av`or` <br/><br/> Uttryck med kombinationer `and` `or` av och i [konjunktiv normalform (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
