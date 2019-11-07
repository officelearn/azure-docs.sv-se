---
title: Skapa skalbara molndatabaser
description: Bygg skalbara .NET Database-appar med klient biblioteket för Elastic Database
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
ms.openlocfilehash: 0b5b3c924a644c065327db36a6a8d64b4a552d40
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690523"
---
# <a name="building-scalable-cloud-databases"></a>Skapa skalbara molndatabaser

Att skala ut databaser kan enkelt utföras med skalbara verktyg och funktioner för Azure SQL Database. I synnerhet kan du använda **Elastic Database klient biblioteket** för att skapa och hantera utskalade databaser. Med den här funktionen kan du enkelt utveckla shardade-program med hundratals, eller till och med tusentals, Azure SQL-databaser.

Hämta:

* Java-versionen av biblioteket finns i [maven Central-lagringsplats](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* .NET-versionen av biblioteket finns i [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentation

1. [Kom igång med Elastic Database-verktyg](sql-database-elastic-scale-get-started.md)
2. [Elastic Database funktioner](sql-database-elastic-scale-introduction.md)
3. [Karthantering för shard](sql-database-elastic-scale-shard-map-management.md)
4. [Migrera befintliga databaser för att skala ut](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Databeroende routning](sql-database-elastic-scale-data-dependent-routing.md)
6. [Frågor för flera Shard](sql-database-elastic-scale-multishard-querying.md)
7. [Lägga till en Shard med hjälp av Elastic Database verktyg](sql-database-elastic-scale-add-a-shard.md)
8. [Program med flera klienter med elastiska databas verktyg och säkerhet på radnivå](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Uppgradera klient biblioteks appar](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Översikt över elastiska frågor](sql-database-elastic-query-overview.md)
11. [Ordlista för verktyg i elastiska databaser](sql-database-elastic-scale-glossary.md)
12. [Elastic Database klient bibliotek med Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Klient bibliotek för Elastic Database med dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Verktyg för delad sammanslagning](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Prestandaräknare för karthanteraren för shard](sql-database-elastic-database-client-library.md) 
16. [Vanliga frågor och svar om elastiska databas verktyg](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Klient funktioner

Att skala ut program som använder *horisontell partitionering* presenterar utmaningar för både utvecklare och administratören. Klient biblioteket fören klar hanterings åtgärderna genom att tillhandahålla verktyg som låter både utvecklare och administratörer hantera utskalade databaser. I ett typiskt exempel finns det många databaser, som kallas "Shards", för att hantera. Kunder är samplacerade i samma databas och det finns en databas per kund (ett schema med en enda klient). Klient biblioteket innehåller följande funktioner:

- **Hantering av Shard-kartor**: en särskild databas som kallas "Shard Map Manager" skapas. Shard Map Management är möjligheten för ett program att hantera metadata om dess Shards. Utvecklare kan använda den här funktionen för att registrera databaser som Shards, beskriva mappningar av enskilda horisontell partitionering-nycklar eller nyckel intervall till dessa databaser och upprätthålla dessa metadata eftersom antalet och sammansättningen av databaserna utvecklas för att avspegla kapacitets ändringar. Utan klient biblioteket för Elastic Database skulle du behöva ägna mycket tid åt att skriva hanterings koden när du implementerar horisontell partitionering. Mer information finns i [Shard Map Management](sql-database-elastic-scale-shard-map-management.md).

- **Data beroende routning**: Föreställ dig att en begäran kommer till programmet. Baserat på horisontell partitionering nyckel värde för begäran måste programmet fastställa rätt databas baserat på nyckelvärdet. Sedan öppnas en anslutning till databasen för att bearbeta begäran. Data beroende routning ger möjlighet att öppna anslutningar med ett enkelt anrop till Shard-kartan för programmet. Data beroende routning var en annan del av infrastruktur koden som nu omfattas av funktioner i klient biblioteket för Elastic Database. Mer information finns i [data beroende routning](sql-database-elastic-scale-data-dependent-routing.md).
- **Shard frågor (MSQ)** : multi-Shard-frågor fungerar när en begäran omfattar flera (eller alla) Shards. En multi-Shard-fråga kör samma T-SQL-kod på alla Shards eller en uppsättning Shards. Resultatet från de deltagande Shards sammanfogas i en övergripande resultat uppsättning med UNION ALL semantik. De funktioner som exponeras via klient biblioteket hanterar många aktiviteter, inklusive hantering av anslutningar, tråd hantering, fel hantering och mellanliggande resultat bearbetning. MSQ kan fråga upp till hundratals Shards. Mer information finns i [multi-Shard-frågor](sql-database-elastic-scale-multishard-querying.md).

I allmänhet kan kunder som använder elastiska databas verktyg vänta med att få fullständiga T-SQL-funktioner när de skickar Shard-lokala åtgärder i stället för Shard-åtgärder som har egna semantik.



## <a name="next-steps"></a>Nästa steg

- Elastic Database klient bibliotek ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.net](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) – för att **Ladda ned** biblioteket.

- [Kom igång med elastiska databas verktyg](sql-database-elastic-scale-get-started.md) – för att testa **exempel appen** som visar klient funktioner.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.net](https://github.com/Azure/elastic-db-tools)) – för att göra bidrag till koden.
- [Översikt över Azure SQL Database elastisk fråga](sql-database-elastic-query-overview.md) – om du vill använda elastiska frågor.

- [Flytta data mellan utskalade moln databaser](sql-database-elastic-scale-overview-split-and-merge.md) – instruktioner om hur du använder **verktyget Dela och slå samman**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

