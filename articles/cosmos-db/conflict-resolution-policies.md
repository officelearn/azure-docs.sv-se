---
title: Konflikt upplösning-typer och principer för lösning med flera skriva regioner i Azure Cosmos DB
description: Den här artikeln beskriver konflikt kategorier och principer för lösning av konflikt i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 24201cfd657d4f23eb962b7407ed20262d780cf7
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407427"
---
# <a name="conflict-types-and-resolution-policies"></a>Konflikttyper och matchningsprinciper

Skriva regioner konflikter och konfliktlösning policys är tillämpbara om Azure Cosmos DB-kontot har konfigurerats med flera.

Uppdateringskonflikter kan inträffa när skrivare samtidigt uppdaterar samma objekt i flera regioner för Azure Cosmos-konton som har konfigurerats med flera Skriv-regioner. Uppdateringskonflikter kan vara av följande tre typer:

* **Infoga konflikter**: Dessa konflikter kan uppstå när ett program samtidigt infogar två eller flera objekt med samma unika index i två eller flera regioner. Exempelvis kan kan den här konflikt uppstå med en ID-egenskap.

* **Ersätt konflikter**: Dessa konflikter kan uppstå när ett program uppdaterar samma objekt samtidigt i två eller fler regioner.

* **Ta bort konflikter**: Dessa konflikter kan uppstå när ett program samtidigt tar bort ett objekt i en region och uppdaterar den i en annan region.

## <a name="conflict-resolution-policies"></a>Konfliktlösningsprinciper

Azure Cosmos DB erbjuder en flexibel principstyrda mekanism för att lösa konflikter för skrivning. Du kan välja mellan två konflikt upplösning principer på en Azure Cosmos-behållare:

- **Senast ändrad Wins (LWW)**: Den här principen, använder som standard en systemdefinierade tidsstämpel-egenskapen. Den baseras på protokollet tidssynkronisering klockan. Om du använder SQL-API kan ange du någon annan anpassad numeriska egenskap (t.ex. egna begreppet en tidsstämpel) ska användas vid konfliktlösning. En anpassad numeriska egenskap också kallas den *sökväg för lösning av konflikt*. 

  Om två eller flera objekt konflikt på Infoga eller ersätt-åtgärder, blir objektet med det högsta värdet för vägen med konflikt upplösning vinnaren. Systemet avgör vinnaren om flera objekt har samma numeriskt värde för konflikt upplösning sökvägen. Alla regioner garanteras att Konvergera till en enda vinnare och slutar in med samma version av det allokerade objektet. När du ta bort konflikter ingår den borttagna versionen alltid wins infoga eller ersätta konflikter. Det här resultatet inträffar oavsett värdet för konflikt upplösning sökvägen finns.

  > [!NOTE]
  > Senaste skriva Wins är standardprincipen för konfliktlösning. Den är tillgänglig för följande API: er: SQL, MongoDB, Cassandra, Gremlin och tabellen.

  Mer information finns i [exempel som använder LWW står i konflikt upplösning principer](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Anpassat**: Den här principen är utformat för programdefinierade semantik för avstämningen av konflikter. När du anger den här principen på din Azure Cosmos-behållare kan du också behöva registrera en *sammanfoga lagrade proceduren*. Den här proceduren anropas automatiskt när konflikter upptäcks under en databastransaktion på servern. Systemet innehåller exakt en gång garanterar för körning av en merge-procedur som en del av protokollet åtagande.  

  Om du konfigurerar din behållare med alternativet anpassad lösning och du inte kan registreras en merge-procedur på behållaren eller merge-procedur genereras ett undantag vid körning, det är i konflikt har skrivits till den *konflikter feed*. Sedan ditt program måste manuellt lösa konflikter i konflikter feed. Mer information finns i [exempel på hur du använder den anpassade principen och hur du använder det orsakar en konflikt feed](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

  > [!NOTE]
  > Principen för anpassad konfliktlösning är endast tillgänglig för SQL-API-konton.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar principer för lösning av konflikt:

* [Så här konfigurerar du multimaster i dina program](how-to-multi-master.md)
* [Hur du använder principen för konfliktlösning LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Hur du använder principen för anpassad konfliktlösning](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Läsa från står i konflikt feed](how-to-manage-conflicts.md#read-from-conflict-feed)
