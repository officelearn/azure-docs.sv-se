---
title: Vad är Azure Synapse Analytics (tidigare SQL DW)?
description: Azure Synapse Analytics (tidigare SQL DW) är en obegränsad analys tjänst som sammanför företags data lager hantering och stor data analys.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 8840791c7b18d1efa499c2826a6eaf041a6da787
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93317487"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Vad är Azure Synapse Analytics (tidigare SQL DW)?

> [!NOTE]
>Utforska [dokumentationen för Azure Synapse (arbets ytans för hands version)](../overview-what-is.md).
>

Azure Synapse är en analys tjänst som samlar ihop företags data lager hantering och stor data analys. Det ger dig friheten att fråga efter data på dina villkor, med hjälp av antingen serverlösa resurser på begäran eller etablerade resurser – i stor skala. Azure Synapse sammanför de två världarna med en enhetlig upplevelse för att mata in, förbereda, hantera och hämta data för omedelbara BI- och maskininlärningsbehov.

Azure-Synapse har fyra komponenter:

- Synapse SQL: fullständig T-SQL-baserad analys – allmänt tillgänglig
  - Dedikerad SQL-pool (betala per DWU etablerad)
  - SQL-pool utan server (betala per TB bearbetad) (för hands version)
- Spark: djupt integrerad Apache Spark (förhands granskning)
- Synapse-pipeline: hybrid data integrering (för hands version)
- Studio: enhetlig användar upplevelse. (förhandsversion)

## <a name="dedicated-sql-pool-in-azure-synapse"></a>Dedikerad SQL-pool i Azure Synapse

Dedikerad SQL-pool syftar på de företags data lager funktioner som är allmänt tillgängliga i Azure Synapse.

Dedikerad SQL-pool representerar en samling analys resurser som tillhandahålls när du använder Synapse SQL. Storleken på en dedikerad SQL-pool bestäms av data lager enheter (DWU).

Importera Big data med enkla [PolyBase](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL-frågor och Använd sedan kraften i den distribuerade Frågeredigeraren för att köra analys med höga prestanda. När du integrerar och analyserar data, blir Synapse SQL den enda versionen av sanningen som ditt företag kan räkna på för snabbare och mer robusta insikter. 

## <a name="key-component-of-a-big-data-solution"></a>Viktig komponent i en stor data lösning

Data lager är en viktig del av en molnbaserad stor data lösning från slut punkt till slut punkt.

![Lösning för informationslager](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

I en lösning för molndata matas data in i stordatalager från olika källor. När data lagras i ett stordatalager förbereder och tränar Hadoop, Spark och maskininlärningsalgoritmer dessa data. När data är klara för komplex analys använder dedicerad SQL-pool PolyBase för att fråga stora data lager. PolyBase använder vanliga T-SQL-frågor för att hämta data till dedikerade tabeller i SQL-pooler.

Dedikerad SQL-pool lagrar data i Relations tabeller med kolumn lagring. Det här formatet minskar kostnaderna för datalagring avsevärt och förbättrar frågeprestanda. När data lagras kan du köra analyser på massiv skala. Jämfört med traditionella databassystem slutförs analysfrågor på några sekunder i stället för flera minuter, eller på några timmar i stället för flera dagar.

Analysresultaten kan skickas till globala rapporteringsdatabaser eller program. Företagsanalytiker får sedan den information de behöver för att kunna fatta välgrundade affärsbeslut.

## <a name="next-steps"></a>Nästa steg

- Utforska [Azure Synapse-arkitekturen](massively-parallel-processing-mpp-architecture.md)
- [Skapa en dedikerad SQL-pool](create-data-warehouse-portal.md) snabbt
- [Läs in exempel data](load-data-from-azure-blob-storage-using-polybase.md).
- Utforska [videor](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Eller titta på några av de andra Azure Synapse-resurserna.

- Sök efter [Bloggar](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- Skicka en [funktions förfrågan](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Skapa ett support ärende](sql-data-warehouse-get-started-create-support-ticket.md)
- Sök på [sidan Microsoft Q&en fråga](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
- Sök [Stack Overflow forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
