---
title: Konflikt upplösning-typer och upplösning principer i Azure Cosmos DB
description: Den här artikeln beskriver konflikt kategorier och principer för lösning av konflikt i Azure Cosmos DB.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: c682b61a39224f2c80db8fe5fa153ea5e5d82922
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958580"
---
# <a name="conflict-types-and-resolution-policies"></a>Konflikt typer och principer för lösning

Skriva regioner konflikter och konfliktlösning policys är tillämpbara om Cosmos-konto har konfigurerats med flera.

För Cosmos DB-konton som har konfigurerats med flera Skriv-regioner kan update konflikter uppstå när flera skrivare samtidigt uppdaterar samma objekt i flera regioner. Uppdateringskonflikter indelas i följande tre typer.

1. **Infoga konflikter:** dessa konflikter kan uppstå när ett program samtidigt infogar två eller flera objekt med samma unika index (till exempel ID-egenskap) från två eller fler regioner. I det här fallet alla skrivningar kan lyckas inledningsvis i deras respektive lokala regioner, men baserat på principen för konfliktlösning du väljer kan bara ett objekt med den ursprungliga ID strävar slutligen.

1. **Ersätt konflikter:** dessa konflikter kan uppstå när ett program uppdaterar ett enskilt objekt samtidigt från två eller fler regioner.

1. **Ta bort konflikter:** dessa konflikter kan uppstå när ett program samtidigt tar bort ett objekt från en region och uppdaterar den från andra regioner.

## <a name="conflict-resolution-policies"></a>Konfliktlösningsprinciper

Cosmos DB erbjuder en flexibel principstyrda mekanism för att lösa Uppdateringskonflikter. Du kan välja mellan följande två konflikt upplösning principer för en Cosmos-behållare:

- **Senaste-Write-Wins (LWW):** den här principen som standard använder en systemdefinierade tidsstämpelsegenskapen (baserat på protokollet tidssynkronisering clock). Du kan också när du använder SQL-API, kan Cosmos DB du ange någon annan anpassad numeriska egenskap (kallas även ”konflikt upplösning sökväg”) för att användas för konfliktlösning.  

  Om två eller flera objekt konflikt på Infoga eller ersätt-åtgärder, blir det objekt som innehåller det högsta värdet för ”konflikt upplösning sökväg” ”vinnare”. Om flera objekt har samma numeriskt värde för konflikt upplösning sökvägen, bestäms den valda ”vinnare”-versionen av systemet. Alla regioner garanteras att Konvergera till en enda vinnare och slutar upp med identiska version av det allokerade objektet. Om det finns konflikter delete som ingår, wins över antingen infoga den borttagna versionen alltid eller ersätta konflikter, oavsett vilket värde sökvägens konflikt lösning.

  > [!NOTE]
  > Senaste-Write-Wins är principen för konfliktlösning standard och den är tillgänglig för SQL, Table, MongoDB, Cassandra och Gremlin-API-konton.

  Mer information finns i [exempel med LWW står i konflikt upplösning principer](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Anpassad:** den här principen är utformat för programdefinierade semantik för avstämningen av konflikter. När du anger den här principen på din Cosmos-behållare, måste du också registrera en merge lagrade proceduren som anropas automatiskt om Uppdateringskonflikter upptäcks under en databastransaktion på servern. Systemet innehåller exakt en gång garanterar för körning av en merge-procedur som en del av protokollet åtagande.  

  Om du konfigurerar din behållare med anpassade lösningsalternativet men antingen inte kan registreras en merge-procedur på behållaren, eller om proceduren merge genereras ett undantag vid körning, men skrivs det orsakar en konflikt till konflikter feed. Programmet måste manuellt lösa konflikter i konflikter-feeden. Mer information finns i [exempel på användning av anpassade principen och hur du använder konflikter feed](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

  > [!NOTE]
  > Principen för anpassad konfliktlösning är endast tillgänglig för SQL-API-konton.

## <a name="next-steps"></a>Nästa steg

Därefter kan du lära dig hur du konfigurerar principer för lösning av konflikt med hjälp av följande artiklar:

* [Hur du använder principen för konfliktlösning LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Hur du använder principen för anpassad konfliktlösning](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Hur du använder konflikter-feed](how-to-manage-conflicts.md#read-from-conflict-feed)
