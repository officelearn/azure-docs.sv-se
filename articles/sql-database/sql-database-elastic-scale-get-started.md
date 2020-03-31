---
title: Komma igång med elastiska databasverktyg
description: Grundläggande förklaring av funktionen Elastiska databasverktyg i Azure SQL Database, inklusive en lättskjuten exempelapp.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 4577f611287a27366b1168e07565861c16fe0e38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987030"
---
# <a name="get-started-with-elastic-database-tools"></a>Komma igång med elastiska databasverktyg

Det här dokumentet introducerar dig till utvecklarupplevelsen för [klientbiblioteket](sql-database-elastic-database-client-library.md) för elastiska databaser genom att hjälpa dig att köra en exempelapp. Exempelappen skapar ett enkelt fragmenterat program och utforskar viktiga funktioner i funktionen Elastiska databasverktyg i Azure SQL Database. Den fokuserar på [användningsfall för fragmentkarthantering,](sql-database-elastic-scale-shard-map-management.md) [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md)och [frågar för flera fragment](sql-database-elastic-scale-multishard-querying.md). Klientbiblioteket är tillgängligt för .NET samt Java. 

## <a name="elastic-database-tools-for-java"></a>Elastiska databasverktyg för Java

### <a name="prerequisites"></a>Krav

* Ett Java Developer Kit (JDK), version 1.8 eller senare
* [Maven](https://maven.apache.org/download.cgi)
* En SQL Database-server i Azure eller en lokal SQL Server-instans

### <a name="download-and-run-the-sample-app"></a>Ladda ned och kör exempelappen

Så här skapar du JAR-filerna och kommer igång med exempelprojektet: 
1. Klona [GitHub-databasen](https://github.com/Microsoft/elastic-db-tools-for-java) som innehåller klientbiblioteket tillsammans med exempelappen. 

2. Redigera egenskapsfilen _./sample/src/main/resources/resource.properties_ för att ange följande:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Om du vill skapa exempelprojektet kör du följande kommando i _./sample-katalogen:_

    ```
    mvn install
    ```
    
4. Om du vill starta exempelprojektet kör du följande kommando i _./sample-katalogen:_ 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Om du vill veta mer om klientbibliotekets funktioner kan du experimentera med de olika alternativen. Känn dig fri att utforska koden för att lära sig mer om exempelappimplementeringen.

    ![Framsteg-java][5]
    
Grattis! Du har skapat och kört ditt första fragmenterade program med hjälp av elastiska databasverktyg i Azure SQL Database. Använd Visual Studio eller SQL Server Management Studio för att ansluta till DIN SQL-databas och ta en snabb titt på de shards som exemplet har skapat. Du kommer att märka nya exempel shard databaser och en shard map manager databas som exemplet har skapat. 

Om du vill lägga till klientbiblioteket i ditt eget Maven-projekt lägger du till följande beroende i DIN POM-fil:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Elastiska databasverktyg för .NET

### <a name="prerequisites"></a>Krav

* Visual Studio 2012 eller senare med C#. Ladda ner en gratis version på [Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2,7 eller senare. Mer om du vill hämta den senaste versionen finns i [Installera NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Ladda ned och kör exempelappen

Om du vill installera biblioteket går du till [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Biblioteket installeras med exempelappen som beskrivs i följande avsnitt.

Så här hämtar och kör du exemplet: 

1. Ladda ned exemplet [Elastiska DB-verktyg för Azure SQL - Komma igång](https://github.com/Azure/elastic-db-tools). Packa upp exemplet på en plats som du väljer.

2. Om du vill skapa ett projekt öppnar du *elasticscaleStarterKit.sln-lösningen* från *C#-katalogen.*

3. Öppna *filen app.config* i lösningen för exempelprojektet. Följ sedan instruktionerna i filen för att lägga till servernamnet för Azure SQL Database och din inloggningsinformation (användarnamn och lösenord).

4. Skapa och kör programmet. När du uppmanas att aktivera Visual Studio för att återställa NuGet-paketen för lösningen. Den här åtgärden hämtar den senaste versionen av klientbiblioteket för elastisk databas från NuGet.

5. Om du vill veta mer om klientbibliotekets funktioner kan du experimentera med de olika alternativen. Observera de steg som programmet tar i konsolen utgång, och gärna utforska koden bakom kulisserna.
   
    ![Förlopp][4]

Grattis! Du har skapat och kört ditt första fragmenterade program med hjälp av elastiska databasverktyg i SQL Database. Använd Visual Studio eller SQL Server Management Studio för att ansluta till DIN SQL-databas och ta en snabb titt på de shards som exemplet har skapat. Du kommer att märka nya exempel shard databaser och en shard map manager databas som exemplet har skapat.

> [!IMPORTANT]
> Vi rekommenderar att du alltid använder den senaste versionen av Management Studio så att du förblir synkroniserad med uppdateringar till Azure och SQL Database. [Uppdatera SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="key-pieces-of-the-code-sample"></a>Viktiga delar av kodexemplet

* **Hantera shards och shard kartor**: Koden illustrerar hur man arbetar med shards, intervall och mappningar i *ShardManagementUtils.cs* filen. Mer information finns i [Skala ut databaser med fragmentkarthanteraren](https://go.microsoft.com/?linkid=9862595).  

* **Databeroende routning**: Routning av transaktioner till rätt fragment visas i *DataDependentRoutingSample.cs* filen. Mer information finns i [Databeroende routning](https://go.microsoft.com/?linkid=9862596). 

* **Fråga över flera shards**: Fråga över shards illustreras i *MultiShardQuerySample.cs* filen. Mer information finns i [Fråga om flera fragment](https://go.microsoft.com/?linkid=9862597).

* **Lägga till tomma shards**: Den iterativa tillägg av nya tomma shards utförs av koden i *CreateShardSample.cs* filen. Mer information finns i [Skala ut databaser med fragmentkarthanteraren](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Andra elastiska skaloperationer

* **Dela en befintlig shard**: Möjligheten att dela shards tillhandahålls av split-merge verktyget. Mer information finns i [Flytta data mellan utskalade molndatabaser](sql-database-elastic-scale-overview-split-and-merge.md).

* **Sammanslagning av befintliga shards**: Fragmentsammanslagningar utförs också med hjälp av verktyget för delad sammanfogning. Mer information finns i [Flytta data mellan utskalade molndatabaser](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Kostnad

Biblioteket Med elastiska databasverktyg är gratis. När du använder elastiska databasverktyg ådra dig du inga ytterligare avgifter utöver kostnaden för din Azure-användning. 

Exempelprogrammet skapar till exempel nya databaser. Kostnaden för den här funktionen beror på den SQL Database-utgåva du väljer och Azure-användningen av ditt program.

Prisinformation finns i [prisinformation för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Nästa steg

Mer information om elastiska databasverktyg finns i följande artiklar:

* Kodexempel: 
  * Elastiska databasverktyg ([.NET](https://github.com/Azure/elastic-db-tools), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Elastiska databasverktyg för Azure SQL - Entity Framework-integrering](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Fragment elasticitet på Skript center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blogg: [Elastisk skala tillkännagivande](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* Kanal 9: [Översikt över elastisk skala](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Diskussionsforum: [Forum för Azure SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Så här mäter du prestanda: [Prestandaräknare för fragmentkarthanteraren](sql-database-elastic-database-client-library.md)

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

