---
title: Utforma Azure storage-tabeller för dataändring | Microsoft Docs
description: Skapa tabeller för dataändring i Azure table storage.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: 6c008175f01521ce4f96d13e58244dc72d9f6990
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34661061"
---
# <a name="design-for-data-modification"></a>Design för dataändring
Den här artikeln fokuserar på designöverväganden för att optimera infogningar, uppdateringar, och tar bort. I vissa fall behöver du utvärdera kompromissen mellan Designer optimerar för frågor mot Designer optimerar för dataändring precis som i Designer för relationsdatabaser (även om metoder för att hantera design avvägningarna är olika i en relationsdatabas). Avsnittet [tabell designmönster](#table-design-patterns) beskriver vissa detaljerad designmönster för tabelltjänsten och beskrivs några dessa avvägningarna. Du hittar många Designer som optimerats för att fråga entiteter också fungerar bra för att ändra entiteter i praktiken.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optimera prestanda för insert-, update- och delete-åtgärder
Om du vill uppdatera eller ta bort en entitet, du måste kunna identifieras med hjälp av den **PartitionKey** och **RowKey** värden. I detta avseende valet av **PartitionKey** och **RowKey** för ändra entiteter bör följa liknande kriterier till ditt val att stödja punkt frågor eftersom du vill identifiera enheter som effektiv som möjligt. Du inte vill använda en ineffektiv partition eller tabell sökning för att hitta en enhet för att identifiera den **PartitionKey** och **RowKey** värden som du behöver uppdatera eller ta bort den.  

Följande mönster i avsnittet [tabell designmönster](#table-design-patterns) adress optimera prestanda eller din insert, update och delete-åtgärder:  

* [Hög volym ta bort mönster](table-storage-design-patterns.md#high-volume-delete-pattern) -Aktivera borttagning av ett stort antal enheter genom att lagra alla entiteter för samtidiga borttagning i sina egna separata tabeller; ta bort entiteterna genom att ta bort tabellen.  
* [Serien datamönster](table-storage-design-patterns.md#data-series-pattern) -Store fullständig dataserier i en enda enhet för att minimera antalet begäranden som du gör.  
* [Wide entiteter mönster](table-storage-design-patterns.md#wide-entities-pattern) -använda flera fysiska enheter för att lagra logiska entiteter med mer än 252 egenskaper.  
* [Mönster för stora entiteter](table-storage-design-patterns.md#large-entities-pattern) -använda blob storage för att lagra stora egenskapsvärden.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Säkerställa konsekvens i lagrade entiteter
Den viktiga faktor som påverkar ditt val av nycklar för att optimera dataändringar är att säkerställa konsekvens med hjälp av atomiska transaktioner. Du kan bara använda en EGT ska fungera på entiteter som lagras i samma partition.  

Följande mönster i artikeln [tabell designmönster](table-storage-design-patterns.md) adress hantera konsekvens:  

* [Intra-partition sekundärt index mönster](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -lagra flera kopior av varje enhet med hjälp av olika **RowKey** värden (i samma partition) för att aktivera snabb och effektiv sökningar och alternativa sorteringsordningen genom att använda olika **RowKey** värden.  
* [Mellan sekundära Partitionsindex mönster](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – lagra flera kopior av varje entitet som använder olika RowKey värden i olika partitioner eller i separata tabeller för att aktivera snabb och effektiv sökningar och alternativa sorteringen sorterar med hjälp av olika **RowKey** värden.  
* [Överensstämmelse transaktioner mönster](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) -aktivera överensstämmelse beteende mellan partitionsgränser eller lagring system gränser med hjälp av Azure köer.
* [Index entiteter mönster](table-storage-design-patterns.md#index-entities-pattern) -Underhåll index enheter om du vill aktivera effektiva sökningar som returnerar en lista över enheter.  
* [Denormalization mönster](table-storage-design-patterns.md#denormalization-pattern) -kombinera relaterade data tillsammans i en enda enhet så att du kan hämta alla data som du behöver med en enda fråga.  
* [Serien datamönster](table-storage-design-patterns.md#data-series-pattern) -Store fullständig dataserier i en enda enhet för att minimera antalet begäranden som du gör.  

Information om entiteten grupptransaktioner finns i avsnittet [entitet gruppera transaktioner](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Kontrollera din design för effektiv ändringar underlättar effektiv frågor
I många fall bör alltid en design för effektiva frågor ger effektiv ändringar, men du utvärdera om så är fallet för din situation. Några av mönster i artikeln [tabell designmönster](table-storage-design-patterns.md) explicit utvärdera avvägningarna mellan fråga och ändra entiteter och du bör alltid beakta numret för varje typ av åtgärd.  

Följande mönster i artikeln [tabell designmönster](table-storage-design-patterns.md) adressen avvägningarna mellan utformning för effektiva frågor och designar för effektiv dataändring:  

* [Sammansatt nyckel mönster](table-storage-design-patterns.md#compound-key-pattern) -Använd sammansatta **RowKey** värden att aktivera en klient att söka efter relaterade data med en enda fråga.  
* [Loggen pilslut mönster](table-storage-design-patterns.md#log-tail-pattern) -hämta den *n* entiteter som senast lades till en partition med hjälp av en **RowKey** värde som sorterar i omvänd datum och tid ordning.  
