---
title: Konfliktlösningstyper och lösningsprinciper i Azure Cosmos DB
description: I den här artikeln beskrivs konfliktkategorier och konfliktlösningsprinciper i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a8ee72f46e1789088e779c10a0824262469ffde8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441992"
---
# <a name="conflict-types-and-resolution-policies"></a>Konflikttyper och matchningsprinciper

Konflikt- och konfliktlösningsprinciper gäller om ditt Azure Cosmos DB-konto är konfigurerat med flera skrivregioner.

För Azure Cosmos-konton som konfigurerats med flera skrivregioner kan uppdateringskonflikter uppstå när författare samtidigt uppdaterar samma objekt i flera regioner. Uppdateringskonflikter kan vara av följande tre typer:

* **Infoga konflikter**: Dessa konflikter kan uppstå när ett program infogar två eller flera objekt samtidigt med samma unika index i två eller flera regioner. Den här konflikten kan till exempel uppstå med en ID-egenskap.

* **Ersätt konflikter**: Dessa konflikter kan uppstå när ett program uppdaterar samma objekt samtidigt i två eller flera regioner.

* **Ta bort konflikter**: Dessa konflikter kan uppstå när ett program samtidigt tar bort ett objekt i en region och uppdaterar det i en annan region.

## <a name="conflict-resolution-policies"></a>Konfliktlösningsprinciper

Azure Cosmos DB erbjuder en flexibel principdriven mekanism för att lösa skrivkonflikter. Du kan välja mellan två konfliktlösningsprinciper på en Azure Cosmos-behållare:

* **Senaste skriv wins (LWW)**: Den här lösningsprincipen använder som standard en systemdefinierad tidsstämpelegenskap. Det är baserat på tidssynkroniseringsprotokollet. Om du använder SQL API kan du ange vilken annan anpassad numerisk egenskap som helst (t.ex. din egen uppfattning om en tidsstämpel) som ska användas för konfliktlösning. En anpassad numerisk egenskap kallas också *konfliktlösningssökvägen*. 

  Om två eller flera objekt står i konflikt vid infognings- eller ersättningsåtgärder blir objektet med det högsta värdet för konfliktlösningsvägen vinnaren. Systemet avgör vinnaren om flera objekt har samma numeriska värde för konfliktlösningsvägen. Alla regioner är garanterade att konvergera till en enda vinnare och sluta med samma version av det infästa objektet. När det är inblandade i borttagningskonflikter vinner den borttagna versionen alltid över antingen infoga eller ersätta konflikter. Det här resultatet inträffar oavsett värdet på konfliktlösningsvägen.

  > [!NOTE]
  > Senaste skriv wins är standard konfliktlösningsprincipen `_ts` och använder tidsstämpel för följande API:er: SQL, MongoDB, Cassandra, Gremlin och Table. Anpassad numerisk egenskap är endast tillgänglig för SQL API.

  Mer information finns i [exempel som använder LWW konfliktlösningsprinciper](how-to-manage-conflicts.md).

* **Anpassad**: Den här lösningsprincipen är utformad för programdefinierad semantik för avstämning av konflikter. När du anger den här principen för din Azure Cosmos-behållare måste du också registrera en *kopplad lagrad procedur*. Den här proceduren anropas automatiskt när konflikter upptäcks under en databastransaktion på servern. Systemet ger exakt en gång garanti för genomförandet av ett sammanfogningsförfarande som en del av åtagandeprotokollet.  

  Om du konfigurerar behållaren med alternativet anpassad upplösning och du inte kan registrera en kopplingsprocedur på behållaren eller så genererar kopplingsproceduren ett undantag vid körning, skrivs konflikterna till *konfliktflödet*. Programmet måste sedan lösa konflikterna manuellt i konfliktflödet. Mer information finns [i exempel på hur du använder principen om anpassad lösning och hur du använder konfliktflödet](how-to-manage-conflicts.md).

  > [!NOTE]
  > Anpassad konfliktlösningsprincip är endast tillgänglig för SQL API-konton.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar konfliktlösningsprinciper:

* [Konfigurera multi-master i dina program](how-to-multi-master.md)
* [Så här hanterar du konfliktlösningsprinciper](how-to-manage-conflicts.md)
* [Så här läser du från konfliktflödet](how-to-manage-conflicts.md#read-from-conflict-feed)
