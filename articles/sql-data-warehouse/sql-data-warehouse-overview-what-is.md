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
ms.openlocfilehash: 07bf3070e42af8a03ce1fd7ea4445fc76557e8a3
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645519"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Vad är Azure Synapse Analytics (tidigare SQL DW)?

Azure Synapse är en obegränsad analys tjänst som sammanför företags data lager hantering och stor data analys. Det ger dig friheten att fråga efter data på dina villkor, med hjälp av antingen server utan på begäran eller etablerade resurser – i stor skala. Azure Synapse kombinerar de här två världar tillsammans med en enhetlig upplevelse för att mata in, förbereda, hantera och hantera data för omedelbara BI-och maskin inlärnings behov

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

SQL Analytics syftar på de företags data lager funktioner som är allmänt tillgängliga med Azure Synapse. 

SQL-poolen representerar en samling analys resurser som tillhandahålls när du använder SQL Analytics. Storleken på SQL-poolen bestäms av data lager enheter (DWU).

Importera Big data med enkla [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL-frågor och Använd sedan kraften i MPP för att köra analys med höga prestanda. När du integrerar och analyserar, kommer SQL Analytics att bli den enda versionen av sanningen som ditt företag kan räkna med för snabbare och mer robusta insikter.  

## <a name="key-component-of-a-big-data-solution"></a>Viktig komponent i en stor data lösning

Data lager är en viktig del av en molnbaserad stor data lösning från slut punkt till slut punkt.

![Lösning för informationslager](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

I en molndatalösning inhämtas data till lagringsplatser för stordata från olika källor. Hadoop, Spark och maskininlärningsalgoritmer förbereder och tränar data när de finns på lagringsplatser för stordata. När data är klara för komplex analys använder SQL Analytics PolyBase för att fråga stora data lager. PolyBase använder vanliga T-SQL-frågor för att hämta data till SQL Analytics-tabeller.
 
SQL Analytics lagrar data i Relations tabeller med kolumn lagring. Det här formatet minskar lagringskostnaderna för data avsevärt och förbättrar frågeprestanda. När data har lagrats kan du köra analyser i massiv skala. Jämfört med traditionella databassystem slutförs analysfrågor på några sekunder i stället för flera minuter eller på några timmar i stället för flera dagar. 

Analysresultaten kan skickas till globala rapporteringsdatabaser eller program. Företagsanalytiker får sedan den information de behöver för att kunna fatta välgrundade affärsbeslut.

## <a name="next-steps"></a>Nästa steg

- Utforska [Azure Synapse-arkitekturen](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- [Skapa snabbt en SQL-pool](create-data-warehouse-portal.md)
- [Läs in exempel data][load sample data].
- Utforska [videor](/azure/sql-data-warehouse/sql-data-warehouse-videos)

Eller titta på några av de andra Azure Synapse-resurserna.  
* Sök efter [Bloggar]
* Skicka en [Funktionsbegäranden]
* Sök efter [Customer Advisory Team-bloggar]
* [Skapa ett supportärende]
* Sök i [MSDN-forum]
* Sök [Stack Overflow-forum]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Skapa ett supportärende]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a data warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse Analytics solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-bloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-forum]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow-forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for Azure Synapse Analytics]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
