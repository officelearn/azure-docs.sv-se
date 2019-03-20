---
title: Arbetsbelastningen vikten | Microsoft Docs
description: Riktlinjer för att ställa in vikten för frågor i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: e53a6fcefb0f5370f6e24cc50fad2ad4ad4c64e3
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2019
ms.locfileid: "57864097"
---
# <a name="sql-data-warehouse-workload-importance-preview"></a>SQL Data Warehouse arbetsbelastning vikten (förhandsversion)

Den här artikeln förklarar hur arbetsbelastning prioritet kan påverka ordningen för körningen för SQL Data Warehouse-begäranden.

> [!Note]
> Prioritet för arbetsbelastningen är tillgänglig på SQL-informationslager Gen2.

## <a name="importance"></a>Prioritet

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Affärsbehov kan kräva informationslager arbetsbelastningar för att vara viktigare än andra.  Tänk dig ett scenario där uppdragskritiska försäljning data har lästs in innan räkenskapsåret stänger tidsperiod.  Data har lästs in för andra källor som weather-data inte har strikta serviceavtal.   Konfigurera hög prioritet för en begäran att läsa in försäljningsdata och låg prioritet på en begäran samtidigt, oavsett om data säkerställer försäljningsdata belastningen hämtar första åtkomst till resurser och slutför snabbare.

## <a name="importance-levels"></a>Prioritetsnivåer

Det finns fem nivåer: låg, under normal, normal, över Normal och hög.  Begäranden som inte anger prioritet tilldelas standardnivån för normal.  Begäranden som har samma prioritetsnivå har samma schemaläggning funktioner som finns i dag.

## <a name="importance-scenarios"></a>Vikten scenarier

Bortom det grundläggande vikt scenario som beskrivs ovan med försäljning och weather-data, finns det andra scenarier där arbetsbelastning vikten hjälper dem att uppfylla bearbetning och fråga efter behov.

### <a name="locking"></a>Låsning

Åtkomst till lås för Läs och Skriv aktivitet är en del av naturliga konkurrens.  Aktiviteter som [partition växla](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) eller [Byt namn på OBJEKTET](/sql/t-sql/statements/rename-transact-sql) kräver förhöjd Lås.  Utan arbetsbelastning viktiga optimerar SQL Data Warehouse för dataflöde.  Optimera för dataflöde innebär att när du kör och begäranden i kö har samma låsning behov och resurser är tillgängliga kringgå i begäranden i kö förfrågningar med högre låsning behov som anlänt i kön tidigare.  När arbetsbelastningen vikten har använts på begäranden med högre låsning måste. Begäran med högre prioritet ska köras innan begäran med lägre prioritet.

Ta följande som exempel:

Q1 körs aktivt och att välja data från salesfact (säljfakta).
Q2 är i kö väntar på att Q1 att slutföra.  Det skickades kl. 9 och försöker att partitionera växel nya data i salesfact (säljfakta).
Q3 skickas kl. 9:01 och vill välja data från salesfact (säljfakta).

Om k2 och K3 ha samma prioritet och Q1 fortfarande körs, börjar Q3 köras. Q2 fortsätter att vänta tills ett exklusivt lås på salesfact (säljfakta).  Om Q2 har högre prioritet än Q3, väntar Q3 tills Q2 är klar innan den kan börja körning.

### <a name="non-uniform-requests"></a>Icke-enhetlig begäranden

Ett annat scenario där prioritet kan hjälpa dig att uppfylla krav på frågor är när begäranden med olika resursklasser skickas.  Som tidigare har nämns, i lika viktiga, optimerar SQL Data Warehouse för dataflöde.  När blandat storlek (till exempel smallrc eller mediumrc) begärandena räknas, väljer SQL Data Warehouse den tidigaste inkommande begäran som passar i de tillgängliga resurserna.  Om arbetsbelastningen prioritet används, kommer bredvid högsta prioritet begäran.
  
Överväg följande exempel på DW500c:

F1, F2, Q3 och kvartal 4 körs smallrc frågor.
F5 har skickats med resursklass mediumrc kl. 9.
Q6 skickas med smallrc resursklass kl. 9:01.

Eftersom F5 är mediumrc, kräver två samtidighetsfack.  F5 måste vänta för två av frågorna som körs för att slutföra.  Men när en av frågorna som körs (Q1 F4) har slutförts schemaläggs Q6 omedelbart eftersom resurserna som finns för att köra frågan.  Om F5 har högre prioritet än Q6, väntar Q6 tills F5 körs innan den kan börja köra.

## <a name="next-steps"></a>Nästa steg

Läs mer om SQL Data Warehouse arbetsbelastning klassificering, [SQL Data Warehouse arbetsbelastning klassificering](sql-data-warehouse-workload-classification.md) och [skapa en arbetsbelastning klassificerare](quickstart-create-a-workload-classifier-tsql.md). Se [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) visa frågor och den prioritet som tilldelas.
