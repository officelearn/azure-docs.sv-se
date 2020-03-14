---
title: Vad är Azure Synapse Analytics (tidigare SQL DW)?
description: Azure Synapse Analytics (tidigare SQL DW) är en obegränsad analys tjänst som sammanför företags data lager hantering och stor data analys.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 68d39b4f363794d50fd05c2067502fc55d5d0170
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240578"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Vad är Azure Synapse Analytics (tidigare SQL DW)?

Azure Synapse är en obegränsad analystjänst som sammanför informationslager i företagsklass och stordataanalys. Det ger dig friheten att fråga efter data på dina villkor, med hjälp av antingen serverlösa resurser på begäran eller etablerade resurser – i stor skala. Azure Synapse kombinerar de här två världar tillsammans med en enhetlig upplevelse för att mata in, förbereda, hantera och hantera data för omedelbara BI-och maskin inlärnings behov

Azure-Synapse har fyra komponenter:
- SQL Analytics: fullständig T-SQL-baserad analys – allmänt tillgänglig
    - SQL-pool (betala per DWU etablerad) 
    - SQL på begäran (betala per TB bearbetat) – (för hands version)
- Spark: djupt integrerad Apache Spark (förhands granskning) 
- Data integrering: hybrid data integrering (för hands version)
- Studio: enhetlig användar upplevelse.  (Förhandsversion)

> [!NOTE]
> För att få åtkomst till för hands versions funktionerna i Azure Synapse, begär åtkomst [här](https://aka.ms/synapsepreview). Microsoft kommer att prioritering alla begär Anden och svara så snart som möjligt.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>SQL Analytics och SQL-pool i Azure Synapse

SQL Analytics syftar på de företags data lager funktioner som är allmänt tillgängliga i Azure Synapse. 

SQL-poolen representerar en samling analys resurser som tillhandahålls när du använder SQL Analytics. Storleken på SQL-poolen bestäms av data lager enheter (DWU).

Importera Big data med enkla [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL-frågor och Använd sedan kraften i MPP för att köra analys med höga prestanda. När du integrerar och analyserar, kommer SQL Analytics att bli den enda versionen av sanningen som ditt företag kan räkna med för snabbare och mer robusta insikter.  

## <a name="key-component-of-a-big-data-solution"></a>Viktig komponent i en stor data lösning

Data lager är en viktig del av en molnbaserad stor data lösning från slut punkt till slut punkt.

![Lösning för informationslager](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

I en molndatalösning inhämtas data till lagringsplatser för stordata från olika källor. Hadoop, Spark och maskininlärningsalgoritmer förbereder och tränar data när de finns på lagringsplatser för stordata. När data är klara för komplex analys använder SQL Analytics PolyBase för att fråga stora data lager. PolyBase använder vanliga T-SQL-frågor för att hämta data till SQL Analytics-tabeller.
 
SQL Analytics lagrar data i Relations tabeller med kolumn lagring. Det här formatet minskar lagringskostnaderna för data avsevärt och förbättrar frågeprestanda. När data har lagrats kan du köra analyser i massiv skala. Jämfört med traditionella databassystem slutförs analysfrågor på några sekunder i stället för flera minuter eller på några timmar i stället för flera dagar. 

Analysresultaten kan skickas till globala rapporteringsdatabaser eller program. Företagsanalytiker får sedan den information de behöver för att kunna fatta välgrundade affärsbeslut.

## <a name="next-steps"></a>Nästa steg

- Utforska [Azure Synapse-arkitekturen](massively-parallel-processing-mpp-architecture.md)
- [Skapa snabbt en SQL-pool](create-data-warehouse-portal.md)
- [Läs in exempel data](sql-data-warehouse-load-sample-databases.md).
- Utforska [videor](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Eller titta på några av de andra Azure Synapse-resurserna.  
* Sök efter [Bloggar](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* Skicka en [funktions förfrågan](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Skapa ett supportärende](sql-data-warehouse-get-started-create-support-ticket.md)
* Sök i [MSDN-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
* Sök [Stack Overflow forum](https://stackoverflow.com/questions/tagged/azure-sqldw)