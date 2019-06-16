---
author: brjohnstmsft
ms.service: search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: b4d0bc73e652a1cd6ef59589318b92f63727c7f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079644"
---
| Datatyp | Funktioner som tillåts i lambda-uttryck med `any` | Funktioner som tillåts i lambda-uttryck med `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Allt utom `search.ismatch` och `search.ismatchscoring` | Samma |
| `Collection(Edm.String)` | Jämförelser med `eq` eller `search.in` <br/><br/> Kombinera underuttryck med `or` | Jämförelser med `ne` eller `not search.in()` <br/><br/> Kombinera underuttryck med `and` |
| `Collection(Edm.Boolean)` | Jämförelser med `eq` eller `ne` | Samma |
| `Collection(Edm.GeographyPoint)` | Med hjälp av `geo.distance` med `lt` eller `le` <br/><br/> `geo.intersects` <br/><br/> Kombinera underuttryck med `or` | Med hjälp av `geo.distance` med `gt` eller `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Kombinera underuttryck med `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Jämförelser med `eq`, `ne`, `lt`, `gt`, `le`, eller `ge` <br/><br/> Kombinera jämförelser med andra underordnade uttryck med hjälp av `or` <br/><br/> Kombinera jämförelser utom `ne` med andra underordnade uttryck med hjälp av `and` <br/><br/> Uttryck med hjälp av kombinationer av `and` och `or` i [Disjunktivt Normal formuläret (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Jämförelser med `eq`, `ne`, `lt`, `gt`, `le`, eller `ge` <br/><br/> Kombinera jämförelser med andra underordnade uttryck med hjälp av `and` <br/><br/> Kombinera jämförelser utom `eq` med andra underordnade uttryck med hjälp av `or` <br/><br/> Uttryck med hjälp av kombinationer av `and` och `or` i [Conjunctive Normal formuläret (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
