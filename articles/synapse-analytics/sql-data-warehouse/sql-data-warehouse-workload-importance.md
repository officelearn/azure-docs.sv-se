---
title: Arbetsbelastningsprioritet
description: Vägledning för att ange betydelse för Synapse SQL-poolfrågor i Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 43ee14784b6049e9b5c1a78e733e72bbc45f915d
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744036"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Azure Synapse Analytics arbetsbelastning betydelse

I den här artikeln beskrivs hur arbetsbelastningsbetydelse kan påverka körningsordningen för Synapse SQL-poolbegäranden i Azure Synapse.

## <a name="importance"></a>Betydelse

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Affärsbehov kan kräva att datalagringsarbetsbelastningar är viktigare än andra.  Tänk dig ett scenario där verksamhetskritiska försäljningsdata läses in innan räkenskapsperioden stängs.  Databelastningar för andra källor, till exempel väderdata, har inte strikta SLA:er. Om du ställer in stor betydelse för en begäran om att läsa in försäljningsdata och låg prioritet för en begäran om att läsa in väderdata säkerställer du att försäljningsdatabelastningen får första åtkomst till resurser och slutför snabbare.

## <a name="importance-levels"></a>Viktnivåer

Det finns fem nivåer av betydelse: låg, below_normal, normal, above_normal och hög.  Begäranden som inte anger prioritet tilldelas standardnivån för normal. Begäranden som har samma prioritetsnivå har samma schemaläggningsbeteende som finns idag.

## <a name="importance-scenarios"></a>Scenarier för betydelse

Utöver det scenario för grundläggande betydelse som beskrivs ovan med försäljnings- och väderdata finns det andra scenarier där arbetsbelastningsbetydelse hjälper till att uppfylla databehandlings- och frågebehov.

### <a name="locking"></a>Låsning

Tillgång till lås för läs- och skrivaktivitet är ett område med naturlig konkurrens. Aktiviteter som [partitionsväxling](sql-data-warehouse-tables-partition.md) eller [BYT NAMN PÅ OBJEKT](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) kräver förhöjda lås.  Utan arbetsbelastningsbetydelse optimerar Synapse SQL-pool i Azure Synapse för dataflöde. Optimering för dataflöde innebär att när du kör och köade begäranden har samma låsbehov och resurser är tillgängliga, kan de köade begärandena kringgå begäranden med högre låsbehov som kom i förfråganskön tidigare. När arbetsbelastningens betydelse tillämpas på begäranden med högre låsbehov. Begäran med högre prioritet kommer att köras före begäran med lägre prioritet.

Ta följande som exempel:

- Q1 körs aktivt och väljer data från SalesFact.
- Q2 står i kö i väntan på att Q1 ska slutföras.  Den lämnades in klockan 09:00 och försöker partitionera växla nya data till SalesFact.
- Q3 skickas klockan 09:01 och vill välja data från SalesFact.

Om Q2 och Q3 har samma betydelse och Q1 fortfarande körs, kommer Q3 att börja köra. Q2 kommer att fortsätta att vänta på ett exklusivt lås på SalesFact.  Om Q2 har större betydelse än Q3 väntar Q3 tills Q2 är klar innan den kan börja utföra.

### <a name="non-uniform-requests"></a>Icke-enhetliga begäranden

Ett annat scenario där betydelse kan hjälpa till att uppfylla frågekrav är när begäranden med olika resursklasser skickas.  Som tidigare nämnts optimerar Synapse SQL-pool i Azure Synapse för dataflöde under samma betydelse. När begäranden med blandad storlek (till exempel smallrc eller mediumrc) står i kö väljer Synapse SQL-pool den tidigaste begäran som passar inom de tillgängliga resurserna. Om arbetsbelastningsbetydelse tillämpas schemaläggs begäran med högsta prioritet nästa.
  
Tänk på följande exempel på DW500c:

- Q1, Q2, Q3 och Q4 kör smallrc-frågor.
- Q5 skickas med mediumrc resursklass vid 09:00.
- Q6 skickas med smallrc resursklass kl 9:01.

Eftersom Q5 är mediumrc kräver det två samtidighetsplatser. Q5 måste vänta på att två av de frågor som körs har slutförts.  Men när en av de frågor som körs (Q1-Q4) slutförs, schemaläggs Q6 omedelbart eftersom resurserna finns för att köra frågan.  Om Q5 har större betydelse än Q6 väntar Q6 tills Q5 körs innan den kan börja köras.

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du skapar en klassificerare finns i [CREATE WORKLOAD CLASSIFIER (Transact-SQL).](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  
- Mer information om arbetsbelastningsklassificering finns i [Arbetsbelastningsklassificering](sql-data-warehouse-workload-classification.md).  
- Se Quickstart [Skapa arbetsbelastningsklassificerare](quickstart-create-a-workload-classifier-tsql.md) för hur du skapar en arbetsbelastningsklassificerare.
- Se hur du gör-artiklar för att [konfigurera arbetsbelastningsbetydning](sql-data-warehouse-how-to-configure-workload-importance.md) och hur du [hanterar och övervakar arbetsbelastningshantering](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Se [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om du vill visa frågor och den tilldelade betydelsen.
