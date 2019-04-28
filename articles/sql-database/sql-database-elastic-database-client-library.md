---
title: Skapa skalbara molndatabaser | Microsoft Docs
description: Bygga skalbara .NET-databas med klientbiblioteket för elastiska databaser
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: c0d50f3a66d940618f2bc421537b113120a2eaca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475872"
---
# <a name="building-scalable-cloud-databases"></a>Skapa skalbara molndatabaser

Skala ut databaser kan enkelt utföras med hjälp av skalbara verktyg och funktioner för Azure SQL Database. Framför allt, du kan använda den **Elastic Database-klientbiblioteket** att skapa och hantera utskalade databaser. Den här funktionen kan du enkelt utveckla fragmenterade (sharded) program med hundratals – eller till och med tusentals – av Azure SQL-databaser. [Elastiska jobb](sql-database-elastic-jobs-powershell.md) kan sedan användas för att enkelt hantering av dessa databaser.

Så här hämtar:

* Java-versionen av biblioteket, se [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* .NET-versionen av biblioteket, se [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentation

1. [Kom igång med Elastic Database-verktyg](sql-database-elastic-scale-get-started.md)
2. [Elastic Database-funktioner](sql-database-elastic-scale-introduction.md)
3. [Karthantering för shard](sql-database-elastic-scale-shard-map-management.md)
4. [Migrera befintliga databaser för att skala ut](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Databeroende routning](sql-database-elastic-scale-data-dependent-routing.md)
6. [Multi-shard-frågor](sql-database-elastic-scale-multishard-querying.md)
7. [Att lägga till en shard med elastiska Databasverktyg](sql-database-elastic-scale-add-a-shard.md)
8. [Program för flera innehavare med elastic database-verktyg och säkerhet på radnivå](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Uppgradera klienten biblioteket appar](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Översikt över elastiska frågor](sql-database-elastic-query-overview.md)
11. [Ordlista för verktyg i elastiska databaser](sql-database-elastic-scale-glossary.md)
12. [Elastic Database-klientbibliotek med Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Elastic database-klientbibliotek med Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Verktyget för dela / sammanslå](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Prestandaräknare för karthanteraren för shard](sql-database-elastic-database-client-library.md) 
16. [Vanliga frågor och svar för elastiska Databasverktyg](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Funktioner i klientprogrammet

Skala ut program med hjälp av *horisontell partitionering* medför utmaningar för såväl utvecklaren som administratören. Klientbiblioteket förenklar administrativa uppgifter genom att tillhandahålla verktyg som låter både utvecklare och administratörer hantera utskalade databaser. Det finns många databaser som kallas ”shards”, för att hantera i ett typexempel. Kunder som är samordnad i samma databas och det finns en databas per kund (en enda klient schemat). Klientbiblioteket innehåller följande funktioner:

- **Fragmentkarthantering**: En särskild databas som heter ”fragmentkartehanteraren” har skapats. Fragmentkarthantering är möjligheten för ett program för att hantera metadata om dess shards. Utvecklare kan använda den här funktionen för att registrera databaser som shards, beskriver mappningar av enskilda horisontell partitionering nycklar eller nyckelintervall till dessa databaser och underhålla dessa metadata med antalet och sammansättning av databaser som utvecklas för att återspegla ändringar i kapacitet. Utan elastic database-klientbiblioteket, skulle du behöva ta lite tid att skriva management-kod när du implementerar horisontell partitionering. Mer information finns i [fragmentkarthantering](sql-database-elastic-scale-shard-map-management.md).

- **Databeroende routning**: Anta att en begäran som kommer in i programmet. Baserat på horisontell partitionering nyckelvärdet för begäran kan måste programmet fastställa rätt databas baserat på nyckelvärdet. Sedan öppnas en anslutning till databasen för att bearbeta begäran. Databeroende routning ger möjlighet att öppna anslutningar med ett enda enkelt anrop till fragmentkartan för programmet. Databeroende routning har ett annat område i infrastrukturkod som omfattas nu av funktioner i klientbiblioteket för elastiska databaser. Mer information finns i [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md).
- **Multi-shard-frågor (MSQ)**: Multi-shard-frågor fungerar när en begäran omfattar flera (eller alla) shards. En Multi-shard-fråga körs samma T-SQL-kod på alla shards eller en uppsättning shards. Resultaten från deltagande shards slås samman i en övergripande resultatmängden med hjälp av UNION ALL-semantik. Funktioner som exponeras via klientbiblioteket hanterar många aktiviteter, inklusive: anslutningshanteringen, tråd management, hantering av fel och mellanresultat bearbetning. MSQ kan fråga upp till hundratals shards. Mer information finns i [Multi-shard-frågor](sql-database-elastic-scale-multishard-querying.md).

I allmänhet kunder som använder elastic database-verktyg kan förvänta sig att hämta alla T-SQL-funktioner när du skickar in shard-lokala åtgärder i stället för mellan fragment åtgärder som har sina egna semantik.



## <a name="next-steps"></a>Nästa steg

- Elastic Database-klientbibliotek ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) – för att **hämta** i biblioteket.

- [Kom igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md) – om du vill prova den **exempelapp** som visar klientfunktioner.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) – om du vill göra bidrag i koden.
- [Översikt över elastisk fråga i Azure SQL Database](sql-database-elastic-query-overview.md) – om du vill använda elastiska frågor.

- [Flytta data mellan utskalade molndatabaser](sql-database-elastic-scale-overview-split-and-merge.md) – anvisningar om hur du använder den **dela / sammanslå verktyget**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

