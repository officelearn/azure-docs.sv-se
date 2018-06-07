---
title: Kom igång med elastisk Databasverktyg - Azure | Microsoft Docs
description: Grundläggande förklaring av funktionen elastisk Databasverktyg i Azure SQL Database, inklusive ett enkelt och kör sample-appen.
services: sql-database
manager: craigg
author: anumjs
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: anjangsh
ms.openlocfilehash: cf180149ceb0b6c88fab8a0a7bb58a84d25b1b9c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647239"
---
# <a name="get-started-with-elastic-database-tools"></a>Kom igång med elastisk Databasverktyg
Det här dokumentet ger en introduktion till utvecklare upplevelsen för den [klientbibliotek för elastisk databas](sql-database-elastic-database-client-library.md) genom att du kör en exempelapp. Exempelappen skapar ett enkelt delat program och utforskar viktiga funktioner för funktionen elastisk Databasverktyg i Azure SQL Database. Den fokuserar på användningsområden för [Fragmentera kartan management](sql-database-elastic-scale-shard-map-management.md), [data beroende routning](sql-database-elastic-scale-data-dependent-routing.md), och [flera Fragmentera frågar](sql-database-elastic-scale-multishard-querying.md). Klientbiblioteket är tillgänglig för .NET och Java. 

## <a name="elastic-database-tools-for-java"></a>Elastisk Databasverktyg för Java
### <a name="prerequisites"></a>Förutsättningar
* En Java Developer Kit (JDK), version 1,8 eller senare
* [Maven 3.](http://maven.apache.org/download.cgi)
* En logisk server i Azure eller en lokal SQL Server-instans

### <a name="download-and-run-the-sample-app"></a>Hämta och kör exempelappen
För att bygga JAR-filer och kom igång med exempelprojektet, gör du följande: 
1. Klona den [GitHub-lagringsplatsen](https://github.com/Microsoft/elastic-db-tools-for-java) som innehåller klientbiblioteket tillsammans med sample-appen. 

2. Redigera den _./sample/src/main/resources/resource.properties_ fil att ange följande:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Att skapa exempelprojektet, i den _. / exempel_ directory, kör du följande kommando:

    ```
    mvn install
    ```
    
4. Starta exempelprojektet, i den _. / exempel_ directory, kör du följande kommando: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Mer information om biblioteket klientfunktioner experimentera med olika alternativ. Du kan utforska koden att lära dig om exempel app implementering.

    ![Progress-java][5]
    
Grattis! Du har skapat och köra ditt första delat program med elastiska Databasverktyg på Azure SQL Database. Använd Visual Studio eller SQL Server Management Studio för att ansluta till SQL-databasen och ta en titt på shards som exemplet skapas. Du ser nya exempeldatabaserna Fragmentera och en Fragmentera kartan manager-databas som har skapats av exemplet. 

Lägg till klientbiblioteket egna Maven-projekt genom att lägga till följande beroende på POM-filen:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Elastisk Databasverktyg för .NET 
### <a name="prerequisites"></a>Förutsättningar
* Visual Studio 2012 eller senare med C#. Hämta en kostnadsfri version på [Visual Studio-hämtningar](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 eller senare. Om du vill hämta den senaste versionen finns [installera NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Hämta och kör exempelappen
Om du vill installera biblioteket, gå till [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Biblioteket har installerats med exempelappen som beskrivs i följande avsnitt.

Följ dessa steg om du vill hämta och köra exemplet: 

1. Hämta den [elastisk DB-verktyg för Azure SQL - komma igång exempel](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) på MSDN. Packa upp exemplet på en plats som du väljer.

2. Om du vill skapa ett projekt, öppna den *ElasticScaleStarterKit.sln* lösningar från den *C#* directory.

3. Öppna i lösningen för exempelprojektet den *app.config* fil. Följ sedan instruktionerna i filen för att lägga till din Azure SQL Database-servernamn och din inloggningsinformation (användarnamn och lösenord).

4. Skapa och köra programmet. När du uppmanas aktivera Visual Studio för att återställa NuGet-paket för lösningen. Den här åtgärden hämtar den senaste versionen av klientbiblioteket för elastisk databas från NuGet.

5. Mer information om biblioteket klientfunktioner experimentera med olika alternativ. Observera de steg som programmet tar i konsolen utdata och gärna att utforska koden i bakgrunden.
   
    ![Förlopp][4]

Grattis! Du har skapat och köra ditt första delat program med elastiska Databasverktyg på SQL-databas. Använd Visual Studio eller SQL Server Management Studio för att ansluta till SQL-databasen och ta en titt på shards som exemplet skapas. Du ser nya exempeldatabaserna Fragmentera och en Fragmentera kartan manager-databas som har skapats av exemplet.

> [!IMPORTANT]
> Vi rekommenderar att du alltid använder den senaste versionen av Management Studio så att du vara synkroniserat med uppdateringar till Azure och SQL-databas. [Uppdatera SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="key-pieces-of-the-code-sample"></a>Nyckelinformation kodexemplet
* **Hantera delar och Fragmentera**: koden illustrerar hur du arbetar med shards, intervall och avbildningar i den *ShardManagementUtils.cs* fil. Mer information finns i [skala ut databaser med Fragmentera kartan manager](http://go.microsoft.com/?linkid=9862595).  

* **Data dependent routning**: routning av transaktioner till rätt Fragmentera visas i den *DataDependentRoutingSample.cs* fil. Mer information finns i [Data beroende routning](http://go.microsoft.com/?linkid=9862596). 

* **Fråga över flera delar**: frågar över shards illustreras i den *MultiShardQuerySample.cs* fil. Mer information finns i [flera Fragmentera frågar](http://go.microsoft.com/?linkid=9862597).

* **Lägga till tomma shards**: iterativ tillägg av ny tom shards utförs av koden i den *CreateShardSample.cs* fil. Mer information finns i [skala ut databaser med Fragmentera kartan manager](http://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Andra åtgärder elastisk skalbarhet
* **Dela en befintlig Fragmentera**: möjlighet att dela shards tillhandahålls av verktyget delade dokument. Mer information finns i [flytta data mellan databaser som skalats ut molnet](sql-database-elastic-scale-overview-split-and-merge.md).

* **Sammanslagning av befintlig shards**: Fragmentera sammanslagningar görs också med hjälp av verktyget Dela dokument. Mer information finns i [flytta data mellan databaser som skalats ut molnet](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Kostnad
Elastisk Databasverktyg biblioteket är gratis. När du använder elastisk Databasverktyg innebära utan ytterligare kostnader utöver kostnaden för din användning av Azure. 

Till exempel skapas exempelprogrammet nya databaser. Kostnaden för den här funktionen är beroende av SQL Database-utgåvan som du väljer och Azure användningen av ditt program.

Information om priser finns [SQL-databas prisinformation](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Nästa steg
Mer information om elastisk Databasverktyg finns i följande artiklar:

* Kodexempel: 
  * Elastisk Databasverktyg ([.NET](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Elastisk Databasverktyg för Azure SQL - Entity Framework-integrering](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Fragmentera elasticitet på Script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blogg: [elastisk skalbarhet meddelande](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Microsoft Virtual Academy: [implementera skalbar med hjälp av delning med elastisk databas klienten biblioteket Video](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554?l=lWyQhF1fC_6306218965) 
* Channel 9: [elastisk skalbarhet översikten](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Diskussionsforum: [Azure SQL Database-forum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Att mäta prestanda: [prestandaräknare för Fragmentera kartan manager](sql-database-elastic-database-client-library.md)

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

