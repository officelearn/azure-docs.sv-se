---
title: Vad är Azure Synapse Analytics (tidigare SQL DW)?
description: Azure Synapse Analytics (tidigare SQL DW) är en obegränsad analystjänst som sammanför företagsdatalagring och Big Data-analys.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a9506d45350a567e3643b6edd6afc7668662f6e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416032"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Vad är Azure Synapse Analytics (tidigare SQL DW)?

Azure Synapse är en analystjänst som sammanför företagsdatalagring och Big Data-analys. Det ger dig friheten att fråga efter data på dina villkor, med hjälp av antingen serverlösa resurser på begäran eller etablerade resurser – i stor skala. Azure Synapse sammanför dessa två världar med en enhetlig upplevelse för att inta, förbereda, hantera och hantera data för omedelbara bi- och maskininlärningsbehov.

Azure Synapse har fyra komponenter:

- Synapse SQL: Komplett T-SQL-baserad analys – allmänt tillgänglig
  - SQL-pool (betala per DWU-etablerad)
  - SQL on-demand (betala per TB bearbetad) – (preview)
- Spark: Djupt integrerad Apache Spark (Preview)
- Synaps pipelines: Hybrid dataintegrering (Preview)
- Studio: Enhetlig användarupplevelse.  (Förhandsversion)

> [!NOTE]
> Om du vill komma åt förhandsversionen av Azure Synapse ber du om åtkomst [här](https://aka.ms/synapsepreview). Microsoft kommer att triage alla förfrågningar och svara så snart som möjligt.
>
> Visa [förhandsversionen av Azure Synapse](../overview-what-is.md).

## <a name="synapse-sql-pool-in-azure-synapse"></a>Synapse SQL-pool i Azure Synapse

Synapse SQL-pool refererar till de funktioner för företagsdatalagring som är allmänt tillgängliga i Azure Synapse.

SQL-poolen representerar en samling analytiska resurser som etableras när Du använder Synapse SQL. Storleken på SQL-poolen bestäms av Data Lagerenheter (DWU).

Importera stordata med enkla [PolyBase](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL-frågor och använd sedan kraften i MPP för att köra högpresterande analyser. När du integrerar och analyserar blir Synapse SQL-pool den enda version av sanningen som ditt företag kan lita på för snabbare och mer robusta insikter.  

## <a name="key-component-of-a-big-data-solution"></a>Nyckelkomponent i en stordatalösning

Datalagring är en viktig komponent i en molnbaserad, heltäckande stordatalösning.

![Lösning för informationslager](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

I en molndatalösning inhämtas data till lagringsplatser för stordata från olika källor. Hadoop, Spark och maskininlärningsalgoritmer förbereder och tränar data när de finns på lagringsplatser för stordata. När data är redo för komplex analys använder Synapse SQL-pool PolyBase för att fråga stordatalager. PolyBase använder vanliga T-SQL-frågor för att föra in data i Synapse SQL-pooltabeller.

Synapse SQL-pool lagrar data i relationstabeller med columnar-lagring. Det här formatet minskar lagringskostnaderna för data avsevärt och förbättrar frågeprestanda. När data lagras kan du köra analyser på massiv skala. Jämfört med traditionella databassystem slutförs analysfrågor på några sekunder i stället för flera minuter eller på några timmar i stället för flera dagar.

Analysresultaten kan skickas till globala rapporteringsdatabaser eller program. Företagsanalytiker får sedan den information de behöver för att kunna fatta välgrundade affärsbeslut.

## <a name="next-steps"></a>Nästa steg

- Utforska [Azure Synapse-arkitektur](massively-parallel-processing-mpp-architecture.md)
- Skapa snabbt [en SQL-pool](create-data-warehouse-portal.md)
- [Läs in exempeldata](load-data-from-azure-blob-storage-using-polybase.md).
- Utforska [videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Eller titta på några av dessa andra Azure Synapse-resurser.

- Sök [bloggar](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- Skicka in en [funktionsförfrågningar](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Skapa ett supportärende](sql-data-warehouse-get-started-create-support-ticket.md)
- Sök [MSDN forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
- Forum [för sökstackspill](https://stackoverflow.com/questions/tagged/azure-sqldw)
