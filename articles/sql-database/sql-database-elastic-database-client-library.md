---
title: Skapa skalbara molnresurser databaser | Microsoft Docs
description: "Skapa skalbara .NET databasen appar med klientbibliotek för elastisk databas"
services: sql-database
manager: jhubbard
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/12/2017
ms.author: sstein
ms.openlocfilehash: e15254fd529d98f2b92acea8dd1ed908a82841ea
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="building-scalable-cloud-databases"></a>Skapa skalbara molndatabaser
Skala ut databaser kan enkelt utföras med hjälp av skalbara verktyg och funktioner för Azure SQL Database. I synnerhet kan du använda den **klientbibliotek för elastisk databas** att skapa och hantera databaser som skalats ut. Den här funktionen kan du enkelt utvecklar delat program med hundratals, eller till och med tusentals – för Azure SQL-databaser. [Elastiska jobb](sql-database-elastic-jobs-powershell.md) kan sedan användas för att underlätta hanteringen av dessa databaser.

Så här hämtar:
* Java-versionen av biblioteket, se [Maven centrallager](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* .NET-versionen av biblioteket, se [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentation
1. [Kom igång med elastiska databasverktyg](sql-database-elastic-scale-get-started.md)
2. [Elastiska databasfunktioner](sql-database-elastic-scale-introduction.md)
3. [Karthantering för shard](sql-database-elastic-scale-shard-map-management.md)
4. [Migrera befintliga databaser ska skalas ut](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Databeroende routning](sql-database-elastic-scale-data-dependent-routing.md)
6. [Flera Fragmentera frågor](sql-database-elastic-scale-multishard-querying.md)
7. [Lägga till en Fragmentera med hjälp av verktyg för elastisk databas](sql-database-elastic-scale-add-a-shard.md)
8. [Program med flera klienter med elastiska Databasverktyg och säkerhet på radnivå](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Uppgradera klienten biblioteket appar](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Översikt över elastisk frågor](sql-database-elastic-query-overview.md)
11. [Ordlista för verktyg i elastiska databaser](sql-database-elastic-scale-glossary.md)
12. [Klientbibliotek för elastisk databas med Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Klientbibliotek för elastisk databas med Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Dela merge tool](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Prestandaräknare för karthanteraren för shard](sql-database-elastic-database-client-library.md) 
16. [Vanliga frågor och svar för elastiska Databasverktyg](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Klientfunktioner
Skala ut program med hjälp av *horisontell partitionering* visar utmaningar för både utvecklaren som administratören. Klientbiblioteket förenklar hanteringsuppgifter genom att tillhandahålla verktyg som gör att båda utvecklare och administratörer hantera databaser som skalats ut. Det finns många databaser som kallas ”shards” för att hantera i ett typexempel. Kunder som är samordnad i samma databas och det finns en databas per kund (en enskild klient schemat). Klientbiblioteket innehåller följande funktioner:

- **Fragmentera kartan Management**: en särskild databas kallas ”Fragmentera kartan manager” skapas. Fragmentera kartan management är möjligheten för ett program att hantera metadata om dess delar. Utvecklare kan använda den här funktionen för att registrera databaser som delar, beskriver mappningar av enskilda horisontell partitionering nycklar eller viktiga områden för att dessa databaser och underhålla dessa metadata som talet och sammansättning av databaser som utvecklas för att återspegla ändringar kapacitet. Du skulle behöva tillbringar mycket tid skriva management koden när du implementerar horisontell partitionering utan klientbibliotek för elastisk databas. Mer information finns i [Fragmentera kartan management](sql-database-elastic-scale-shard-map-management.md).

- **Data beroende routning**: Anta en begäran till programmet. Baserat på värdet för nyckeln för horisontell partitionering av begäran måste programmet fastställa rätt databas baserat på värdet för nyckeln. Sedan öppnas en anslutning till databasen för att behandla begäran. Data beroende routning ger dig möjlighet att öppna anslutningar med ett enda enkelt anrop till Fragmentera mappningen av programmet. Data beroende routning har ett annat område av infrastrukturkod som omfattas nu av funktioner i klientbibliotek för elastisk databas. Mer information finns i [Data beroende routning](sql-database-elastic-scale-data-dependent-routing.md).
- **Flera Fragmentera frågor (MSQ)**: flera Fragmentera frågar fungerar när en begäran omfattar flera (eller alla) delar. En fråga med flera Fragmentera kör samma T-SQL-kod på alla delar eller en uppsättning shards. Resultaten från deltagande shards slås samman till en övergripande resultatmängden med hjälp av UNION ALL semantik. Funktioner som exponeras via klientbiblioteket hanterar många aktiviteter, inklusive: anslutningshanteringen, tråd management, hantering av fel och mellanresultat bearbetning. MSQ kan fråga upp till hundratals delar. Mer information finns i [flera Fragmentera frågar](sql-database-elastic-scale-multishard-querying.md).

I allmänhet kunder som använder elastisk Databasverktyg kan förvänta sig att hämta alla T-SQL-funktioner när du skickar in Fragmentera lokala åtgärder och mellan Fragmentera åtgärder som har sina egna semantik.



## <a name="next-steps"></a>Nästa steg

- Klientbibliotek för elastisk databas ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) – för att **hämta** i biblioteket.

- [Kom igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md) – du kan prova den **exempelapp** som visar klientfunktioner.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) - om du vill se bidrag till koden.
- [Översikt av Azure SQL Database-elastisk frågan](sql-database-elastic-query-overview.md) - om du vill använda elastisk frågor.

- [Flytta data mellan databaser som skalats ut molnet](sql-database-elastic-scale-overview-split-and-merge.md) - instruktioner om hur du använder den **för delade sökvägssammanslagning**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

