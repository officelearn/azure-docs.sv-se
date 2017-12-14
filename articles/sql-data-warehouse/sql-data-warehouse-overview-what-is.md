---
title: "Vad är Azure SQL Data Warehouse? | Microsoft Docs"
description: "En distribuerad databas i företagsklass som kan bearbeta petabytevolymer med relationella och icke-relationella data. Den är branschens första informationslager i molnet som kan växa, krympa och pausa på sekunden."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jenniehubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 10/23/2017
ms.author: jrj
ms.openlocfilehash: 7fd17c29c413905084c6b3736018d111d3b28fea
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="what-is-azure-sql-data-warehouse"></a>Vad är Azure SQL Data Warehouse?

SQL Data Warehouse är ett molnbaserad företagsinformationslager som använder en MPP-modell (Massively Parallel Processing) för att snabbt köra komplexa frågor över petabyte med data. Använd SQL Data Warehouse som en viktig komponent i en lösning för stordata. Importera stordata till SQL Data Warehouse med enkla PolyBase T-SQL-frågor och utnyttja sedan kraften i MPP för att köra högpresterande analyser. Använd informationslagret en tid för att integrera och analysera och upptäck vilket fantastiskt hjälpmedel det är för att fördjupa insikterna.  


## <a name="key-component-of-big-data-solution"></a>Viktig komponent i lösning för stordata
SQL Data Warehouse är en viktig komponent i en lösning mellan slutpunkter för stordata i molnet.

![Lösning för informationslager](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

I en molndatalösning inhämtas data till lagringsplatser för stordata från olika källor. Hadoop, Spark och maskininlärningsalgoritmer förbereder och tränar data när de finns på lagringsplatser för stordata. När dessa data är färdiga för komplexa analyser använder SQL Data Warehouse PolyBase för att fråga lagringsplatserna för stordata. PolyBase använder de T-SQL-frågor som är standard för att flytta data till SQL Data Warehouse.
 
SQL Data Warehouse lagrar data i relationstabeller med kolumnbaserad lagring. Det här formatet minskar lagringskostnaderna för data avsevärt och förbättrar frågeprestanda. Du kan köra analys på massiv skala när data lagras i SQL Data Warehouse. Jämfört med traditionella databassystem slutförs analysfrågor på några sekunder i stället för flera minuter eller på några timmar i stället för flera dagar. 

Analysresultaten kan skickas till globala rapporteringsdatabaser eller program. Företagsanalytiker får sedan den information de behöver för att kunna fatta välgrundade affärsbeslut.

## <a name="optimization-choices"></a>Optimeringsalternativ

SQL Data Warehouse erbjuder [prestandanivåer](performance-tiers.md) som är utformade för flexibilitet för att kunna uppfylla dina databehov, oavsett om de är stora eller små. Du kan välja ett informationslager som är optimerat för elasticitet eller för beräkning. 

- På **prestandanivån Optimerat för elasticitet** avgränsas lagren för beräkning och lagring i arkitekturen. Det här alternativet är perfekt för arbetsbelastningar som kan dra full nytta av avgränsningen mellan beräkning och lagring genom frekvent skalning för bättre stöd för korta perioder med hög aktivitet. Den här beräkningsnivån har det lägsta startpriset och stöd för skalning som omfattar merparten av kundens arbetsbelastningar.

- På **prestandanivån Optimerat för beräkning** används den senaste Azure-maskinvaran för att införa ett nytt NVMe-Solid State Disk-cacheminne som lagrar de data som ofta används nära processorerna. Den här prestandanivån är perfekt för komplexa frågor eftersom all I/O sparas lokalt på beräkningslagret genom att lagringen automatiskt nivåindelas. Dessutom har kolumnlagringen förbättrats för att lagra obegränsad data i ditt SQL Data Warehouse. Prestandanivån Optimerat för beräkning ger störst skalbarhet och hjälper dig att skala upp till 30 000 cDWU (informationslagerenheter för beräkning). Välj den här nivån för arbetsbelastningar som kräver kontinuerlig och blixtsnabb prestanda.

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
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/en-us/support/legal/sla/
