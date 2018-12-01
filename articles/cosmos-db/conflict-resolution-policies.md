---
title: Konflikt upplösning-typer och upplösning principer i Azure Cosmos DB
description: Den här artikeln beskriver konflikt kategorier och principer för lösning av konflikt i Azure Cosmos DB.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 5506b27ce56ca7a83ce16aab818767a392d77430
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52680302"
---
# <a name="conflict-types-and-resolution-policies"></a>Konflikttyper och matchningsprinciper

Skriva regioner konflikter och konfliktlösning policys är tillämpbara om Azure Cosmos DB-kontot har konfigurerats med flera.

Uppdateringskonflikter kan inträffa när skrivare samtidigt uppdaterar samma objekt i flera regioner för Azure Cosmos DB-konton som har konfigurerats med flera Skriv-regioner. Uppdateringskonflikter indelas i följande tre typer.

* **Infoga konflikter**: dessa konflikter kan uppstå när ett program samtidigt infogar två eller flera objekt med samma unika index från två eller fler regioner. Exempelvis kan kan den här konflikt uppstå med en ID-egenskap. Alla skrivningar kan lyckas inledningsvis i deras respektive lokala regioner. Men baserat på principen för konfliktlösning du väljer kan bara ett objekt med den ursprungliga ID strävar slutligen.

* **Ersätt konflikter**: dessa konflikter kan uppstå när ett program uppdaterar ett enskilt objekt samtidigt från två eller fler regioner.

* **Ta bort konflikter**: dessa konflikter kan uppstå när ett program samtidigt tar bort ett objekt från en region och uppdaterar den från en annan region.

## <a name="conflict-resolution-policies"></a>Konfliktlösningsprinciper

Azure Cosmos DB erbjuder en flexibel principstyrda mekanism för att lösa Uppdateringskonflikter. Du kan välja mellan två konflikt upplösning principer på en Azure Cosmos DB-behållare:

- **Senaste skriva Wins (LWW)**: den här principen, som standard använder en systemdefinierade tidsstämpel-egenskapen. Den baseras på protokollet tidssynkronisering klockan. Om du använder Azure Cosmos DB SQL API kan ange du någon annan anpassad numeriska egenskap som ska användas för konfliktlösning. En anpassad numeriska egenskap kallas också konflikt upplösning sökvägen. 

  Om två eller flera objekt konflikt på Infoga eller ersätt-åtgärder, blir objektet med det högsta värdet för vägen med konflikt upplösning vinnaren. Systemet avgör vinnaren om flera objekt har samma numeriskt värde för konflikt upplösning sökvägen. Alla regioner garanteras att Konvergera till en enda vinnare och slutar in med samma version av det allokerade objektet. När du ta bort konflikter ingår den borttagna versionen alltid wins infoga eller ersätta konflikter. Det här resultatet inträffar oavsett värdet för konflikt upplösning sökvägen.

  > [!NOTE]
  > Senaste skriva Wins är standardprincipen för konfliktlösning. Den är tillgänglig för SQL, Azure Cosmos DB Table, MongoDB, Cassandra och Gremlin-API-konton.

  Mer information finns i [exempel som använder LWW står i konflikt upplösning principer](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Anpassad**: den här principen är utformat för programdefinierade semantik för avstämningen av konflikter. När du anger den här principen på din Azure Cosmos DB-behållare kan behöva du också registrera en merge lagrad procedur. Den här proceduren anropas automatiskt när konflikter upptäcks under en databastransaktion på servern. Systemet innehåller exakt en gång garanterar för körning av en merge-procedur som en del av protokollet åtagande.  

  Det finns två saker att komma ihåg att om du konfigurerar din behållare med alternativet för anpassad lösning. Om du inte kan registreras en merge-procedur på behållaren eller merge-procedur genereras ett undantag vid körning, har det är i konflikt skrivits till konflikter feed. Sedan ditt program måste manuellt lösa konflikter i konflikter feed. Mer information finns i [exempel på hur du använder den anpassade principen och hur du använder det orsakar en konflikt feed](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

  > [!NOTE]
  > Principen för anpassad konfliktlösning är endast tillgänglig för SQL-API-konton.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar principer för lösning av konflikt. Se följande artiklar:

* [Använd principen för konfliktlösning LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Använd principen för anpassad konfliktlösning](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Använd det orsakar en konflikt feed](how-to-manage-conflicts.md#read-from-conflict-feed)
