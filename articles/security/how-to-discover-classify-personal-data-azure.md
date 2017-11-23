---
title: Identifiera, identifiera och klassificera personliga data i Microsoft Azure | Microsoft Docs
description: "Lär dig mer om sökning, klassificera, upptäcka och identifiera data"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 39dea03ef9f42a3ff06dadd94c8d70541cbdc06c
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="discover-identify-and-classify-personal-data-in-microsoft-azure"></a>Identifiera, identifiera och klassificera personliga data i Microsoft Azure

Den här artikeln innehåller information om hur du identifierar, identifiera och klassificera personliga data i flera Azure-verktyg och tjänster, inklusive användning av Azure Data Catalog, Azure Active Directory, SQL-databas, Power Query för Hadoop-kluster i Azure HDInsight, Azure Informationsskydd, Azure Search och SQL-frågor för Azure Cosmos DB.

## <a name="scenario-problem-statement-and-goal"></a>Scenario, problembeskrivning och mål

Ett företag i USA-baserade sport samlar in en mängd olika personliga och andra data. Detta inkluderar kunder och information om anställda. Företaget behålls i flera databaser och lagrar den i flera olika ställen i sina Azure-miljön. Förutom sälja sportutrustning de också vara värd för och hantera registrering för elite inom händelser över hela världen, inklusive i EU, och i vissa fall innehåller kundinformation som de samlar in medicinska information.

Eftersom företaget värd många internationella bicycling visningar för varje år och har tillfällig personal på platser över hela världen, några av de uppgifter som är ganska stor. Företaget har också inbyggd developer program som används av kunder och anställda.

Företaget vill åtgärda följande problem:

- Kund- och personliga data måste vara klassificeras/särskiljas från övriga data som företaget samlar in för att säkerställa rätt åtkomst och säkerhet.
- Data-administratör behöver enkelt hitta platsen för kundens personliga data mellan olika områden i Azure-miljön.
- Kund- och personliga data som visas i delade dokument och e-postmeddelanden som ska visas för att säkerställa att den förblir säkra.
- Företagets apputvecklare behöver ett sätt att enkelt söka efter kund- och personliga data i webb- och mobilappar.
- Utvecklare måste också fråga sina dokumentdatabasen för personliga data.

### <a name="company-goals"></a>Företagets mål

- Alla kund- och personliga data måste vara taggade/kommenterade i Azure Data Catalog kan hittas enkelt. Helst är kund- och personliga data märkta/kommenterats separat.
- Personliga data från customer och medarbetare användarprofiler och information om arbete som finns i Azure Active Directory måste vara enkel att hitta.
- Personliga data som finns i flera SQL-databaser måste efterfrågas enkelt. 
- Vissa av företagets stora datamängder hanteras via Azure HDInsight och lagras i Hadoop. De måste importeras till Excel så att de kan efterfrågas för personliga data.
- Personliga data delas i dokument och e-postmeddelanden måste klassificeras, etiketter och förvaras skyddade med Azure Information Protection.
- Företagets apputveckling måste kunna identifiera kund- och personliga data i appar som de har skapat som de kan göra med Azure Search.
- Utvecklare måste kunna hitta personliga uppgifter i sina dokumentdatabasen.

## <a name="azure-active-directory-data-discovery"></a>Azure Active Directory: Identifiering av Data

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) är Microsofts molnbaserade, flera innehavare katalog och identity management-tjänsten. Du kan hitta kund- och användarprofiler och arbete användarinformation som innehåller personuppgifter inom din [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD)-miljö med hjälp av den [Azure-portalen](https://portal.azure.com/).

Detta är särskilt användbart om du vill söka efter eller ändra personliga data för en viss användare. Du kan också lägga till eller ändra användarprofil och information om arbete. Du måste logga in med ett konto som är en global administratör för katalogen.

### <a name="how-do-i-locate-or-view-user-profile-and-work-information"></a>Hur jag hitta eller visa användarprofil och fungerar information?

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.

2. Välj **fler tjänster**, ange **användare och grupper** i textrutan och välj sedan **RETUR**.

   ![Hur gör hitta användarprofil och information om arbete](media/how-to-discover-classify-personal-data-azure/user-profile.png)

3. På den **användare och grupper** bladet väljer **användare**.

  ![Öppna användare och grupper](media/how-to-discover-classify-personal-data-azure/users-groups.png)

4. På den **användare och grupper – användare** bladet Välj en användare i listan och i bladet för den valda användaren kan markera **profil** att visa användarens profilinformation som kan innehålla personuppgifter.

  ![Välj användare](media/how-to-discover-classify-personal-data-azure/select-user.png)

5. Om du behöver lägga till eller ändra användarens profilinformation kan du göra det och markera i kommandofältet **spara.**
6. På bladet för den valda användaren väljer **fungerar Info** att visa information om användaren arbete som kan innehålla personuppgifter.

 ![Visa arbetsinformation](media/how-to-discover-classify-personal-data-azure/work-info.png)

7. Om du behöver lägga till eller ändra användarinformation arbete du göra det och markera i kommandofältet **spara.**

## <a name="azure-sql-database-data-discovery"></a>Azure SQL Database: Data identifiering

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) är en databas i molnet som gör att utvecklare kan skapa och hantera program. Personliga data finns i [Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) med standard SQL-frågor. Azure SQL-elastisk fråga (förhandsgranskning) gör att användarna kan utföra mellan databasfrågor.

En detaljerad [SQL-databas](../sql-database/sql-database-technical-overview.md) kursen förklaras många aspekter av att använda en SQL-databas, inklusive hur du skapar en och hur du kör frågor. Följande är en sammanfattning av informationen som finns i självstudiekursen med länkar till specifika avsnitt.

### <a name="how-do-i-build-a-sql-database"></a>Hur skapar jag en SQL-databas

Det finns tre sätt att göra det:

- Du kan skapa en Azure SQL database i den [Azure-portalen](https://portal.azure.com/). I kursen, ska du använda en specifik uppsättning beräkning och lagring resurser inom en resursgrupp och logiska server. Du ska använda exempeldata från exempeldatabasen AdventureWorks. Du måste också skapa en brandväggsregel på servernivå. Om du vill veta hur du gör detta finns i [skapa en Azure SQL database i Azure portal](../sql-database/sql-database-get-started-portal.md) kursen.

  ![Skapa Azure SQL-databas](media/how-to-discover-classify-personal-data-azure/create-database.png)
- En SQL-databas kan även skapas i den [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) CLI, ett webbläsarbaserat kommandoradsverktyg. Verktyget är tillgängligt i Azure-portalen och kan köras direkt därifrån. I kursen får du starta verktyget, definiera skriptvariabler, skapa en resursgrupp och en logisk server och konfigurera en brandväggsregel för servern. Sedan skapar du en databas med exempeldata. Ta reda på hur du skapar din databas sätt den [skapa en enda Azure SQL-databas med hjälp av Azure CLI](../sql-database/sql-database-get-started-cli.md) kursen.

  ![CLIT självstudiekursen](media/how-to-discover-classify-personal-data-azure/cli-tutorial.png)

>[!NOTE]
Azure CLI används ofta av Linux-administratörer och utvecklare. Vissa användare vara enklare och mer intuitivt än PowerShell, vilket är ett tredje alternativ.

- Slutligen kan du skapa en SQL-databas med hjälp av PowerShell som är ett verktyg för kommandoraden eller skript som används för att skapa och hantera Azure och andra resurser. I kursen får du starta verktyget, definiera skriptvariabler, skapa en resursgrupp och en logisk server och konfigurera en brandväggsregel för servern. Sedan ska du skapa en databas med exempeldata.

Kursen kräver Azure PowerShell Modulversion 4.0 eller senare. Kör Get-Module - ListAvailable AzureRM att hitta din version. Om du behöver installera eller uppgradera finns i installera Azure PowerShell-modulen.

```PowerShell
New-AzureRmSQLDatabase -ResourceGroupName $resourcegroupname `
-ServerName $servername `
-DatabaseName $databasename `
-RequestedServiceObjectiveName "s0"
```

Ta reda på hur du skapar din databas sätt den [skapa en enda Azure SQL-databas med hjälp av Powershell](../sql-database/sql-database-get-started-powershell.md) kursen.

>[!Note]
Windows-administratörer tenderar att använda PowerShell, men några av dem föredrar Azure CLI.

### <a name="how-do-i-search-for-personal-data-in-sql-database-in-the-azure-portal"></a>Hur jag söker efter personliga data i SQL-databas på Azure-portalen? **

Du kan använda den inbyggda frågan Redigeraren för i Azure-portalen för att söka efter personlig data. Du måste logga in till verktyget med din SQL server-administratören inloggningsnamn och lösenord och sedan ange en fråga.

  ![Sök sql med hjälp av portalen](media/how-to-discover-classify-personal-data-azure/search-sql-portal.png)

Steg 5 i kursen visar en exempelfråga i fönstret Redigeraren för frågan, men det fokusera inte på personlig eller känslig information (det också kombinerar data från två tabeller och skapar alias för källkolumnen i den datamängd som returnerades). Följande skärmbild visar frågan från steg 5 samt resultatfönstret som returneras:

  ![frågeredigerare](media/how-to-discover-classify-personal-data-azure/query-editor.png)

Om din databas anropades mytable prefix, en exempelfråga för personlig information kan innehålla namn, personnummer och ID-nummer och skulle se ut så här:

”Välj namn, SSN, ID-nummer från mytable prefix”

Du vill köra frågan och se resultaten i den **resultat** fönstret.

Mer information om hur du frågar en SQL-databas på Azure-portalen finns i [frågor till SQL-databasen](../sql-database/sql-database-get-started-portal.md) avsnittet av kursen.

### <a name="how-do-i-search-for-data-across-multiple-databases"></a>Hur jag söker efter data över flera databaser?

Elastisk SQL-fråga (förhandsgranskning) gör att du kan utföra flera databaser och flera databasfrågor och returnera ett enskilt resultat. Den [självstudiekursen översikt](../sql-database/sql-database-elastic-query-overview.md) innehåller en detaljerad beskrivning av scenarier och förklaras skillnaden mellan vågräta och lodräta databasen partitioneras. Horisontell partitionering kallas ”horisontell partitionering”.

  ![Vertikal partitionering](media/how-to-discover-classify-personal-data-azure/vertical-partition.png)

  ![horisontell partitionering](media/how-to-discover-classify-personal-data-azure/horizontal.png)

Kom igång genom att besöka den [översikt över Azure SQL Database elastisk fråga (förhandsgranskning)](../sql-database/sql-database-elastic-query-overview.md) sidan.

#### <a name="power-query-for-importing-azure-hdinsight-hadoop-clusters-data-discovery-for-large-data-sets"></a>Power Query (för att importera Azure HDInsight Hadoop-kluster): identifiering av data för stora datamängder

Hadoop är en öppen källkod Apache lagring och behandling av tjänsten för stora datamängder som analyseras och lagras i Hadoop-kluster. [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) tillåter användare att arbeta med Hadoop-kluster i Azure. Power Query är ett Excel-tillägg som bland annat hjälper användarna att identifiera data från olika källor.

Personliga data som är associerade med Hadoop-kluster i Azure HDInsight kan importeras till Excel med Power Query. Du kan använda en fråga för att identifiera den när data är i Excel.

#### <a name="how-do-i-use-excel-power-query-to-import-hadoop-clusters-in-azure-hdinsight-into-excel"></a>Hur använder jag Power Query för Excel för att importera Hadoop-kluster i Azure HDInsight till Excel

Ett HDInsight-kursen vägleder dig genom hela processen. Det beskrivs förutsättningar och innehåller en länk till en [komma igång med Azure HDInsight](../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md) kursen. Anvisningar om Excel 2016 samt 2013 och 2010 (steg är något annorlunda för äldre versioner av Excel). Om du inte har Excel Power Query-tillägget i kursen visar hur du gör den. Du börjar kursen i Excel och måste ha ett Azure Blob storage-konto som är associerade med klustret.

  ![Frågan i Excel](media/how-to-discover-classify-personal-data-azure/excel.png)

Om du vill veta hur du gör detta finns i [ansluta Excel till Hadoop med Power Query](../hdinsight/hadoop/apache-hadoop-connect-excel-power-query.md) kursen.

Källa: [Anslut Excel till Hadoop med Power Query](../hdinsight/hadoop/apache-hadoop-connect-excel-power-query.md)

## <a name="azure-information-protection-personal-data-classification-for-documents-and-email"></a>Azure Information Protection: personuppgifter klassificering för dokument och e-post

[Azure Information Protection](https://www.microsoft.com/cloud-platform/azure-information-protection) hjälp Azure-kunder kan använda etiketter för att klassificera och skydda internt eller externt delade dokument och e-kommunikation. Vissa av dessa element kan innehålla personlig information för kunden eller medarbetare. Regler och villkor kan definieras automatiskt eller manuellt av administratörer eller användare. Om en användare sparar ett dokument som innehåller information om kreditkort, till exempel visas han eller hon en rekommendation för etikett som har konfigurerats av administratören.

### <a name="how-do-i-try-it"></a>Hur försök den?

Om du vill pröva Azure Information Protection att se om det kan vara en anpassning för din organisation, finns det [Snabbstartsguide](https://docs.microsoft.com/information-protection/get-started/infoprotect-quick-start-tutorial). Den vägleder dig igenom fem grundläggande steg – från installationen att konfigurera princip för att se klassificering, etikettering och dela i praktiken – och bör ta mindre än en halvtimme.

### <a name="how-do-i-deploy-it"></a>Hur distribuerar jag det?

Om du vill distribuera Azure Information Protection för din organisation, finns det [distributionsplan för klassificering, etikettering och skydd](https://docs.microsoft.com/information-protection/plan-design/deployment-roadmap).

### <a name="is-there-anything-else-i-should-know"></a>Finns det något annat jag veta?

Kompletterande information som hjälper dig att tänka igenom hur du ställer in finns i [redo, ange kan skydda!](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
blogginlägg. Och Läs mer länkarna nedan för mer om Azure Information Protection.

## <a name="azure-search-data-discovery-for-developer-apps"></a>Azure Search: data identifiering för utvecklare appar

[Azure Search](https://azure.microsoft.com/services/search/) är en sökning molnlösning för utvecklare och ger en omfattande data sökinställningar för dina program. Azure Search kan du söka efter data i användardefinierade index, kommer från Azure Cosmo DB, Azure SQL Database, Azure Blob Storage, Azure Table storage eller anpassade kunden JSON-data. Du kan också struktur Lucene frågor med Azure Search REST API för att söka efter personlig datatyper eller specifika personer personliga data. Funktioner är fulltextsökning och enkel frågesyntaxen Lucene frågesyntaxen. 

## <a name="how-do-i-use-sql-to-query-data"></a>Hur använder SQL att fråga efter data?

Börja med grunderna, finns det [Azure CosmosD DB: hur man frågan med hjälp av SQL](../cosmos-db/tutorial-query-documentdb.md) kursen. Självstudierna innehåller ett exempel på dokument och två exempel SQL-frågor och resultat.

Mer detaljerad vägledning om hur du skapar SQL-frågor finns [SQL-frågor för Cosmos-dokumentet på Azure DB DB API: et.](../cosmos-db/documentdb-sql-query.md)

Om du inte har använt Azure Cosmos DB och vill veta hur du skapar en databas, lägga till en samling och lägga till data, finns det [Azure Cosmos DB: skapa en webbapp med DocumentDB API](../cosmos-db/create-documentdb-dotnet.md) Snabbstartsguide. Om du vill göra detta i ett annat språk än .NET, till exempel Java eller Python, Välj ditt språk när du kommer till platsen.

## <a name="next-steps"></a>Nästa steg

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50)

[Vad är SQL Database?](../sql-database/sql-database-technical-overview.md)

[SQL Database Query Editor finns i Azure-portalen] (https://azure.microsoft.com/blog/t-sql-query-editor-in-browser-azure-portal/)

[Vad är Azure Information Protection?](https://docs.microsoft.com/information-protection/understand-explore/what-is-information-protection)

[Vad är Azure Rights Management?](https://docs.microsoft.com/information-protection/understand-explore/what-is-azure-rms)

[Azure Information Protection: Klar, ange, skydda!](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
