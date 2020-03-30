---
title: Skapa skalbara molndatabaser
description: Skapa skalbara .NET-databasappar med klientbiblioteket för elastisk databas
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/25/2018
ms.openlocfilehash: ae26f669ddbe2cc2c5b6e25a9c1c0229e88dc2e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823912"
---
# <a name="building-scalable-cloud-databases"></a>Skapa skalbara molndatabaser

Skala ut databaser kan enkelt utföras med skalbara verktyg och funktioner för Azure SQL Database. Du kan särskilt använda **klientbiblioteket för elastisk databas** för att skapa och hantera utskalade databaser. Med den här funktionen kan du enkelt utveckla fragmenterade program med hundratals eller till och med tusentals Azure SQL-databaser.

Så här laddar du ned:

* Java-versionen av biblioteket, se [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* .NET-versionen av biblioteket, se [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentation

1. [Kom igång med Elastic Database-verktyg](sql-database-elastic-scale-get-started.md)
2. [Elastiska databasfunktioner](sql-database-elastic-scale-introduction.md)
3. [Karthantering för shard](sql-database-elastic-scale-shard-map-management.md)
4. [Migrera befintliga databaser för att skala ut](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Databeroende routning](sql-database-elastic-scale-data-dependent-routing.md)
6. [Multi-shard-frågor](sql-database-elastic-scale-multishard-querying.md)
7. [Lägga till en shard med hjälp av elastiska databasverktyg](sql-database-elastic-scale-add-a-shard.md)
8. [Program med flera innehavare med elastiska databasverktyg och säkerhet på radnivå](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Uppgradera klientbiblioteksappar](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Översikt över elastiska frågor](sql-database-elastic-query-overview.md)
11. [Ordlista för verktyg i elastiska databaser](sql-database-elastic-scale-glossary.md)
12. [Klientbibliotek för elastisk databas med entitetsramverk](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Klientbibliotek för elastisk databas med Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Verktyg för delad koppling](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Prestandaräknare för karthanteraren för shard](sql-database-elastic-database-client-library.md) 
16. [Vanliga frågor och svar om elastiska databasverktyg](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Klientfunktioner

Att skala ut program med hjälp av *sharding* innebär utmaningar för både utvecklaren och administratören. Klientbiblioteket förenklar hanteringsuppgifterna genom att tillhandahålla verktyg som gör att både utvecklare och administratörer kan hantera utskalade databaser. I ett typiskt exempel finns det många databaser, så kallade "shards", att hantera. Kunderna finns i samma databas och det finns en databas per kund (ett system med en enda klient). Klientbiblioteket innehåller följande funktioner:

- **Shard Map Management**: En särskild databas som kallas "shard map manager" skapas. Shard map management är möjligheten för ett program att hantera metadata om dess shards. Utvecklare kan använda den här funktionen för att registrera databaser som shards, beskriva mappningar av enskilda sharding nycklar eller nyckelområden till dessa databaser, och underhålla dessa metadata som antalet och sammansättningen av databaser utvecklas för att återspegla kapacitetsändringar. Utan den elastiska databasen klientbibliotek, skulle du behöva spendera mycket tid att skriva hanteringskoden när du implementerar fragmentering. Mer information finns i [Hantering av fragmentkart .](sql-database-elastic-scale-shard-map-management.md)

- **Databeroende routning**: Tänk dig en begäran som kommer in i programmet. Baserat på nyckeln sharding värdet för begäran, måste programmet bestämma rätt databas baserat på nyckelvärdet. Den öppnar sedan en anslutning till databasen för att bearbeta begäran. Databeroende routning ger möjlighet att öppna anslutningar med ett enkelt anrop i fragmentkartan för programmet. Databeroende routning var ett annat område med infrastrukturkod som nu omfattas av funktioner i klientbiblioteket för elastiska databaser. Mer information finns i [Databeroende routning](sql-database-elastic-scale-data-dependent-routing.md).
- **MSQ (Multi-shard queries):** Multi-shard querying works when a request involves several (or all) shards. En multi-shard-fråga kör samma T-SQL-kod på alla shards eller en uppsättning shards. Resultaten från de deltagande shards slås samman till en övergripande resultatuppsättning med HJÄLP AV UNION ALL semantik. Funktionen som exponeras via klientbiblioteket hanterar många uppgifter, inklusive: anslutningshantering, trådhantering, felhantering och mellanliggande resultatbearbetning. MSQ kan fråga upp till hundratals shards. Mer information finns i [Fråga om flera fragment](sql-database-elastic-scale-multishard-querying.md).

I allmänhet kan kunder som använder elastiska databasverktyg förvänta sig att få full T-SQL-funktionalitet när de skickar fragment-lokala åtgärder i motsats till korsskärdåtgärder som har sina egna semantik.



## <a name="next-steps"></a>Nästa steg

- Elastisk databasklientbibliotek ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22) [, .NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) - för att **hämta** biblioteket.

- [Kom igång med elastiska databasverktyg](sql-database-elastic-scale-get-started.md) – prova **exempelappen** som demonstrerar klientfunktioner.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) - för att bidra till koden.
- [Azure SQL Database elastisk fråga översikt](sql-database-elastic-query-overview.md) - att använda elastiska frågor.

- [Flytta data mellan utskalade molndatabaser](sql-database-elastic-scale-overview-split-and-merge.md) – för instruktioner om hur du använder **verktyget för delad koppling**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

