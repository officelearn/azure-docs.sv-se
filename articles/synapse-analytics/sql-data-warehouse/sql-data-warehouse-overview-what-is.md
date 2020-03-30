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
ms.openlocfilehash: a118d028cc85eb858dd0dc1fa6d5d2268f7db43b
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350394"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Vad är Azure Synapse Analytics (tidigare SQL DW)?

Azure Synapse är en obegränsad analystjänst som sammanför informationslager i företagsklass och stordataanalys. Det ger dig friheten att fråga efter data på dina villkor, med hjälp av antingen serverlösa resurser på begäran eller etablerade resurser – i stor skala. Azure Synapse sammanför dessa två världar med en enhetlig upplevelse för att inta, förbereda, hantera och hantera data för omedelbara bi- och maskininlärningsbehov

Azure Synapse har fyra komponenter:
- SQL Analytics: Komplett T-SQL-baserad analys – allmänt tillgänglig
    - SQL-pool (betala per DWU-etablerad) 
    - SQL on-demand (betala per TB bearbetad) – (preview)
- Spark: Djupt integrerad Apache Spark (Preview) 
- Dataintegration: Integrering av hybriddata (förhandsversion)
- Studio: Enhetlig användarupplevelse.  (Förhandsversion)

> [!NOTE]
> Om du vill komma åt förhandsversionen av Azure Synapse ber du om åtkomst [här](https://aka.ms/synapsepreview). Microsoft kommer att triage alla förfrågningar och svara så snart som möjligt.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>SQL Analytics och SQL-pool i Azure Synapse

SQL Analytics refererar till de funktioner för lagring av företagsdata som är allmänt tillgängliga i Azure Synapse. 

SQL-poolen representerar en samling analytiska resurser som etableras när SQL Analytics används. Storleken på SQL-poolen bestäms av Data Lagerenheter (DWU).

Importera stordata med enkla [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL-frågor och använd sedan kraften i MPP för att köra högpresterande analyser. När du integrerar och analyserar blir SQL Analytics den enda version av sanningen som ditt företag kan lita på för snabbare och mer robusta insikter.  

## <a name="key-component-of-a-big-data-solution"></a>Nyckelkomponent i en stordatalösning

Datalagring är en viktig komponent i en molnbaserad, heltäckande stordatalösning.

![Lösning för informationslager](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

I en molndatalösning inhämtas data till lagringsplatser för stordata från olika källor. Hadoop, Spark och maskininlärningsalgoritmer förbereder och tränar data när de finns på lagringsplatser för stordata. När data är klara för komplex analys används PolyBase av SQL Analytics för att köra frågor mot stordatalagren. PolyBase använder standardmässiga T-SQL-frågor för att hämta data till SQL Analytics-tabeller.
 
SQL Analytics lagrar data i relationstabeller med kolumnbaserad lagring. Det här formatet minskar lagringskostnaderna för data avsevärt och förbättrar frågeprestanda. När data lagras kan du köra analyser på massiv skala. Jämfört med traditionella databassystem slutförs analysfrågor på några sekunder i stället för flera minuter eller på några timmar i stället för flera dagar. 

Analysresultaten kan skickas till globala rapporteringsdatabaser eller program. Företagsanalytiker får sedan den information de behöver för att kunna fatta välgrundade affärsbeslut.

## <a name="next-steps"></a>Nästa steg

- Utforska [Azure Synapse-arkitektur](massively-parallel-processing-mpp-architecture.md)
- Skapa snabbt [en SQL-pool](create-data-warehouse-portal.md)
- [Läs in exempeldata](load-data-from-azure-blob-storage-using-polybase.md).
- Utforska [videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Eller titta på några av dessa andra Azure Synapse-resurser.  
* Sök [bloggar](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* Skicka in en [funktionsförfrågningar](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Skapa ett supportärende](sql-data-warehouse-get-started-create-support-ticket.md)
* Sök [MSDN forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
* Forum [för sökstackspill](https://stackoverflow.com/questions/tagged/azure-sqldw)
