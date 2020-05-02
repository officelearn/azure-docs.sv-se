---
title: Vad är Azure Synapse Analytics (tidigare SQL DW)?
description: Azure Synapse Analytics (tidigare SQL DW) är en obegränsad analys tjänst som sammanför företags data lager hantering och stor data analys.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 27e6c7beb8c88a29b90b30e0c77e35e493f2480a
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690912"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Vad är Azure Synapse Analytics (tidigare SQL DW)?

> [!NOTE] 
> Testa de senaste Azure Synapse-funktionerna som arbets ytor, Spark, SQL på begäran och den integrerade Synapse Studio-upplevelsen genom att [begära åtkomst till Azure Synapse (för hands versioner av arbets ytor)](https://aka.ms/synapsepreview). 
>
>Utforska [dokumentationen för Azure Synapse (arbets ytans för hands version)](../overview-what-is.md).

Azure Synapse är en analys tjänst som samlar ihop företags data lager hantering och stor data analys. Det ger dig friheten att fråga efter data på dina villkor, med hjälp av antingen serverlösa resurser på begäran eller etablerade resurser – i stor skala. Azure synapse ger de här två världar tillsammans med en enhetlig upplevelse för att mata in, förbereda, hantera och hantera data för omedelbara BI-och maskin inlärnings behov.

Azure-Synapse har fyra komponenter:

- Synapse SQL: fullständig T-SQL-baserad analys – allmänt tillgänglig
  - SQL-pool (betala per DWU etablerad)
  - SQL på begäran (betala per TB bearbetat) – (för hands version)
- Spark: djupt integrerad Apache Spark (förhands granskning)
- Synapse-pipeline: hybrid data integrering (för hands version)
- Studio: enhetlig användar upplevelse.  (Förhandsversion)



## <a name="synapse-sql-pool-in-azure-synapse"></a>Synapse SQL-pool i Azure Synapse

Synapse SQL-pool syftar på de företags data lager funktioner som är allmänt tillgängliga i Azure Synapse.

SQL-poolen representerar en samling analys resurser som tillhandahålls när du använder Synapse SQL. Storleken på SQL-poolen bestäms av data lager enheter (DWU).

Importera Big data med enkla [PolyBase](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL-frågor och Använd sedan kraften i MPP för att köra analys med höga prestanda. När du integrerar och analyserar Synapse SQL-poolen den enda versionen av sanningen som ditt företag kan räkna på för snabbare och mer robusta insikter.  

## <a name="key-component-of-a-big-data-solution"></a>Viktig komponent i en stor data lösning

Data lager är en viktig del av en molnbaserad stor data lösning från slut punkt till slut punkt.

![Lösning för informationslager](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

I en molndatalösning inhämtas data till lagringsplatser för stordata från olika källor. Hadoop, Spark och maskininlärningsalgoritmer förbereder och tränar data när de finns på lagringsplatser för stordata. När data är klara för komplex analys använder Synapse SQL-poolen PolyBase för att fråga de stora data lager. PolyBase använder vanliga T-SQL-frågor för att hämta data till Synapse i SQL-pooler.

Synapse SQL-pool lagrar data i Relations tabeller med kolumn lagring. Det här formatet minskar lagringskostnaderna för data avsevärt och förbättrar frågeprestanda. När data lagras kan du köra analyser på massiv skala. Jämfört med traditionella databassystem slutförs analysfrågor på några sekunder i stället för flera minuter eller på några timmar i stället för flera dagar.

Analysresultaten kan skickas till globala rapporteringsdatabaser eller program. Företagsanalytiker får sedan den information de behöver för att kunna fatta välgrundade affärsbeslut.

## <a name="next-steps"></a>Nästa steg

- Utforska [Azure Synapse-arkitekturen](massively-parallel-processing-mpp-architecture.md)
- [Skapa snabbt en SQL-pool](create-data-warehouse-portal.md)
- [Läs in exempel data](load-data-from-azure-blob-storage-using-polybase.md).
- Utforska [videor](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Eller titta på några av de andra Azure Synapse-resurserna.

- Sök efter [Bloggar](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- Skicka en [funktions förfrågan](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Skapa ett supportärende](sql-data-warehouse-get-started-create-support-ticket.md)
- Sök i [MSDN-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
- Sök [Stack Overflow forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
