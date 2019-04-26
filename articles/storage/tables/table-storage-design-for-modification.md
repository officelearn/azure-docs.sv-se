---
title: Skapa Azure storage-tabeller för dataändringar | Microsoft Docs
description: Skapa tabeller för dataändringar i Azure-tabellagring.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: e993d169025f9b76c5e813bae31ca6cb2a39ba71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60325892"
---
# <a name="design-for-data-modification"></a>Utforma för dataändring
Den här artikeln fokuserar på designöverväganden för att optimera infogningar, uppdateringar och borttagningar. I vissa fall behöver du utvärdera det är säkerhetsaspekten Designer som optimerar för frågor mot Designer som optimerar för dataändringar precis som i utformning för relationsdatabaser (även om teknikerna för att hantera design avvägningarna är skiljer sig i en relationsdatabas). Designmönster för avsnittet tabell beskriver vissa detaljerad designmönster för Table service och visar några dessa kompromisser. I praktiken märker du att många Designer som optimerats för att fråga entiteter också fungerar bra för att ändra entiteter.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optimera prestandan för insert-, update- och delete-åtgärder
Om du vill uppdatera eller ta bort en entitet, måste du att kunna identifiera den med hjälp av den **PartitionKey** och **RowKey** värden. I detta avseende ditt val av **PartitionKey** och **RowKey** ändra entiteter bör följa liknande kriterier för att stödja punktfrågor eftersom du vill identifiera enheter som effektivt som möjligt. Du inte vill använda en ineffektiv skanning för partitionen eller tabell för att hitta en entitet för att identifiera den **PartitionKey** och **RowKey** värden som du behöver uppdatera eller ta bort den.  

Följande mönster i avsnittet tabell design mönster adress optimera prestanda eller din insert-, uppdatera och ta bort:  

* [Hög volym ta bort mönstret](table-storage-design-patterns.md#high-volume-delete-pattern) -aktivera borttagningen av ett stort antal entiteter genom att lagra alla entiteter för samtidiga borttagning i sina egna separata tabeller; du ta bort entiteter genom att ta bort tabellen.  
* [Serien datamönster](table-storage-design-patterns.md#data-series-pattern) -Store fullständig dataserier i en enda entitet att minimera antalet begäranden som du gör.  
* [Mönster för många entiteter](table-storage-design-patterns.md#wide-entities-pattern) -använder flera fysiska enheter för att lagra logiska entiteter med mer än 252 egenskaper.  
* [Mönster för stora entiteter](table-storage-design-patterns.md#large-entities-pattern) -använda blob storage för att lagra stora egenskapsvärden.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Säkerställa konsekvens i dina lagrade entiteter
Den viktiga faktor som påverkar ditt val av nycklar för att optimera dataändringar är att säkerställa konsekvens med hjälp av atomiska transaktioner. Du kan bara använda en EGT för att arbeta med entiteter som lagras i samma partition.  

Följande mönster i artikeln [tabell designmönster](table-storage-design-patterns.md) adress hantera konsekvens:  

* [Intra-partition sekundärt index mönstret](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -Store flera kopior av varje entitet med hjälp av olika **RowKey** värden (i samma partition) för att aktivera snabb och effektiv uppslag och alternativ sorteringsordningar med hjälp av olika **RowKey** värden.  
* [Mönster för kommunikation mellan sekundära Partitionsindex](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – Store flera kopior av varje entitet som använder olika RowKey värden i olika partitioner eller separata tabeller för att aktivera snabb och effektiv uppslag och alternativ sortera beställningar med hjälp av olika **RowKey** värden.  
* [Konsekvent transaktioner mönstret](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) -aktivera konsekvent beteenden över partitionsgränser eller gränser för storage-system med hjälp av Azure-köer.
* [Index entiteter mönstret](table-storage-design-patterns.md#index-entities-pattern) -Underhåll index entiteter för att aktivera effektiv sökning som returnerar en lista över entiteter.  
* [Mönster för denormalisering](table-storage-design-patterns.md#denormalization-pattern) -kombinera relaterade data tillsammans i en enda enhet så att du kan hämta alla data som du behöver med en enda fråga.  
* [Serien datamönster](table-storage-design-patterns.md#data-series-pattern) -Store fullständig dataserier i en enda entitet att minimera antalet begäranden som du gör.  

Information om entitetsgrupptransaktioner finns i avsnittet [entitetsgrupptransaktioner](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Kontrollera din design för effektiv ändringar underlättar effektiva frågor
En design för effektiva frågor ger effektiv ändringar, men du bör alltid i många fall kan utvärdera om så är fallet för ditt specifika scenario. Några av mönster i artikeln [tabell designmönster](table-storage-design-patterns.md) uttryckligen utvärdera avvägningarna mellan fråga och ändra entiteter och du får alltid ha i åtanke antalet varje typ av åtgärd.  

Följande mönster i artikeln [tabell designmönster](table-storage-design-patterns.md) åtgärda avvägningarna mellan designa för effektiva frågor och designar för effektiv dataändringar:  

* [Sammansatt nyckel mönstret](table-storage-design-patterns.md#compound-key-pattern) -Använd sammansatta **RowKey** värden att aktivera en klient att söka efter relaterade data med en enda fråga.  
* [Log tail mönstret](table-storage-design-patterns.md#log-tail-pattern) -hämta den *n* entiteter som nyligen lagt till en partition med hjälp av en **RowKey** värde som sorteras listan i omvänd datum- och tidsordning.  
