---
title: Kör analysfrågor mot klient databaser med hjälp av Azure SQL Data Warehouse | Microsoft Docs
description: Flera klienter analytics-frågor med hjälp av data som extraheras från Azure SQL Database, SQL Data Warehouse, Azure Data Factory eller Power BI.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: MightyPen, sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: a658e2fe32ec95dfabad54684a0c9095af7a341d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485109"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Utforska SaaS-analyser med Azure SQL Database, SQL Data Warehouse, Data Factory och Power BI

I den här självstudien får gå igenom ett scenario för slutpunkt till slutpunkt analytics. Scenariot visar hur analytics över klientdata kan underlätta programvaruleverantörer att fatta smarta beslut. Med data som extraheras från varje klientdatabas kan använda du analytics och få insikter om klient beteendet, inklusive deras användning av Wingtip biljetter SaaS-exempelprogram. Det här scenariot omfattar tre steg: 

1.  **Extrahera data** från varje hyresgäst databasen till en analytics-butik i det här fallet ett SQL Data Warehouse.
2.  **Optimera de extrahera data** för bearbetning av trafikanalys.
3.  Använd **Business Intelligence** verktyg för att rita ut användbara insikter och hjälper beslutsfattande. 

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> - Skapa analytics lagra för inläsning av klienten.
> - Använd Azure Data Factory (ADF) för att extrahera data från varje klientdatabas i analytics data warehouse.
> - Optimera extraherade data (reorganize i ett star-schema).
> - Söka i datalagret för analys.
> - Använda Power BI för datavisualisering av för att markera trender i klientdata och ge rekommendation för förbättringarna.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analytics över extraherade klientdata

SaaS-program håller potentiellt mängder med klientorganisationens data i molnet. Dessa data kan ge insikter om åtgärden omfattande källan och användning av programmet och beteendet för dina klienter. Dessa insikter hjälper funktionsutveckling, förbättringar av användbarhet och andra investeringar i appar och plattform.

Det är enkelt att komma åt data för alla klienter när alla data är i en databas för flera innehavare. Men åtkomst är mer komplext när du har distribuerat till tusentals databaser. Ett sätt att fördjupad insikt i hantering komplexiteten är att extrahera data till en analysdatabas eller ett informationslager för frågan.

Den här kursen behandlas en slutpunkt till slutpunkt analytics-scenario för programmet Wingtip biljetter. Först [Azure Data Factory (ADF)](../data-factory/introduction.md) används som orchestration-verktyg för att extrahera biljetter försäljnings- och relaterade data från varje klientdatabas. Dessa data läses in i mellanlagringstabellerna i ett Arkiv för analys. Arkivet analytics kan antingen vara en SQL-databas eller ett SQL Data Warehouse. Den här självstudien används [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) som analyser lagra.

Sedan de extraherade data omvandlas och läses in i en uppsättning [star-schema](https://www.wikipedia.org/wiki/Star_schema) tabeller. Tabellerna bestå av en central faktatabell plus relaterade dimensionstabeller:

- Den centrala faktatabellen i star-schema innehåller data för biljetten.
- Dimensionstabellerna innehåller data om platser, händelser, kunder och köpa datum.

Tillsammans central och tabeller aktivera effektiv analytiska dimensionsbearbetning. Ett star-schema som används i den här självstudien visas i följande bild:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Slutligen tillfrågas tabellerna star-schema. Frågeresultat visas visuellt med Power BI för att markera insikter om klient beteende och deras användning av programmet. Med den här star-schema, kan du köra frågor som visar:

- Vem köpa biljetter och från vilken plats.
- Mönster och trender i försäljningen av biljetter.
- Varje lokal relativa popularitet.

Den här självstudiekursen innehåller grundläggande exempel på insikter som kan finns i Wingtip biljetter data. Förstå hur varje plats använder tjänsten kan det leda till att leverantören att tänka på olika tjänstplaner som är riktad mot mer eller mindre aktiva platser, till exempel Wingtip biljetter. 

## <a name="setup"></a>Konfiguration

### <a name="prerequisites"></a>Nödvändiga komponenter

> [!NOTE]
> Den här självstudien använder funktioner i Azure Data Factory som finns för närvarande i en begränsad förhandsversion (länkad tjänst parameterisering). Om du vill följa den här guiden kan du ange ditt prenumerations-ID [här](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Vi skickar en bekräftelse när prenumerationen har aktiverats.

Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:
- Wingtip biljetter SaaS databas Per klient-programmet har distribuerats. Om du vill distribuera i mindre än fem minuter [distribuera och utforska Wingtip SaaS-program](saas-dbpertenant-get-started-deploy.md).
- Wingtip biljetter SaaS databas Per klient skript och programmet [källkod](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) laddas ned från GitHub. Se Hämta instruktioner. Se till att *avblockera zip-filen* innan du extrahera dess innehåll.
- Power BI Desktop har installerats. [Hämta Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- Batch med ytterligare klienter har etablerats kan du läsa den [ **etablera klienter självstudien**](saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Skapa data för presentationen

Den här guiden utforskar analytics över försäljningsdata för biljett. I det här steget ska generera du biljett data för alla klienter. I ett senare steg extraheras dessa data för analys. _Se till att du har etablerat batch med klienter_ (enligt beskrivningen tidigare) så att du har tillräckligt med data för att exponera en mängd olika biljett inköpsmönster.

1. I PowerShell ISE öppnar *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*, och ange följande värde:
    - **$DemoScenario** = **1** köp biljetter för evenemang på alla platser
2. Tryck på **F5** att köra skriptet och skapa biljettköpshistorik för alla platser. Med 20-klienter, skriptet genererar tiotusentals biljetter och kan ta 10 minuter eller mer.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>Distribuera SQL Data Warehouse använder Data Factory och Blob-lagring 
I appen Wingtip biljetter distribueras klienternas transaktionsdata över flera databaser. Azure Data Factory (ADF) används för att dirigera extrahering, inläsning och transformera (ELT) av dessa data till datalagret. Om du vill läsa in data i SQL Data Warehouse mest effektivt ADF extraherar data till mellanliggande-blobfiler och använder sedan [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) att läsa in data till datalagret.   

I det här steget ska du distribuera ytterligare resurser som används i självstudien: ett SQL Data Warehouse kallas _tenantanalytics_, en Azure Data Factory kallas _dbtodwload -\<användaren\>_  , och ett Azure storage-konto med namnet _wingtipstaging\<användaren\>_. Lagringskontot används för att tillfälligt lagra extraherade filer som blobar innan de läses in i informationslagret. Det här steget också distribuerar data warehouse-schemat och definierar ADF-pipelines som samordnar ELT-processen.
1. I PowerShell ISE öppnar *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* och ange:
    - **$DemoScenario** = **2** distribuera klient analytics data warehouse, blob-lagring och data factory 
1. Tryck på **F5** att köra demo-skriptet och distribuera Azure-resurser. 

Nu ska du granska resurserna som du har distribuerat:
#### <a name="tenant-databases-and-analytics-store"></a>Klient-databaser och analytics store
Använd [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) att ansluta till **tenants1-dpt -&lt;användaren&gt;**  och **catalog-dpt -&lt;användare&gt;**  servrar. Ersätt &lt;användaren&gt; med det värde som används när du distribuerade appen. Använda inloggning = *developer* och lösenord = *P\@ssword1*. Se den [inledande självstudien](saas-dbpertenant-wingtip-app-overview.md) för mer information.

![Ansluta till SQL Database-server från SSMS](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

I Object Explorer:

1. Expandera den *tenants1-dpt -&lt;användaren&gt;*  server.
1. Expandera noden databaser och se en lista över klientdatabaser.
1. Expandera den *catalog-dpt -&lt;användaren&gt;*  server.
1. Kontrollera att du ser analytics lagra som innehåller följande objekt:
    1. Tabeller **raw_Tickets**, **raw_Customers**, **raw_Events** och **raw_Venues** håller extraherade rådata från klientdatabaserna.
    1. Star-schema-tabeller är **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, och **dim_Dates** .
    1. Den lagrade proceduren **sp_transformExtractedData** används för att omvandla data och läsa in den i tabellerna star-schema.

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage
1. I den [Azure-portalen](https://ms.portal.azure.com), navigera till den resursgrupp som du använde för att distribuera programmet. Kontrollera att ett lagringskonto med namnet **wingtipstaging\<användaren\>**  har lagts till.

   ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Klicka på **wingtipstaging\<användaren\>**  lagringskonto för att utforska objekt som finns.
1. Klicka på **Blobar** panelen
1. Klicka på behållaren **configfile**
1. Kontrollera att **configfile** innehåller en JSON-fil som heter **TableConfig.json**. Den här filen innehåller käll- och tabellnamn, kolumnnamn och spåraren kolumnnamn.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)
I den [Azure-portalen](https://ms.portal.azure.com) i resursgruppen, kontrollerar du att en Azure Data Factory heter _dbtodwload -\<användaren\>_  har lagts till. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

Det här avsnittet utforskar data factory skapas. Följ stegen nedan för att starta data factory:
1. I portalen klickar du på datafabriken som kallas **dbtodwload -\<användaren\>**.
2. Klicka på **författare och Övervakare** att starta i Data Factory-designer i en separat flik. 

## <a name="extract-load-and-transform-data"></a>Extrahera, belastning, och omvandla data
Azure Data Factory används för att samordna extrahering, inläsning och transformering av data. I den här självstudien får du extrahera data från fyra olika SQL-vyer från varje klientdatabaserna: **rawTickets**, **rawCustomers**, **rawEvents**, och  **rawVenues**. Bland dessa vyer finns plats-Id, så att du kan skilja data från varje plats i datalagret. Data läses in i motsvarande mellanlagringstabellerna i datalagret: **raw_Tickets**, **raw_customers**, **raw_Events** och **raw_Venue**. En lagrad procedur sedan omvandlar rådata och fylls tabellerna star-schema: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** , och **dim_Dates**.

I föregående avsnitt, distribueras och initiera Azure-resurser, inklusive data factory. Distribuerad data factory innehåller pipelines, datauppsättningar, länkade tjänster, osv., krävs för att extrahera, läsa in och transformera data för klientorganisationen. Låt oss utforska dessa ytterligare objekt och utlösa pipelinen att flytta data från klientdatabaser till datalagret.

### <a name="data-factory-pipeline-overview"></a>Översikt över data factory-pipeline
Det här avsnittet förklaras de objekt som skapas i data factory. Följande bild beskrivs det totala arbetsflödet för ADF-pipelinen som används i den här självstudien. Om du vill utforska pipelinen senare och se resultaten först gå vidare till nästa avsnitt **utlösa pipelinekörningen**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

I översiktssidan, växlar du till **författare** fliken på den vänstra panelen och notera att det finns tre [pipelines](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) och tre [datauppsättningar](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) skapas.
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

Det finns tre kapslade pipelines: SQLDBToDW DBCopy och TableCopy.

**Pipeline 1 – SQLDBToDW** letar upp namnen på klientdatabaserna lagras i katalogdatabasen (tabellnamn: [__ShardManagement]. [ ShardsGlobal]) och varje klientdatabas kör den **DBCopy** pipeline. När åtgärden har slutförts angiven **sp_TransformExtractedData** lagrade proceduren schema körs. Den här lagrade proceduren omvandlar inlästa data i mellanlagringstabellerna och fylls tabellerna star-schema.

**Pipeline 2 – DBCopy** letar upp namnen på källtabeller och kolumner från en konfigurationsfil som lagras i blob storage.  Den **TableCopy** sedan körs pipelinen för var och en av de fyra tabellerna: TicketFacts, CustomerFacts, EventFacts och VenueFacts. Den **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** aktivitet körs parallellt för alla 20 databaser. ADF tillåter högst 20 loop iterationer för att köras parallellt. Överväg att skapa flera pipelines för flera databaser.    

**Pipeline 3 – TableCopy** använder rad versionsnummer i SQL-databas (_rowversion_) att identifiera rader som har ändrats eller uppdaterats. Den här aktiviteten söker upp början och slutet rad versionen för att extrahera rader från källtabellerna. Den **CopyTracker** tabell som lagras i varje klientdatabas spårar den sista raden som extraheras från varje källtabell i varje körning. Nya eller ändrade rader ska kopieras till motsvarande mellanlagringstabellerna i datalagret: **raw_Tickets**, **raw_Customers**, **raw_Venues**, och **raw_ Händelser**. Slutligen den senaste versionen av raden som sparas i den **CopyTracker** tabellen som ska användas som den första rad versionen för nästa utvinning. 

Det finns också tre parametriserade länkade tjänster länken datafabriken käll-SQL-databaser, mål SQL Data Warehouse och mellanliggande Blob-lagringen. I den **författare** fliken, klickar du på **anslutningar** att utforska de länkade tjänsterna, enligt följande bild:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

Motsvarar tre länkade tjänster, finns det tre datauppsättningar som refererar till data som används i pipelineaktiviteter som indata eller utdata. Utforska var och en av datauppsättningarna att Observera anslutningar och parametrar som används. _AzureBlob_ pekar på den konfigurationsfil som innehåller käll- och tabeller och kolumner, samt spåraren kolumn i varje källa.
  
### <a name="data-warehouse-pattern-overview"></a>Översikt över mönstret för data warehouse
SQL Data Warehouse används som arkivet analytics för att utföra aggregering på data för klientorganisationen. I det här exemplet används PolyBase för att läsa in data till SQL Data warehouse. Rådata läses in i mellanlagringstabeller som har en identity-kolumn för att hålla reda på rader som har omvandlats till tabellerna star-schema. Följande bild visar mönstret inläsning: ![loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

Långsamt används dimensionstabeller för ändra Dimension (SCD) typ 1 i det här exemplet. Varje dimension har en surrogatnyckel som definieras med hjälp av en identitetskolumn. Som bästa praxis är dimensionstabellen datum i förväg för att spara tid. För andra dimensionstabeller skapa TABLE AS SELECT... Instruktionen (CTAS) används för att skapa en tillfällig tabell som innehåller de befintliga ändrade och icke-modifierade raderna, tillsammans med surrogate-nycklar. Detta görs med IDENTITY_INSERT = ON. Nya rader infogas sedan i tabellen med IDENTITY_INSERT = OFF. För enkelt återställa befintliga dimensionstabellen har bytt namn och den tillfälliga tabellen har bytt namn till den nya dimensionstabellen. Den gamla dimensionstabellen tas bort innan varje körning.

Dimensionstabeller har lästs in före faktatabellen. Den här sekvensering säkerställer att varje inkommande att alla refererade dimensioner redan finns. Fakta läses Företagsnyckel för varje motsvarande dimension matchas och motsvarande surrogate nycklarna läggs till i varje fakta.

Det sista steget av transformeringen tar bort tillfälliga data som är redo för nästa körning av pipelinen.
   
### <a name="trigger-the-pipeline-run"></a>Utlös pipelinen kör
Följ stegen nedan för att köra den fullständiga extrahera, inläsning och transformering pipeline för alla klientdatabaser:
1. I den **författare** användargränssnittet ADF, som du väljer fliken **SQLDBToDW** pipeline i den vänstra rutan.
1. Klicka på **utlösaren** och från den ägardatabasen menyn klickar du på **Utlös nu**. Den här åtgärden kör pipelinen omedelbart. I ett produktionsscenario definierar du en tidsplan för att köra pipelinen för att uppdatera data enligt ett schema.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. På **Pipelinekörning** klickar du på **Slutför**.
 
### <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen
1. I användargränssnittet ADF växlar du till den **övervakaren** fliken på menyn till vänster.
1. Klicka på **uppdatera** tills SQLDBToDW pipelinens status är **lyckades**.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Ansluta till informationslagret med SSMS och fråga tabellerna star-schema för att verifiera att data har lästs in i dessa tabeller.

När pipelinen har slutförts faktatabellen innehåller försäljningsdata för biljett för alla platser och dimensionstabellerna fylls med motsvarande platser, händelser och kunder.

## <a name="data-exploration"></a>Datautforskning

### <a name="visualize-tenant-data"></a>Visualisera data för klientorganisationen

Data i star-schema innehåller alla biljett försäljningsdata behövs för din analys. Visualisera data grafiskt gör det lättare att se trender i stora datamängder. I det här avsnittet ska du använda **Power BI** att manipulera och visualisera data för klientorganisationen i datalagret.

Använd följande steg för att ansluta till Power BI och för att importera de vyer som du skapade tidigare:

1. Starta Power BI desktop.
2. Välj Home-menyfliksområdet och **hämta Data**, och välj **mer...** på menyn.
3. I den **hämta Data** väljer **Azure SQL Database**.
4. Ange namnet på servern i inloggningsfönstret databasen (**catalog-dpt -&lt;användaren&gt;. database.windows.net**). Välj **Import** för **läge för dataanslutning**, och klicka sedan på **OK**. 

    ![sign-in-to-power-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Välj **databasen** i den vänstra rutan, ange användarnamn = *developer*, och ange lösenord = *P\@ssword1*. Klicka på **Anslut**.  

    ![database-sign-in](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. I den **Navigator** rutan under analysdatabas, Välj tabellerna som star-schema: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** och **dim_Dates**. Välj sedan **belastningen**. 

Grattis! Du har läst in data i Power BI. Utforska intressanta visualiseringar för att få insikter om dina klienter. Låt oss gå igenom hur analytics kan ge några datadrivna rekommendationer att företagsteamet Wingtip biljetter. Rekommendationer kan hjälpa till att optimera företag modell och kundernas upplevelse.

Börja med att analysera försäljningsdata för biljett för att se variationen i användning på platser. Välj alternativen som visas i Power BI för att rita ett stapeldiagram av det totala antalet ärenden som säljs av varje plats. (Dina resultat på grund av slumpmässiga variation i biljett generator kan vara annorlunda.)
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

Föregående området bekräftar att Antal biljetter som säljs av varje lokal varierar. Platser som säljer mer biljetter använder tjänsten tyngre än platser som säljer färre biljetter. Det kan finnas en möjlighet att skräddarsy resursallokering efter behov för annan klient.

Du kan ytterligare analysera data om du vill se hur biljettförsäljningar variera över tid. Välj alternativen som visas i följande bild i Power BI för att rita det totala antalet ärenden som säljs varje dag under en period på 60 dagar.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

Föregående diagram visar den biljett försäljning topp för vissa platser. Dessa toppar förstärka idé att vissa platser kan förbruka systemresurser oproportionerligt. Än så länge finns det inget uppenbart mönster i när toppar som inträffar.

Nästa ska vi undersöka betydelsen av dessa dagar för försäljning med hög belastning. När uppstår dessa toppar när biljetter? Välj alternativen som visas i följande bild i Power BI om du vill rita biljetter som säljs per dag.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Det här området visar att vissa platser sälja stort antal biljetter på den första dagen i försäljning. När biljetter går vid försäljning på dessa platser, verkar det vara får gott om tid. Den här burst av aktivitet av några platser kan påverka tjänsten för andra klienter.

Du kan se data igen för att se om det här får gott om tid gäller för alla händelser som är värd för dessa platser. I föregående diagrammen såg du att Contosos Konserthall säljer många biljetter och att Contoso har också en topp i biljettförsäljningar på vissa dagar. Experimentera med Power BI-alternativ för att rita kumulativ biljettförsäljningar för Contosos Konserthall fokusera på försäljningen trender för var och en av händelserna. Följ samma mönster för försäljningen i alla händelser? Försök att skapa en rityta se nedan.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Den här exempeldiagram av ackumulerad biljettförsäljningar över tid för Contosos Konserthall för varje händelse visar att den får gott om tid inte sker för alla händelser. Experimentera med filteralternativen att utforska försäljning trender för andra platser.

Insikter om biljett sälja mönster kan leda Wingtip biljetter för att optimera sin affärsmodell. I stället för att debitera alla klienter lika ska kanske Wingtip införa tjänstnivåer med olika storlekar. Större platser som behöver för att sälja mer biljetter per dag skulle kunna erbjudas en högre nivå med ett högre servicenivåavtal (SLA). Dessa platser kan ha sina databaser som placerats i pool med högre resursgränser för per databas. Varje tjänstnivå kan ha en försäljning per timme allokering, med ytterligare avgifter som debiteras för som överskrider allokeringen. Större platser som har periodiska toppar för försäljning skulle ha nytta av de högsta nivåerna och Wingtip biljetter kan tjäna pengar på deras tjänst mer effektivt.

Vissa kunder Wingtip biljetter börjar under tiden kan klaga att de behöva kämpa för att sälja tillräckligt med biljetter för att motivera kostnaden för tjänsten. Kanske i dessa insikter finns en möjlighet att öka efter presterar som förväntat platser. Högre försäljning ökar upplevd värdet av tjänsten. Högerklicka på fact_Tickets och välj **nytt mått**. Ange följande uttryck för det nya måttet namnet **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Välj följande alternativ för visualisering att rita de procentandel biljetter som säljs av varje plats för att fastställa deras relativa framgång.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

Området ovan visar att även om de flesta platser sälja mer än 80% för biljetten, vissa kämpar för att fylla i mer än hälften sina platser. Experimentera med de värden bra att välja högsta eller lägsta procentandel av biljetter som säljs för varje plats.

## <a name="embedding-analytics-in-your-apps"></a>Bädda in analyser i dina appar 
Den här självstudien har fokuserar på flera klienter analytics används för att förbättra programvaruleverantörens förståelse för sina klienter. Analytics kan även ange information om den _klienter_, för att hjälpa dem att hantera sin verksamhet mer effektivt själva. 

I exemplet Wingtip biljetter identifierade du tidigare att biljettförsäljningar tenderar att följer förutsägbara mönster. Den här informationen kan användas för att presterar platser boost biljettförsäljningar som förväntat. Det finns kanske en möjlighet att använda machine learning-teknik för att förutsäga efter händelser. Effekterna av prisförändringar kan också modelleras så att effekten av erbjuder rabatter på att förutse. Power BI Embedded kan integreras i en händelse hanteringsprogram att visualisera förväntad försäljning, inklusive effekten av rabatter på totalt antal platser som säljs och intäkter på låg sälja händelser. Du kan även integrera faktiskt tillämpar rabatten för biljett priserna, direkt i visualisering med Power BI Embedded.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Distribuera ett SQL Data Warehouse som fyllts med ett star-schema för klientanalys.
> * Använd Azure Data Factory för att extrahera data från varje klientdatabas i analytics data warehouse.
> * Optimera extraherade data (reorganize i ett star-schema).
> * Söka i datalagret för analys. 
> * Använd Power BI för att visualisera trender i data för alla klienter.

Grattis!

## <a name="additional-resources"></a>Ytterligare resurser

- Ytterligare [självstudier som bygger på Wingtip SaaS-program](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
