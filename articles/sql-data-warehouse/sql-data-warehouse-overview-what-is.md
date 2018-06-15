---
title: Vad är Azure SQL Data Warehouse? | Microsoft Docs
description: En distribuerad databas i företagsklass som kan bearbeta petabytevolymer med relationella och icke-relationella data. Den är branschens första informationslager i molnet som kan växa, krympa och pausa på sekunden.
services: sql-data-warehouse
author: igorstanko
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: overview
ms.component: design
ms.date: 04/17/2018
ms.author: igorstan
ms.reviewer: igorstan
ms.openlocfilehash: c8e6af4eabc96845ff6c237735cdb0da2ed24743
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185985"
---
# <a name="what-is-azure-sql-data-warehouse"></a>Vad är Azure SQL Data Warehouse?

SQL Data Warehouse är ett molnbaserad företagsinformationslager som använder en MPP-modell (Massively Parallel Processing) för att snabbt köra komplexa frågor över petabyte med data. Använd SQL Data Warehouse som en viktig komponent i en lösning för stordata. Importera stordata till SQL Data Warehouse med enkla PolyBase T-SQL-frågor och utnyttja sedan kraften i MPP för att köra högpresterande analyser. Använd informationslagret en tid för att integrera och analysera och upptäck vilket fantastiskt hjälpmedel det är för att fördjupa insikterna.  


## <a name="key-component-of-big-data-solution"></a>Viktig komponent i lösning för stordata
SQL Data Warehouse är en viktig komponent i en lösning mellan slutpunkter för stordata i molnet.

![Lösning för informationslager](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

I en molndatalösning inhämtas data till lagringsplatser för stordata från olika källor. Hadoop, Spark och maskininlärningsalgoritmer förbereder och tränar data när de finns på lagringsplatser för stordata. När dessa data är färdiga för komplexa analyser använder SQL Data Warehouse PolyBase för att fråga lagringsplatserna för stordata. PolyBase använder de T-SQL-frågor som är standard för att flytta data till SQL Data Warehouse.
 
SQL Data Warehouse lagrar data i relationstabeller med kolumnbaserad lagring. Det här formatet minskar lagringskostnaderna för data avsevärt och förbättrar frågeprestanda. Du kan köra analys på massiv skala när data lagras i SQL Data Warehouse. Jämfört med traditionella databassystem slutförs analysfrågor på några sekunder i stället för flera minuter eller på några timmar i stället för flera dagar. 

Analysresultaten kan skickas till globala rapporteringsdatabaser eller program. Företagsanalytiker får sedan den information de behöver för att kunna fatta välgrundade affärsbeslut.


## <a name="next-steps"></a>Nästa steg
Nu när du vet lite om SQL Data Warehouse kan du gå vidare och se hur du snabbt [skapar ett SQL Data Warehouse][create a SQL Data Warehouse] och [läsa in exempeldata][load sample data]. Om du inte har erfarenhet av Azure kan [Azure-ordlistan][Azure glossary] vara till hjälp om du stöter på ny terminologi. Eller så kan du se över några av de övriga SQL Data Warehouse-resurserna.  

* [Kundernas framgångsberättelser]
* [Bloggar]
* [Funktionsbegäranden]
* [Videoklipp]
* [Customer Advisory Team-bloggar]
* [Skapa ett supportärende]
* [MSDN-forum]
* [Stack Overflow-forum]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Skapa ett supportärende]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Kundernas framgångsberättelser]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-bloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-forum]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow-forum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
