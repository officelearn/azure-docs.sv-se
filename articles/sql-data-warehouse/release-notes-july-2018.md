---
title: Azure SQL Data Warehouse viktig juli 2018 | Microsoft Docs
description: Viktig information för Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/06/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: c7d2211ca69fcd18588ea1b20b638b2970b8439c
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318851"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Vad är nytt i Azure SQL Data Warehouse? Juli 2018
Azure SQL Data Warehouse tar emot förbättringar kontinuerligt. Den här artikeln beskriver nya funktioner och ändringar som har införts i juli 2018.

## <a name="lightning-fast-query-performance"></a>Blixtrande snabbt frågeprestanda
[Azure SQL Data Warehouse](https://aka.ms/sqldw) anger nya prestandamått med introduktionen av direktåtkomst till Data som förbättrar blanda åtgärder. Direktåtkomst till Data minskar CPU-användningen för dataflyttningsåtgärder genom att använda direkt SQL-Server till SQL Server native dataåtgärder. Integrering med SQL Server-motorn direkt för dataförflyttning innebär det att SQL Data Warehouse är nu **67% snabbare än Amazon Redshift** genom att använda en arbetsbelastning fås från välkänd branschstandard [TPC Benchmark™ H (TPC-H)](http://www.tpc.org/tpch/).

![Azure SQL Data Warehouse är snabbare och billigare än Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/eb3b908a-464d-4847-b384-9f296083a737.png)
<sub>källa: [Gigaom Forskningsrapport analytiker: datalager i molnet-Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

Utöver runtime-prestanda i [Gigaom Research](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) rapporten också mäts prisprestanda förhållandet för att kvantifiera USD kostnaden för specifika arbetsbelastningar. SQL Data Warehouse har **minst 23 procent mindre** än Redshift för 30 TB arbetsbelastningar. Med SQL Data Warehouse möjligheten att Elastiskt skala databearbetning och pausa och återuppta arbetsbelastningar kan kunderna betalar bara när tjänsten är i användning, ytterligare minska sina kostnader.
![Azure SQL Data Warehouse är snabbare och billigare än Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/cb76447e-621e-414b-861e-732ffee5345a.png)
<sub>källa: [Gigaom Forskningsrapport analytiker: datalager i molnet-Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

### <a name="query-concurrency"></a>Fråga samtidighet
SQL Data Warehouse innebär också att data är tillgängliga i ditt företag. Microsoft har förbättrats tjänsten för att stödja 128 samtidiga frågor så att fler användare kan söka i samma databasen och inte blockeras av andra förfrågningar. Däremot begränsar Amazon Redshift maximalt antal samtidiga frågor till 50, begränsa åtkomst till data i organisationen.

SQL Data Warehouse ger fråga prestanda och fråga samtidighet fördelarna utan någon prisökning och bygga på dess unika arkitektur med fristående lagringsutrymme och databearbetning.

## <a name="finer-granularity-for-cross-region-and-server-restores"></a>Mer detaljerad information mellan regioner och server återställningar
Du kan nu återställa i regioner och servrar med hjälp av valfri återställningspunkt istället för att välja geo-redundanta säkerhetskopieringar som vidtas var 24: e timme. Mellan regioner och server återställning har stöd för både en användardefinierad eller automatisk återställningspunkter som aktiverar finare granularitet för ytterligare dataskydd. Med flera återställningspunkter är tillgängliga, du kan vara säker på att ditt informationslager är logiskt konsekvent när du återställer i olika regioner.

![Restore-kommandots – Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![alternativ för återställningen – Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

Mer information finns i den [Accelerated och flexibel återställningspunkter](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) blogginlägg.

## <a name="20-minute-restorations"></a>20 minuter lång återställningar
SQL Data Warehouse erbjuder nu återställningen av alla informationslager **mindre än 20 minuter** inom samma region oavsett databasens storlek. Tiden för återställning gäller oavsett om återställningen till samma eller en annan logisk server inom samma region. Dessutom har ögonblicksbildprocessen förbättrats för att minska den tid det tar för att skapa en återställningspunkt. I lägre prestandanivåer (lägre DWU-inställningar) förbättring är en *2 x i minskad* för ögonblicksbilder skapas.

Mer information finns i den [Accelerated och flexibel återställningspunkter](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) blogginlägg.

## <a name="custom-restoration-configurations"></a>Återställning av den anpassade konfigurationer
Du kan nu ändra din prestandanivå (DWU) när du återställer i Azure-portalen. Du kan även återställa till en uppgraderad Gen2 data warehouse. Genom att återställa till en Gen 2-instans, kan du nu utvärdera effekten av Gen2 innan [uppgradera informationslagret Gen1](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation).

![Anpassad återställning-konfiguration – Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>FJÄRRPROCEDURANROPET
Den [Fjärrproceduranropet](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) lagrad procedur används ofta av verktyg för att hämta metadata om parametrar i Transact-SQL-batch. Metoden returnerar en rad för varje parameter i batch med härleda typinformation för parametern. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

Resultatuppsättningen innehåller metadata om den `@id` parametern (ofullständiga resultat visas)
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```
## <a name="sprefreshsqlmodule"></a>SP_REFRESHSQLMODULE
Den [sp_refreshsqlmodule](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-refreshsqlmodule-transact-sql) lagrade proceduren uppdaterar metadata för ett databasobjekt om underliggande metadata har blivit föråldrade på grund av ändringar av underliggande objekt. Detta kan inträffa om bastabeller för en vy ändras och vyn inte har återskapats. Detta sparar steget i att släppa och återskapa beroende objekt.

Exemplet nedan visar en vy som blir inaktuella på grund av den underliggande tabellen ändringen. Här ser du att informationen är korrekt för den första kolumn ändringen (1 till Mollie) men kolumnnamnet är ogiltigt och den andra kolumnen finns inte. 
```sql
CREATE TABLE base_table (Id INT);
GO

INSERT INTO base_table (Id) VALUES (1);
GO

CREATE VIEW base_view AS SELECT * FROM base_table;
GO

SELECT * FROM base_view;
GO

-- Id
-- ----
-- 1

DROP TABLE base_table;
GO

CREATE TABLE base_table (fname VARCHAR(10), lname VARCHAR(10));
GO

INSERT INTO base_table (fname, lname) VALUES ('Mollie', 'Gallegos');
GO

SELECT * FROM base_view;
GO

-- Id
-- ----------
-- Mollie

EXEC sp_refreshsqlmodule @Name = 'base_view';
GO

SELECT * FROM base_view;
GO

-- fname     | lname
-- ---------- ----------
-- Mollie    | Gallegos
```

## <a name="next-steps"></a>Nästa steg
Nu när du vet lite om SQL Data Warehouse, lär du dig hur du snabbt [skapa ett SQL Data Warehouse][create a SQL Data Warehouse]. Om du inte har erfarenhet av Azure kan [Azure-ordlistan][Azure glossary] vara till hjälp om du stöter på ny terminologi. Eller så kan du se över några av de övriga SQL Data Warehouse-resurserna.  

* [Kundernas framgångsberättelser]
* [Bloggar]
* [Funktionsbegäranden]
* [Videoklipp]
* [Customer Advisory Team-bloggar]
* [Stack Overflow-forum]
* [Twitter]


[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-bloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Kundernas framgångsberättelser]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-forum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
