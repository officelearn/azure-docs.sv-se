---
title: Kör analytics frågor mot klient databaser med hjälp av Azure SQL Data Warehouse | Microsoft Docs
description: Mellan klient analytics-frågor med data som hämtats från flera databaser i Azure SQL Database.
keywords: sql database tutorial
services: sql-database
documentationcenter: ''
author: anumjs
manager: craigg
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/08/2017
ms.author: anjangsh
ms.openlocfilehash: dd51480f30187456104a0f9d72ec17082dc1e39e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Utforska SaaS analytics med Azure SQL Database, SQL Data Warehouse, Data Factory och Power BI

I den här kursen går igenom ett scenario för slutpunkt till slutpunkt analytics. Scenariot visar hur analytics över klientdata kan ge programleverantörer att fatta smarta beslut. Med data som hämtats från databasen för varje klient kan använda du analytics och få insikter om klient beteende, inklusive deras användning av exempel Wingtip biljetter SaaS-program. Det här scenariot omfattar tre steg: 

1.  **Extrahera data** från varje hyresgäst databasen till en analytics-arkivet i det här fallet ett SQL Data Warehouse.
2.  **Optimera extraherade data** för analytics-bearbetning.
3.  Använd **Business Intelligence** verktyg för att rita ut användbar information som hjälper beslutsfattande. 

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> - Skapa klienten analytics lagra för inläsning.
> - Använd Azure Data Factory (ADM) för att extrahera data från varje klient-databas till datalagret analytics.
> - Optimera extraherade data (reorganize till en stjärna schemat).
> - Fråga analytics-datalagret.
> - Använd Power BI för visualisering av data och markera trender i klientdata och ge rekommendation för förbättringar.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analytics över extraherade klientdata

SaaS-program innehåller ett potentiellt stora datamängder klient i molnet. Dessa data kan ge insikter om åtgärden omfattande källan och användningen av ditt program och beteendet för dina klienter. Dessa insights hjälper funktionen utveckling, användbarhet förbättringar och andra investeringar i appar och plattform.

Det är enkelt att komma åt data för alla klienter när alla data i en databas för flera innehavare. Men åtkomst är mer komplexa distribuerade i större skala över tusentals databaser. Ett sätt att Undvik komplexitet är att extrahera data till en analytics-databas eller ett datalager för frågan.

Den här kursen behandlas en slutpunkt till slutpunkt analytics scenario för programmets Wingtip biljetter. Första, [Azure Data Factory (ADM)](../data-factory/introduction.md) används som orchestration-verktyg för att extrahera biljetter försäljnings- och relaterade data från varje klient-databas. Dessa data har lästs in i mellanlagringstabellerna i en butik analytics. Arkivet analytics kan antingen vara en SQL-databas eller ett SQL Data Warehouse. Den här kursen använder [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) som analyserna lagra.

Därefter extraherade data omvandlas och läses in i en uppsättning [star-schema](https://www.wikipedia.org/wiki/Star_schema) tabeller. Tabellerna består av en central faktatabell plus relaterade dimensionstabeller:

- Central faktatabell i stjärnan-schemat innehåller biljett data.
- Dimensionstabellerna innehåller data om handelsplatser, händelser, kunder och köpa datum.

Tillsammans central och tabeller aktivera effektivt analytiska dimensionsbearbetning. Star-schema som används i den här självstudiekursen visas i följande bild:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Slutligen tillfrågas tabellerna star-schema. Frågans resultat visas visuellt med hjälp av Power BI om du vill markera insikter om klient beteende och deras användning av programmet. Med den här star-schema köra frågor som exponerar:

- Vem köpa biljetter och från vilken plats.
- Mönster och trender i försäljning av biljetter.
- Den relativa populariteten för varje plats.

Den här kursen ger grundläggande exempel på insikter som kan vara uppnår från Wingtip biljetter data. Förstå hur varje plats använder tjänsten kan orsaka att tänka på olika serviceplaner riktad mot mer eller mindre aktiva handelsplatser, till exempel Wingtip biljetter leverantören. 

## <a name="setup"></a>Konfiguration

### <a name="prerequisites"></a>Förutsättningar

> [!NOTE]
> Den här kursen använder funktioner i Azure Data Factory som för närvarande i en begränsad förhandsgranskning (länkade tjänsten parameterization). Om du vill göra den här självstudiekursen, ange ditt prenumerations-ID [här](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Vi skickar en bekräftelse när prenumerationen har aktiverats.

Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:
- Wingtip biljetter SaaS databas Per klient programmet distribueras. För att distribuera på mindre än fem minuter finns [distribuera och utforska Wingtip SaaS-program](saas-dbpertenant-get-started-deploy.md).
- Wingtip biljetter SaaS databas Per klient skript och programmet [källkod](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) laddas ned från GitHub. Se Hämta instruktioner. Se till att *avblockera zip-filen* innan du extraherar innehållet.
- Power BI Desktop har installerats. [Hämta Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- I gruppen med ytterligare klienter har etablerats, finns det [ **etablera hyresgäster kursen**](saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Skapa data för demonstrationen

Den här självstudiekursen utforskar analytics över försäljningsdata för biljett. I det här steget kan generera du biljett data för alla klienter. I ett senare steg extraheras data för analys. _Se till att du har etablerat i gruppen med klienter_ (enligt beskrivningen tidigare) så att du har tillräckligt med data att exponera en mängd olika biljett köp mönster.

1. I PowerShell ISE öppnar *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*, och anger följande värde:
    - **$DemoScenario** = **1** köpa biljetter för händelser på alla handelsplatser
2. Tryck på **F5** att köra skriptet och skapa biljett köp historik för alla handelsplatser. Skriptet genererar biljetter tiotusentals med 20-klienter och kan ta 10 minuter eller mer.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>Distribuera SQL Data Warehouse Data Factory och Blob Storage 
I appen Wingtip biljetter distribueras klienternas transaktionsdata över flera databaser. Azure Data Factory (ADM) används för att dirigera extrahering, belastning och transformera ELT () av dessa data till datalagret. Om du vill läsa in data till SQL Data Warehouse mest effektivt ADF extraherar data i mellanliggande blob-filer och använder sedan [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) att läsa in data till datalagret.   

I det här steget kan du distribuera ytterligare resurser som används i kursen: ett SQL Data Warehouse kallas _tenantanalytics_, ett Azure Data Factory kallas _dbtodwload -\<användare\>_  , och ett Azure storage-konto som kallas _wingtipstaging\<användare\>_. Storage-konto används för att tillfälligt lagra extraherade filer som blobar innan de läses till datalagret. Det här steget också distribuerar data warehouse schemat och definierar ADF pipelines som samordnar ELT processen.
1. I PowerShell ISE öppnar *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* och ange:
    - **$DemoScenario** = **2** distribuera klient analytics datalagret, blob-lagring och data factory 
1. Tryck på **F5** att köra skriptet demo och distribuera Azure-resurser. 

Nu ska du granska de Azure-resurser du har distribuerat:
#### <a name="tenant-databases-and-analytics-store"></a>Klient-databaser och analyser store
Använd [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) att ansluta till **tenants1-dpt -&lt;användare&gt;**  och **katalog-dpt -&lt;användare&gt;**  servrar. Ersätt &lt;användaren&gt; med det värde som används när du har distribuerat appen. Använd inloggningen = *developer* och lösenord = *P@ssword1*. Finns det [inledande kursen](saas-dbpertenant-wingtip-app-overview.md) för mer hjälp.

![Ansluta till SQL Database-server från SSMS](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

I Object Explorer:

1. Expandera den *tenants1-dpt -&lt;användare&gt;*  server.
1. Expandera noden databaser och se en lista över databaser som klient.
1. Expandera den *katalog-dpt -&lt;användare&gt;*  server.
1. Kontrollera att du ser analytics lagra som innehåller följande objekt:
    1. Tabeller **raw_Tickets**, **raw_Customers**, **raw_Events** och **raw_Venues** håller extraherade rådata från klient-databaser.
    1. Tabellerna star-schema är **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, och **dim_Dates** .
    1. Den lagrade proceduren **sp_transformExtractedData** används för att omvandla data och läsa in det i tabellerna star-schema.

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage
1. I den [Azure Portal](https://ms.portal.azure.com), navigera till den resursgrupp som du använde för att distribuera programmet. Kontrollera att ett lagringskonto för **wingtipstaging\<användare\>**  har lagts till.

  ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Klicka på **wingtipstaging\<användare\>**  lagringskonto för att utforska objekt som finns.
1. Klicka på **Blobbar** panelen
1. Klicka på behållaren **configfile**
1. Kontrollera att **configfile** innehåller en JSON-fil som kallas **TableConfig.json**. Den här filen innehåller käll- och tabellnamn, kolumnnamn och spåraren kolumnnamnet.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADM)
I den [Azure Portal](https://ms.portal.azure.com) i resursgrupp, kontrollerar du att ett Azure Data Factory för _dbtodwload -\<användare\>_  har lagts till. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

Det här avsnittet behandlar datafabriken skapas. Följ stegen nedan för att starta datafabriken:
1. I portalen klickar du på datafabriken kallas **dbtodwload -\<användare\>**.
2. Klicka på **författare & övervakaren** rutan för att starta Data Factory-designer i en separat flik. 

## <a name="extract-load-and-transform-data"></a>Extrahera, Load, och transformera data
Azure Data Factory används för att samordna extrahering, inläsning och transformering av data. I den här självstudiekursen extrahera data från fyra olika SQL-vyer från varje klient-databaser: **rawTickets**, **rawCustomers**, **rawEvents**, och  **rawVenues**. Bland dessa vyer finns plats-Id, så du kan skilja data från varje plats i datalagret. Data läses in i motsvarande mellanlagringstabellerna i datalagret: **raw_Tickets**, **raw_customers**, **raw_Events** och **raw_Venue**. En lagrad procedur sedan omvandlar rådata och fyller tabellerna star-schema: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** , och **dim_Dates**.

I föregående avsnitt, distribueras och initiera nödvändiga Azure-resurser, inklusive datafabriken. Distribuerade datafabriken innehåller pipelines, datauppsättningar, länkade tjänster, etc., krävs för att extrahera, ladda och transformera klientdata. Vi ska undersöka dessa ytterligare objekt och utlösare pipeline att flytta data från klienten databaser till datalagret.

### <a name="data-factory-pipeline-overview"></a>Översikt över data factory-pipelinen
Det här avsnittet behandlar objekt som skapas i datafabriken. Följande bild beskrivs övergripande arbetsflödet i ADF pipeline används i den här kursen. Om du vill utforska pipeline senare och se resultaten först gå vidare till nästa avsnitt **utlösa pipeline kör**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

På översiktssidan växla till **författaren** fliken på den vänstra panelen och Observera att det finns tre [pipelines](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) och tre [datauppsättningar](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) skapas.
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

Tre kapslade pipelines är: SQLDBToDW, DBCopy och TableCopy.

**Pipeline 1 - SQLDBToDW** letar upp namnen på klient-databaser som lagras i katalogen-databasen (tabellnamn: [__ShardManagement]. [ ShardsGlobal]) och för varje klient databas körs den **DBCopy** pipeline. När åtgärden har slutförts angiven **sp_TransformExtractedData** lagrade proceduren schema körs. Den här lagrade proceduren omvandlar inlästa data i mellanlagringstabellerna och fylls tabellerna star-schema.

**Pipeline 2 - DBCopy** letar upp namnen på källtabeller och kolumner från en konfigurationsfil som lagras i blob storage.  Den **TableCopy** pipeline körs sedan för var och en av de fyra tabellerna: TicketFacts, CustomerFacts, EventFacts och VenueFacts. Den **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** aktiviteten körs parallellt för alla 20 databaser. ADF tillåter högst 20 loop iterationer för att köras parallellt. Överväg att skapa flera pipelines för flera databaser.    

**Pipeline 3 - TableCopy** använder rad versionsnummer i SQL-databas (_rowversion_) att identifiera de rader som har ändrats eller uppdaterats. Den här aktiviteten letar upp början och slutet raden version för att extrahera rader från källtabellerna. Den **CopyTracker** tabellen som lagras i databasen för varje klient spårar den sista raden som extraheras från varje källtabellen i varje körning. Nya eller ändrade rader kopieras till motsvarande mellanlagringstabellerna i datalagret: **raw_Tickets**, **raw_Customers**, **raw_Venues**, och **raw_ Händelser**. Slutligen den senaste versionen av raden har sparats i den **CopyTracker** tabellen som ska användas som den första rad versionen för nästa extrahering. 

Det finns också tre parametrar länkade tjänster länken datafabriken käll-SQL-databaser, mål SQL Data Warehouse och mellanliggande Blob storage. I den **författaren** klickar du på **anslutningar** att utforska de länkade tjänsterna som visas i följande bild:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

Motsvarar tre länkade tjänster finns tre datauppsättningar som refererar till data som används i pipeline-aktiviteter som indata eller utdata. Utforska var och en av datauppsättningarna som du vill se anslutningar och parametrar som används. _AzureBlob_ pekar på den konfigurationsfil som innehåller käll- och tabeller och kolumner, samt kolumnen spåraren i varje källa.
  
### <a name="data-warehouse-pattern-overview"></a>Översikt över data warehouse mönster
SQL Data Warehouse används som arkivet analytics för att utföra aggregering på klientdata. I det här exemplet används PolyBase för att läsa in data till SQL Data warehouse. Rådata läses in i tillfälliga tabeller som har en identitetskolumn att hålla reda på rader som har varit omvandlas till tabellerna star-schema. Följande bild visar mönstret inläsning: ![loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

Långsamt används ändra Dimension (SCD) typ 1 dimensionstabeller i det här exemplet. Varje dimension har en surrogat definieras med hjälp av en identitetskolumn. Ett bra tips är dimensionstabellen datum i förväg för att spara tid. För andra dimensionstabeller CREATE TABLE som MARKERAR... (CTAS)-instruktion används för att skapa en tillfällig tabell som innehåller de befintliga ändrade och icke-modifierade raderna, tillsammans med surrogat nycklar. Detta görs med IDENTITY_INSERT = ON. Nya rader infogas i tabellen med IDENTITY_INSERT = OFF. För enkel återställning, byta namn på befintliga dimensionstabellen och den temporära tabellen har bytt namn till dimensionstabellen. Varje kör tas gamla dimensionstabellen bort.

Dimensionstabeller har lästs in innan faktatabellen. Den här ordningsföljd säkerställer att för varje inkommande faktum alla refererade dimensioner redan finns. Fakta läses business-nyckel för varje motsvarande dimension matchas och motsvarande surrogat nycklar läggs till varje uppgift.

Det sista steget i transformeringen tar du bort mellanlagringsplatsen data redo för nästa körning av pipeline.
   
### <a name="trigger-the-pipeline-run"></a>Utlösa pipeline kör
Följ stegen nedan för att köra det fullständiga extrahera, load and transform pipeline för alla klient-databaser:
1. I den **författaren** fliken i ADF-användargränssnittet, Välj **SQLDBToDW** pipeline i den vänstra rutan.
1. Klicka på **utlösaren** och från den ägardatabasen meny klickar du på **utlösaren nu**. Den här åtgärden kör pipeline omedelbart. I ett produktionsscenario definierar du en tidsplan för att köra pipelinen för att uppdatera data på ett schema.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. På **Pipeline kör** klickar du på **Slutför**.
 
### <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen
1. I användargränssnittet ADF växla till den **övervakaren** fliken på menyn till vänster.
1. Klicka på **uppdatera** tills SQLDBToDW pipeline status är **lyckades**.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Anslut till data warehouse med SSMS och fråga tabellerna star-schema för att verifiera att data har lästs in i dessa tabeller.

När pipelinen har slutförts faktatabellen innehåller biljett försäljningsdata för alla handelsplatser och dimensionstabeller fylls i med motsvarande handelsplatser, händelser och kunder.

## <a name="data-exploration"></a>Datagranskning

### <a name="visualize-tenant-data"></a>Visualisera klientdata

Data i stjärnan-schemat innehåller alla biljett säljdata behövs för din analys. Visualisera data grafiskt gör det lättare att se trender i stora datamängder. I det här avsnittet kan du använda **Power BI** att ändra och visualisera klientdata i datalagret.

Använd följande steg för att ansluta till Power BI och för att importera de vyer som du skapade tidigare:

1. Starta Power BI desktop.
2. Välj Home-menyfliksområdet **hämta Data**, och välj **mer...** från menyn.
3. I den **hämta Data** väljer **Azure SQL Database**.
4. Ange namnet på servern i fönstret databas inloggning (**katalog-dpt -&lt;användare&gt;. database.windows.net**). Välj **importera** för **Data anslutningsläget**, och klicka sedan på **OK**. 

    ![Logga-i-till-power-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Välj **databasen** sedan ange användarnamn i det vänstra fönstret = *developer*, och ange lösenord = *P@ssword1*. Klicka på **Anslut**.  

    ![databas-inloggning](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. I den **Navigator** rutan under analytics-databasen, Välj tabellerna som star-schema: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** och **dim_Dates**. Välj sedan **belastningen**. 

Grattis! Du har läst in data till Power BI. Utforska nu intressanta visualiseringar om du vill få insikter om dina klienter. Låt oss gå igenom hur analytics kan ge några datadrivna rekommendationer att Wingtip biljetter business-teamet. Rekommendationerna som hjälper dig för att optimera business modell och customer experience.

Starta genom att analysera biljett försäljningsdata för att se variationen i användning över handelsplatser. Välj vilka alternativ som visas i Power BI för att rita ett stapeldiagram av det totala antalet biljetter säljs av varje plats. (På grund av slumpmässig variation i biljett generator resultatet kan skilja.)
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

Föregående ritytans bekräftar att varierar antalet biljetter säljs av varje plats. Handelsplatser som säljer mer biljetter använder tjänsten tyngre än handelsplatser som säljer färre biljetter. Det kan finnas en möjlighet att skräddarsy resursallokering efter behov för olika innehavare.

Du kan ytterligare analysera data för att se hur Biljettförsäljning variera över tid. Välj de alternativ som visas i följande bild i Power BI för att rita ut det totala antalet biljetter säljs varje dag i 60 dagar.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

Föregående diagram visar att biljetten försäljning topp för vissa handelsplatser. Dessa toppar förstärka idé att vissa handelsplatser kan förbrukar systemresurser oproportionerligt. Hittills är inget uppenbara mönster när toppar inträffar.

Nästa vi undersöka betydelsen av dessa försäljning dagar med hög belastning. När dessa toppar, sker när biljetter går du vidare försäljning? Välj de alternativ som visas i följande bild i Power BI om du vill rita biljetter säljs per dag.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Det här området visar att vissa handelsplatser säljer stort antal biljetter på den första dagen i försäljning. Så snart biljetter går du vidare försäljning på dessa handelsplatser, verkar vara en får går lite för snabbt. Den här burst av aktivitet efter några handelsplatser kan det påverka tjänst för andra klienter.

Detaljer om data igen för att se om det här får går lite för snabbt gäller för alla händelser som dessa handelsplatser värd. Du såg att Contoso samklang Hall säljer många biljetter och att Contoso även har en topp i biljett sales på vissa dagar i föregående områden. Experimentera med Power BI-alternativ för att rita kumulativa Biljettförsäljning för Contoso samklang Hall, fokuserar på försäljning trender för var och en händelse. Följ samma mönster för försäljning i alla händelser? Försök att skapa en rityta som den nedan.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Område för kumulativa Biljettförsäljning över tid för Contoso samklang Hall för varje händelse visar att den får går lite för snabbt inte sker för alla händelser. Experimentera med filteralternativ att utforska försäljning trender för mera.

Insikter om biljett sälja mönster leda Wingtip biljetter för att optimera sin affärsmodell. I stället för att ladda alla klienter lika bör kanske Wingtip installera tjänstnivåer med olika prestandanivåer. Större handelsplatser som behöver sälja mer biljetter per dag skulle kunna erbjudas en högre nivå med ett högre servicenivåavtal (SLA). De handelsplatser kan ha sina databaser som placerats i pool med högre gränserna för per databas. Varje tjänstnivå kan ha en timvis allokering, med ytterligare avgifter debiteras för tilldelning. Större handelsplatser som har periodiska belastning av försäljningen skulle dra nytta av de högre nivåerna och Wingtip biljetter kan tjäna pengar på sina service mer effektivt.

Vissa kunder Wingtip biljetter klagar under tiden de behöva kämpa sälja tillräckligt med biljetter om du vill justera kostnaden för tjänsten. Kanske i dessa insikter har möjlighet att öka försäljningen biljett för presterar handelsplatser som förväntat. Högre försäljning ökar upplevd värdet för tjänsten. Högerklicka på fact_Tickets och välj **nytt mått**. Ange följande uttryck för det nya måttet som kallas **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Välj följande alternativ för visualisering att rita procentandel biljetterna säljs av varje plats för att fastställa deras relativa resultat.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

Området ovan visar att även om de flesta handelsplatser sälja mer än 80% för biljetten, vissa kämpar fylla mer än hälften deras platser. Experimentera med den värden bra att välja högsta eller lägsta procentandelen biljetter säljs för varje plats.

## <a name="embedding-analytics-in-your-apps"></a>Bädda in analytics i dina appar 
Den här självstudiekursen fokuserat på mellan klient analytics används för att förbättra programvaruleverantörens förståelse för sina klienter. Analytics kan också ge insikter om den _hyresgäster_, för att hjälpa dem att hantera deras verksamhet mer effektivt sig själva. 

I exempel Wingtip biljetter identifierade du tidigare att Biljettförsäljning tenderar att följa förutsägbar mönster. Den här informationen kan användas för att presterar handelsplatser öka biljett försäljningen som förväntat. Kanske är en möjlighet att använda machine learning-tekniker att förutsäga Biljettförsäljning för händelser. Effekterna av kursen ändras kan även modelleras så att effekten av erbjudandet rabatter förutsägas. Power BI Embedded kan integreras i en händelse hanteringsprogram visualisera förväntade försäljning, inklusive effekten av rabatter på totalt antal platser som säljs och intäkter på låg sälja händelser. Du kan även integrera faktiskt tillämpar rabatten biljett priserna, direkt visualiseringen upplevelsen med Power BI Embedded.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Distribuera ett SQL Data Warehouse med ett stjärnschema för analys av klienten.
> * Använd Azure Data Factory för att extrahera data från varje klient-databas till datalagret analytics.
> * Optimera extraherade data (reorganize till en stjärna schemat).
> * Fråga analytics-datalagret. 
> * Använd Power BI om du vill Visualisera trender i data över alla klienter.

Grattis!

## <a name="additional-resources"></a>Ytterligare resurser

- Ytterligare [självstudier som bygger på Wingtip SaaS-program](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
