---
title: Designa Azure Table Storage för dataändring | Microsoft-dokument
description: Designtabeller för dataändring i Azure Table storage.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: c95be7afae5c0a84c06b691c8225f32f2aa68260
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771554"
---
# <a name="design-for-data-modification"></a>Utforma för dataändring
Den här artikeln fokuserar på designöverväganden för att optimera infogningar, uppdateringar och borttagningar. I vissa fall måste du utvärdera avvägningen mellan mönster som optimerar för att fråga mot mönster som optimerar för datamodifiering precis som du gör i design för relationsdatabaser (även om teknikerna för att hantera design kompromisser är olika i en relationsdatabas). Avsnittet Tabelldesignmönster beskriver några detaljerade designmönster för tabelltjänsten och belyser några av dessa kompromisser. I praktiken kommer du att upptäcka att många designer som är optimerade för att fråga entiteter också fungerar bra för att ändra entiteter.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optimera prestanda för att infoga, uppdatera och ta bort åtgärder
Om du vill uppdatera eller ta bort en entitet måste du kunna identifiera den med hjälp av **värdena PartitionKey** och **RowKey.** I det här avseendet bör ditt val av **PartitionKey** och **RowKey** för att ändra entiteter följa liknande kriterier som ditt val för att stödja punktfrågor eftersom du vill identifiera entiteter så effektivt som möjligt. Du vill inte använda en ineffektiv partition eller tabellgenomsökning för att hitta en entitet för att identifiera **de PartitionKey-** och **RowKey-värden** som du behöver för att uppdatera eller ta bort den.  

Följande mönster i avsnittet Tabelldesignmönster behandlar optimering av prestanda eller infognings-, uppdaterings- och borttagningsåtgärder:  

* [Borttagningsmönster](table-storage-design-patterns.md#high-volume-delete-pattern) med hög volym - Aktivera borttagning av en hög mängd entiteter genom att lagra alla entiteter för samtidig borttagning i sin egen separata tabell. du tar bort entiteterna genom att ta bort tabellen.  
* [Dataseriemönster](table-storage-design-patterns.md#data-series-pattern) - Lagra fullständiga dataserier i en enda entitet för att minimera antalet begäranden du gör.  
* [Brett entitetsmönster](table-storage-design-patterns.md#wide-entities-pattern) - Använd flera fysiska entiteter för att lagra logiska entiteter med mer än 252 egenskaper.  
* [Stort entitetsmönster](table-storage-design-patterns.md#large-entities-pattern) - Använd blob-lagring för att lagra stora egenskapsvärden.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Se till att dina lagrade enheter är konsekventa
Den andra nyckelfaktorn som påverkar ditt val av nycklar för att optimera dataändringar är hur du säkerställer konsekvens med hjälp av atomtransaktioner. Du kan bara använda en EGT för att fungera på entiteter som lagras i samma partition.  

Följande mönster i artikeln [Tabelldesignmönster](table-storage-design-patterns.md) behandlar konsekvens:  

* [Sekundärt indexmönster inom partitionen](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Lagra flera kopior av varje entitet med hjälp av olika **RowKey-värden** (i samma partition) för att möjliggöra snabba och effektiva sökninger och alternativa sorteringsorder med hjälp av olika **RowKey-värden.**  
* [Sekundärt indexmönster mellan partitioner](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Lagra flera kopior av varje entitet med olika RowKey-värden i separata partitioner eller i separata tabeller för att möjliggöra snabba och effektiva sökninger och alternativa sorteringsorder med hjälp av olika **RowKey-värden.**  
* [Så småningom konsekventa transaktionsmönster](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) - Aktivera så småningom konsekvent beteende över partitionsgränser eller lagringssystemgränser med hjälp av Azure-köer.
* [Indexentiteter mönstrar](table-storage-design-patterns.md#index-entities-pattern) - Underhåll indexentiteter för att möjliggöra effektiva sökningar som returnerar listor över entiteter.  
* [Denormaliseringsmönster](table-storage-design-patterns.md#denormalization-pattern) - Kombinera relaterade data tillsammans i en enda entitet så att du kan hämta alla data du behöver med en enda punktfråga.  
* [Dataseriemönster](table-storage-design-patterns.md#data-series-pattern) - Lagra fullständiga dataserier i en enda entitet för att minimera antalet begäranden du gör.  

Information om entitetsgruppstransaktioner finns i avsnittet [Entitetsgruppstransaktioner](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Se till att din design för effektiva ändringar underlättar effektiva frågor
I många fall resulterar en design för effektiv fråga i effektiva ändringar, men du bör alltid utvärdera om detta är fallet för ditt specifika scenario. Några av mönstren i artikeln [Tabelldesignmönster utvärderar](table-storage-design-patterns.md) uttryckligen kompromisser mellan att fråga och ändra entiteter, och du bör alltid ta hänsyn till antalet för varje typ av åtgärd.  

Följande mönster i artikeln [Tabelldesignmönster](table-storage-design-patterns.md) tar itu med kompromisser mellan att utforma för effektiva frågor och att utforma för effektiv dataändring:  

* [Sammansattnyckelmönster](table-storage-design-patterns.md#compound-key-pattern) - Använd sammansatta **RowKey-värden** för att en klient ska kunna söka efter relaterade data med en enda punktfråga.  
* [Log tail pattern](table-storage-design-patterns.md#log-tail-pattern) - Hämta n-entiteterna som senast lades till i en partition med hjälp av ett *n* **RowKey-värde** som sorteras i omvänd datum och tidsordning.  
