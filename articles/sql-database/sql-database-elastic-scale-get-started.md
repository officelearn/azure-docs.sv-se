---
title: Kom igång med Elastic Database-verktyg – Azure | Microsoft Docs
description: Grundläggande förklaring av funktionen Verktyg för elastiska databaser i Azure SQL Database, inklusive ett enkelt och kör exempelappen.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: ''
manager: craigg
ms.date: 08/27/2018
ms.openlocfilehash: 13e9bb045bcce553ba9fdc344baa86ba96260387
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870239"
---
# <a name="get-started-with-elastic-database-tools"></a>Kom igång med elastiska Databasverktyg
Det här dokumentet ger en introduktion till utvecklarupplevelsen för den [klientbibliotek för elastiska databaser](sql-database-elastic-database-client-library.md) genom att hjälpa du kör en exempelapp. Exempelappen skapar ett enkelt delat program och utforskar nyckelfunktioner av funktionen Verktyg för elastiska databaser i Azure SQL Database. Den fokuserar på användningsområden för [fragmentkarthantering](sql-database-elastic-scale-shard-map-management.md), [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md), och [Multi-shard-frågor](sql-database-elastic-scale-multishard-querying.md). Det finns klientbiblioteket för .NET och Java. 

## <a name="elastic-database-tools-for-java"></a>Elastiska Databasverktyg för Java
### <a name="prerequisites"></a>Förutsättningar
* En Java Developer Kit (JDK), version 1.8 eller senare
* [Maven 3.](http://maven.apache.org/download.cgi)
* En logisk server i Azure eller en lokal SQL Server-instans

### <a name="download-and-run-the-sample-app"></a>Hämta och köra exempelappen
Om du vill skapa JAR-filerna och kom igång med i exempelprojektet kan du göra följande: 
1. Klona den [GitHub-lagringsplatsen](https://github.com/Microsoft/elastic-db-tools-for-java) som innehåller klientbiblioteket, tillsammans med exempelappen. 

2. Redigera den _./sample/src/main/resources/resource.properties_ filen för att ange följande:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Skapa exempelprojektet, i den _. / exempel_ directory, kör du följande kommando:

    ```
    mvn install
    ```
    
4. Starta i exempelprojektet, i den _. / exempel_ directory, kör du följande kommando: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Experimentera med de olika alternativen om du vill veta mer om funktionerna för klient-biblioteket. Passa på att utforska kod som du vill veta mer om app-implementeringen av exemplet.

    ![Progress-java][5]
    
Grattis! Du har skapat och kör ditt första delat program med hjälp av verktyg för elastiska databaser på Azure SQL Database. Använd Visual Studio eller SQL Server Management Studio för att ansluta till din SQL-databas och ta en titt på shards som skapat exemplet. Du ser nya Exempeldatabaser shard och en databas som exemplet har skapats. 

Lägg till klientbiblioteket till dina egna Maven-projekt genom att lägga till följande beroende i POM-filen:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Elastiska Databasverktyg för .NET 
### <a name="prerequisites"></a>Förutsättningar
* Visual Studio 2012 eller senare med C#. Hämta en kostnadsfri version på [hämtningsfiler för Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 eller senare. Om du vill hämta den senaste versionen, se [installera NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Hämta och köra exempelappen
Installera biblioteket genom att gå till [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Biblioteket har installerats med exempelappen som beskrivs i följande avsnitt.

Hämta och kör exemplet genom att följa dessa steg: 

1. Ladda ned den [elastiska DB-verktyg för Azure SQL - exemplet Getting Started](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) från MSDN. Packa upp exemplet på en plats som du väljer.

2. Om du vill skapa ett projekt, öppna den *ElasticScaleStarterKit.sln* lösning från den *C#* directory.

3. I lösningen för exempelprojektet, öppna den *app.config* fil. Följ sedan instruktionerna i filen för att lägga till din Azure SQL Database-servernamn och din inloggningsinformation (användarnamn och lösenord).

4. Skapa och kör programmet. När du uppmanas, aktivera Visual Studio för att återställa NuGet-paket för lösningen. Den här åtgärden hämtar den senaste versionen av klientbiblioteket för elastiska databaser från NuGet.

5. Experimentera med de olika alternativen om du vill veta mer om funktionerna för klient-biblioteket. Observera de steg som programmet tar i konsolen utdata och passa på att utforska koden i bakgrunden.
   
    ![Förlopp][4]

Grattis! Du har skapat och kör ditt första delat program med hjälp av verktyg för elastiska databaser i SQL-databas. Använd Visual Studio eller SQL Server Management Studio för att ansluta till din SQL-databas och ta en titt på shards som skapat exemplet. Du ser nya Exempeldatabaser shard och en databas som exemplet har skapats.

> [!IMPORTANT]
> Vi rekommenderar att du alltid använder den senaste versionen av Management Studio så att du alltid är synkroniserad med uppdateringar av Azure och SQL Database. [Uppdatera SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="key-pieces-of-the-code-sample"></a>Viktiga delar av kodexemplet
* **Hantera shards och shard**: koden visar hur du arbetar med fragment, intervall och avbildningar i den *ShardManagementUtils.cs* fil. Mer information finns i [skala ut databaser med fragmentkartehanteraren](https://go.microsoft.com/?linkid=9862595).  

* **Databeroende routning**: routning av transaktioner till rätt fragment visas i den *DataDependentRoutingSample.cs* fil. Mer information finns i [databeroende routning](https://go.microsoft.com/?linkid=9862596). 

* **Fråga över flera shard**: fråga över shards illustreras i den *MultiShardQuerySample.cs* fil. Mer information finns i [Multi-shard-frågor](https://go.microsoft.com/?linkid=9862597).

* **Att lägga till tom shards**: iterativ tillägg av nya tom fragmenten utförs av koden i den *CreateShardSample.cs* fil. Mer information finns i [skala ut databaser med fragmentkartehanteraren](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Andra åtgärder med elastisk skalning
* **Dela en befintlig fragment**: möjlighet att dela shards tillhandahålls av verktyget Dela och slå samman. Mer information finns i [flytta data mellan utskalade molndatabaser](sql-database-elastic-scale-overview-split-and-merge.md).

* **Sammanslagning av befintlig shards**: fragment av dokument utförs också med hjälp av verktyget Dela och slå samman. Mer information finns i [flytta data mellan utskalade molndatabaser](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Kostnad
Elastiska Databasverktyg-biblioteket är gratis. När du använder Verktyg för elastiska databaser, medför inga ytterligare avgifter utöver kostnaden för din Azure-användning. 

Exempelprogrammet skapar till exempel nya databaser. Kostnaden för den här funktionen är beroende av SQL Database-utgåva du väljer och Azure-användning av ditt program.

Information om priser finns i [SQL Database prisinformation](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Nästa steg
Mer information om verktyg för elastiska databaser finns i följande artiklar:

* Kodexempel: 
  * Elastiska Databasverktyg ([.NET](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Elastic Database-verktyg för Azure SQL - integrering av Entity Framework](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Skärvelasticitet på Script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blogg: [elastisk skalning meddelande](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Channel 9: [Elastic Scale översiktsvideo](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Diskussionsforum: [Azure SQL Database-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Att mäta prestanda: [prestandaräknare för karthanteraren för shard](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
[5]: ./media/sql-database-elastic-scale-get-started/java-client-library.PNG

