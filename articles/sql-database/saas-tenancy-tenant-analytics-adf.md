---
title: Köra analysfrågor mot klientdatabaser
description: Analysfrågor för flera innehavare med hjälp av data som extraherats från Azure SQL Database, SQL Data Warehouse, Azure Data Factory eller Power BI.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: MightyPen, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 4791cd3a6b6f72c5d9ee4ca828d66b0d361f356c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73816768"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Utforska SaaS-analyser med Azure SQL Database, SQL Data Warehouse, Data Factory och Power BI

I den här självstudien går du igenom ett heltäckande analysscenario. Scenariot visar hur analyser över klientdata kan ge programvaruleverantörer möjlighet att fatta smarta beslut. Med hjälp av data som extraheras från varje klientdatabas använder du analyser för att få insikter om klientbeteende, inklusive deras användning av exempelprogrammet Wingtip Tickets SaaS. Det här scenariot omfattar tre steg: 

1.  **Extrahera data** från varje klientdatabas till ett analyslager, i det här fallet ett SQL Data Warehouse.
2.  **Optimera extraherade data** för analysbearbetning.
3.  Använd **Business Intelligence-verktyg** för att ta fram användbara insikter som kan vägleda beslutsfattandet. 

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> - Skapa klientanalysarkivet för inläsning.
> - Använd Azure Data Factory (ADF) för att extrahera data från varje klientdatabas till analysdatalagret.
> - Optimera extraherade data (omorganisera till ett stjärnschema).
> - Fråga analysdatalagret.
> - Använd Power BI för datavisualisering för att markera trender i klientdata och ge rekommendation för förbättringar.

![arkitekturÖversikt](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analys över extraherade klientdata

SaaS-program innehåller en potentiellt stor mängd klientdata i molnet. Dessa data kan ge en rik källa till insikter om hur ditt program fungerar och används och hur dina klienter fungerar. Dessa insikter kan vägleda funktionsutveckling, användbarhetsförbättringar och andra investeringar i appar och plattformar.

Det är enkelt att komma åt data för alla klienter när alla data finns i bara en databas med flera innehavare. Men åtkomsten är mer komplex när den distribueras i stor skala över tusentals databaser. Ett sätt att tämja komplexiteten är att extrahera data till en analysdatabas eller ett informationslager för fråga.

Den här självstudien presenterar ett end-to-end-analysscenario för Programmet Wingtip Tickets. Först azure [data factory (ADF)](../data-factory/introduction.md) används som orkestreringsverktyg för att extrahera biljetter försäljning och relaterade data från varje klientdatabas. Dessa data läses in i mellanlagringstabeller i ett analyslager. Analysarkivet kan antingen vara en SQL-databas eller ett SQL Data Warehouse. Den här självstudien använder [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) som analyslager.

Därefter omvandlas extraherade data och läses in till en uppsättning [stjärnschematabeller.](https://www.wikipedia.org/wiki/Star_schema) Tabellerna består av en central faktatabell plus relaterade dimensionstabeller:

- Den centrala faktatabellen i stjärnschemat innehåller biljettdata.
- Dimensionstabellerna innehåller data om platser, händelser, kunder och inköpsdatum.

Tillsammans möjliggör de centrala tabellerna och dimensionstabellerna effektiv analytisk bearbetning. Stjärnschemat som används i den här självstudien visas i följande bild:
 
![arkitekturÖversikt](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Slutligen efterfrågas stjärnschematabellerna. Frågeresultat visas visuellt med Power BI för att markera insikter om klientbeteende och deras användning av programmet. Med det här stjärnschemat kör du frågor som exponerar:

- Vem köper biljetter och från vilken plats.
- Mönster och trender i försäljningen av biljetter.
- Den relativa populariteten för varje plats.

Den här självstudien innehåller grundläggande exempel på insikter som kan hämtas från Wingtip-biljetter-data. Om du förstår hur varje plats använder tjänsten kan wingtip-biljettleverantören tänka på olika serviceplaner som är inriktade på mer eller mindre aktiva platser, till exempel. 

## <a name="setup"></a>Installation

### <a name="prerequisites"></a>Krav

> [!NOTE]
> Den här självstudien använder funktioner i Azure Data Factory som för närvarande finns i en begränsad förhandsversion (länkad tjänstparametrisering). Om du vill göra den här självstudien, ange ditt prenumerations-ID [här](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Vi skickar en bekräftelse så snart din prenumeration har aktiverats.

Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:
- Wingtip-biljetterna SaaS-databasen per klientprogram distribueras. Information om hur du distribuerar på mindre än fem minuter finns i [Distribuera och utforska Wingtip SaaS-programmet](saas-dbpertenant-get-started-deploy.md).
- Wingtip-biljetterna SaaS Database Per Tenant-skript och [programkällakod](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) hämtas från GitHub. Se hämtningsinstruktioner. Var noga med att *låsa upp zip-filen* innan du extraherar dess innehåll.
- Power BI Desktop är installerat. [Ladda ned Power BI-skrivbordet](https://powerbi.microsoft.com/downloads/).
- Batchen med ytterligare klienter har etablerats, se [**självstudien för tillhandahållande klienter**](saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Skapa data för demon

Den här självstudien utforskar analyser över biljettförsäljningsdata. I det här steget genererar du biljettdata för alla klienter. I ett senare steg extraheras dessa data för analys. _Se till att du har etablerat batchen med klienter_ (som beskrivits tidigare) så att du har tillräckligt med data för att exponera en rad olika biljettköpsmönster.

1. Öppna *...\Utbildningsmoduler\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*i PowerShell ISE och ange följande värde:
    - **$DemoScenario**1 Köp biljetter till evenemang på alla arenor**1**  = 
2. Tryck på **F5** för att köra skriptet och skapa biljettinköpshistorik för alla arenor. Med 20 hyresgäster genererar skriptet tiotusentals biljetter och kan ta 10 minuter eller mer.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>Distribuera SQL Data Warehouse, Data Factory och Blob Storage 
I appen Wingtip-biljetter distribueras hyresgästernas transaktionsdata över många databaser. Azure Data Factory (ADF) används för att dirigera extrahera, läsa in och omvandla (ELT) för dessa data till informationslagret. För att läsa in data i SQL Data Warehouse mest effektivt extraherar ADF data till mellanliggande blob-filer och använder sedan [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) för att läsa in data i informationslagret.   

I det här steget distribuerar du ytterligare resurser som används i självstudien: ett SQL Data Warehouse som kallas _tenantanalytics_, en Azure Data Factory som kallas _dbtodwload-\<användare\>_ och ett Azure-lagringskonto som kallas _wingtipstaging-användare\<\>_. Lagringskontot används för att tillfälligt lagra extraherade datafiler som blobbar innan de läses in i informationslagret. Det här steget distribuerar också datalagerschemat och definierar ADF-pipelines som dirigerar ELT-processen.
1. I PowerShell ISE öppnar *du ...\Utbildningsmoduler\Operativa analyser\Klientanalys DW\Demo-TenantAnalyticsDW.ps1* och anger:
    - **$DemoScenario**2 Distribuera datalager för klientanalys, bloblagring och datafabrik**2**  =  
1. Tryck på **F5** för att köra demoskriptet och distribuera Azure-resurser. 

Granska nu de Azure-resurser som du har distribuerat:
#### <a name="tenant-databases-and-analytics-store"></a>Klientdatabaser och analysarkiv
Använd [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) för att ansluta till **klienterna1-dpt-&lt;användare&gt; ** och **&lt;katalog-dpt-användarservrar.&gt; ** Ersätt &lt;&gt; användaren med det värde som användes när du distribuerade appen. Använd Login = *utvecklare* och lösenord = *P\@ssword1*. Mer vägledning finns i den [inledande självstudien.](saas-dbpertenant-wingtip-app-overview.md)

![Ansluta till SQL Database-server från SSMS](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

I objektutforskaren:

1. Expandera *klienterna1-dpt-användarservern.&lt;&gt; *
1. Expandera noden Databaser och se listan över klientdatabaser.
1. Expandera *&lt;&gt; katalog-dpt-användarservern.*
1. Kontrollera att du ser analysarkivet som innehåller följande objekt:
    1. Tabeller **raw_Tickets**, **raw_Customers**, **raw_Events** och **raw_Venues** innehåller råextraherade data från klientdatabaserna.
    1. Stjärnschematabellerna är **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**och **dim_Dates**.
    1. Den lagrade **proceduren, sp_transformExtractedData** används för att omvandla data och läsa in dem i stjärnschematabellerna.

![DWtables (DWtables)](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage
1. I [Azure Portal](https://ms.portal.azure.com)navigerar du till resursgruppen som du använde för att distribuera programmet. Kontrollera att ett lagringskonto som kallas **wingtipstaging-användare\<\> ** har lagts till.

   ![DWtables (DWtables)](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Klicka på **wingtipstaging\<\> användarkonto** för att utforska de objekt som finns.
1. Klicka på panelen **Blobbar**
1. Klicka på **behållarkonfigurationen**
1. Kontrollera att **configfile** innehåller en JSON-fil som heter **TableConfig.json**. Den här filen innehåller käll- och måltabellnamn, kolumnnamn och trackerkolumnnamn.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)
Kontrollera att en Azure Data Factory som kallas _dbtodwload-\<\> användare_ har lagts till i [Azure Portal](https://ms.portal.azure.com) i resursgruppen. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

I det här avsnittet beskrivs den datafabrik som skapats. Följ stegen nedan för att starta datafabriken:
1. I portalen klickar du på datafabriken **dbtodwload-\<user\>**.
2. Klicka på **Panelen Författare & Bildskärm** om du vill starta datafabriksdesignern på en separat flik. 

## <a name="extract-load-and-transform-data"></a>Extrahera, läsa in och omforma data
Azure Data Factory används för att dirigera extrahering, inläsning och omvandling av data. I den här självstudien extraherar du data från fyra olika SQL-vyer från var och en av **klientdatabaserna: rawTickets,** **rawCustomers**, **rawEvents**och **rawVenues**. Dessa vyer inkluderar plats-ID, så att du kan diskriminera data från varje plats i informationslagret. Data läses in i motsvarande mellanlagringstabeller i informationslagret: **raw_Tickets**, **raw_customers**, **raw_Events** och **raw_Venue**. En lagrad procedur omvandlar sedan rådata och fyller i stjärnschematabellerna: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**och **dim_Dates**.

I föregående avsnitt har du distribuerat och initierat nödvändiga Azure-resurser, inklusive datafabriken. Den distribuerade datafabriken innehåller pipelines, datauppsättningar, länkade tjänster osv. Låt oss utforska dessa objekt ytterligare och sedan utlösa pipelinen för att flytta data från klientdatabaser till informationslagret.

### <a name="data-factory-pipeline-overview"></a>Översikt över pipelinen för datafabrik
I det här avsnittet utforskas de objekt som skapats i datafabriken. Följande bild beskriver det övergripande arbetsflödet för ADF-pipelinen som används i den här självstudien. Om du föredrar att utforska pipelinen senare och se resultatet först, hoppa till nästa avsnitt **Utlösa pipeline-körningen**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

På översiktssidan växlar du till fliken **Författare** på den vänstra panelen och observerar att tre [pipelines](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) och tre [datauppsättningar har skapats.](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

De tre kapslade pipelines är: SQLDBToDW, DBCopy och TableCopy.

**Pipeline 1 - SQLDBToDW** slår upp namnen på klientdatabaserna som lagras i katalogdatabasen (tabellnamn: [__ShardManagement].[ ShardsGlobal]) och för varje klientdatabas kör **DBCopy-pipelinen.** När det angivna sp_TransformExtractedData lagrade procedureschemat har slutförts körs det angivna **sp_TransformExtractedData** det lagrade procedurschemat. Den här lagrade proceduren omvandlar inlästa data i mellanlagringstabellerna och fyller i stjärnschematabellerna.

**Pipeline 2 - DBCopy** slår upp namnen på källtabellerna och kolumnerna från en konfigurationsfil som lagras i blob-lagring.  **TableCopy-pipelinen** körs sedan för var och en av de fyra tabellerna: TicketFacts, CustomerFacts, EventFacts och VenueFacts. **[Foreach-aktiviteten](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** körs parallellt för alla 20 databaser. ADF tillåter att maximalt 20 loop iterationer körs parallellt. Överväg att skapa flera pipelines för fler databaser.    

**Pipeline 3 - TableCopy** använder radversionsnummer i SQL Database (_rowversion_) för att identifiera rader som har ändrats eller uppdaterats. Den här aktiviteten söker upp start- och slutradsversionen för att extrahera rader från källtabellerna. **CopyTracker-tabellen** som lagras i varje klientdatabas spårar den sista raden som extraherades från varje källtabell i varje körning. Nya eller ändrade rader kopieras till motsvarande mellanlagringstabeller i informationslagret: **raw_Tickets**, **raw_Customers**, **raw_Venues**och **raw_Events**. Slutligen sparas den sista radversionen i tabellen **CopyTracker** som ska användas som den första radversionen för nästa extrahering. 

Det finns också tre parameteriserade länkade tjänster som länkar datafabriken till källan SQL-databaser, målet SQL Data Warehouse och mellanliggande Blob-lagring. På fliken **Författare** klickar du på **Anslutningar** för att utforska de länkade tjänsterna, som visas i följande bild:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

Som motsvarar de tre länkade tjänsterna finns det tre datauppsättningar som refererar till de data som du använder i pipeline-aktiviteter som indata eller utdata. Utforska var och en av datauppsättningarna för att observera anslutningar och parametrar som används. _AzureBlob_ pekar på konfigurationsfilen som innehåller käll- och måltabeller och kolumner, samt uppföljningskolumnen i varje källa.
  
### <a name="data-warehouse-pattern-overview"></a>Översikt över datalagermönster
SQL Data Warehouse används som analyslag för att utföra aggregering på klientdata. I det här exemplet används PolyBase för att läsa in data i SQL Data-lagret. Rådata läses in i mellanlagringstabeller som har en identitetskolumn för att hålla reda på rader som har omvandlats till stjärnschematabellerna. Följande bild visar läsmönstret: ![loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

Dimensioneringstabeller (Långsamt ändra dimension) används i det här exemplet. Varje dimension har en surrogatnyckel definierad med hjälp av en identitetskolumn. Som bästa praxis är datumdimensionstabellen förifylld för att spara tid. För de andra dimensionstabellerna finns en SKAPA-TABELL SOM SELECT... (CTAS)-sats används för att skapa en temporär tabell som innehåller befintliga ändrade och icke-modifierade rader, tillsammans med surrogatnycklarna. Detta görs med IDENTITY_INSERT=ON. Nya rader infogas sedan i tabellen med IDENTITY_INSERT=AV. För enkel återställning får den befintliga dimensionstabellen ett nytt namn och den temporära tabellen har bytt namn till den nya dimensionstabellen. Före varje körning tas den gamla dimensionstabellen bort.

Dimensionstabeller läses in före faktatabellen. Denna sekvensering säkerställer att för varje ankommande faktum, alla refererade dimensioner redan finns. När fakta läses in matchas affärsnyckeln för varje motsvarande dimension och motsvarande surrogatnycklar läggs till i varje faktum.

Det sista steget i transformeringen tar bort mellanlagringsdata som är redo för nästa körning av pipelinen.
   
### <a name="trigger-the-pipeline-run"></a>Utlösa pipelinekörningen
Följ stegen nedan för att köra hela extrahera, läsa in och omvandla pipeline för alla klientdatabaser:
1. På fliken **Författare** i ADF-användargränssnittet väljer du **SQLDBToDW-pipelinen** i den vänstra rutan.
1. Klicka på **Utlösare** och från den neddragna menyn klickar du på **Utlösa nu**. Den här åtgärden kör pipelinen omedelbart. I ett produktionsscenario definierar du en tidsplan för att köra pipelinen för att uppdatera data enligt ett schema.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. Klicka på **Slutför**på sidan **Pipeline run.**
 
### <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen
1. I ADF-användargränssnittet växlar du till fliken **Bildskärm** från menyn till vänster.
1. Klicka på **Uppdatera** tills SQLDBToDW-pipelinens status **har lyckades**.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Anslut till informationslagret med SSMS och fråga stjärnschematabellerna för att kontrollera att data lästes in i dessa tabeller.

När pipelinen har slutförts innehåller faktatabellen biljettförsäljningsdata för alla platser och dimensionstabellerna fylls med motsvarande platser, händelser och kunder.

## <a name="data-exploration"></a>Data Exploration

### <a name="visualize-tenant-data"></a>Visualisera klientdata

Data i stjärnschemat innehåller alla biljettförsäljningsdata som behövs för din analys. Genom att visualisera data grafiskt blir det lättare att se trender i stora datauppsättningar. I det här avsnittet använder du **Power BI** för att manipulera och visualisera klientdata i informationslagret.

Använd följande steg för att ansluta till Power BI och importera de vyer som du skapade tidigare:

1. Starta Power BI-skrivbordet.
2. Välj **Hämta data**i menyfliksområdet Hem och välj **Mer...** från menyn.
3. Välj **Azure SQL Database**i fönstret Hämta **data** .
4. I databasinloggningsfönstret anger du servernamnet **(catalog-dpt-&lt;&gt;Användare .database.windows.net**). Välj **Importera** för **dataanslutningsläge**och klicka sedan på **OK**. 

    ![logga in-till-power-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Välj **Databas** i den vänstra rutan och ange sedan användarnamn = *utvecklare*och ange lösenord = *P\@ssword1*. Klicka på **Anslut**.  

    ![databas-logga in](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. I **fönstret Navigatör,** under analysdatabasen, väljer du stjärnschematabellerna: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** och **dim_Dates**. Välj sedan **Läs in**. 

Grattis! Du har läst in data i Power BI. Utforska nu intressanta visualiseringar för att få insikter i dina hyresgäster. Låt oss gå igenom hur analyser kan ge vissa datadrivna rekommendationer till Wingtip Tickets affärsteam. Rekommendationerna kan bidra till att optimera affärsmodellen och kundupplevelsen.

Börja med att analysera biljettförsäljningsdata för att se variationen i användningen mellan arenorna. Välj de alternativ som visas i Power BI om du vill rita ett stapeldiagram över det totala antalet biljetter som säljs av varje plats. (På grund av slumpmässig variation i biljettgeneratorn kan dina resultat vara annorlunda.)
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

Den föregående tomten bekräftar att antalet biljetter som säljs av varje plats varierar. Arenor som säljer fler biljetter använder din tjänst tyngre än arenor som säljer färre biljetter. Det kan finnas en möjlighet här att skräddarsy resursallokering efter olika klientbehov.

Du kan ytterligare analysera data för att se hur biljettförsäljningen varierar över tiden. Välj de alternativ som visas i följande bild i Power BI om du vill rita det totala antalet biljetter som sålts varje dag under en period av 60 dagar.
 
![FörsäljningVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

Föregående diagram visar att biljettförsäljningen ökar för vissa platser. Dessa spikar förstärker tanken att vissa arenor kan konsumera systemresurser oproportionerligt. Hittills finns det inget uppenbart mönster i när spikar inträffar.

Nästa låt oss undersöka betydelsen av dessa topp försäljning dagar. När sker dessa toppar efter att biljetterna börjar säljas? Om du vill rita upp biljetter som säljs per dag väljer du de alternativ som visas i följande bild i Power BI.

![FörsäljningDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Denna tomt visar att vissa arenor säljer ett stort antal biljetter på den första försäljningsdagen. Så fort biljetterna börjar säljas på dessa platser, verkar det finnas en galen rush. Denna explosion av aktivitet av några platser kan påverka tjänsten för andra klienter.

Du kan borra i data igen för att se om denna galna rusa är sant för alla evenemang som anordnas av dessa platser. I tidigare tomter såg du att Contoso Concert Hall säljer många biljetter, och att Contoso också har en topp i biljettförsäljningen på vissa dagar. Lek med Power BI-alternativ för att rita kumulativ biljettförsäljning för Contoso Concert Hall, med fokus på försäljningstrender för var och en av dess evenemang. Följer alla evenemang samma försäljningsmönster? Försök att producera en tomt som den nedan.

![ContosoSales (ContosoSales)](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Denna tomt av kumulativ biljettförsäljning över tiden för Contoso Concert Hall för varje händelse visar att den galna rusa inte händer för alla evenemang. Lek med filteralternativen för att utforska försäljningstrender för andra arenor.

Insikterna i biljettförsäljning mönster kan leda Wingtip Biljetter för att optimera sin affärsmodell. I stället för att debitera alla klienter lika, kanske Wingtip bör införa tjänstnivåer med olika beräkningsstorlekar. Större arenor som behöver sälja fler biljetter per dag kan erbjudas en högre nivå med ett högre servicenivåavtal (SLA). Dessa platser kan ha sina databaser placeras i poolen med högre per databas resursgränser. Varje tjänstnivå kan ha en timförsäljningsallokering, med extra avgifter som debiteras för att överskrida allokeringen. Större arenor som har periodiska skurar av försäljning skulle dra nytta av de högre nivåerna, och Wingtip Biljetter kan tjäna pengar på sin tjänst mer effektivt.

Samtidigt klagar vissa Wingtip Tickets kunder på att de kämpar för att sälja tillräckligt med biljetter för att motivera servicekostnaden. Kanske i dessa insikter finns det en möjlighet att öka biljettförsäljningen för underpresterande arenor. Högre försäljning skulle öka det upplevda värdet av tjänsten. Högerklicka fact_Tickets och välj **Nytt mått**. Ange följande uttryck för det nya måttet **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Välj följande visualiseringsalternativ för att rita de procentuella biljetter som säljs av varje plats för att avgöra deras relativa framgång.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

Tomten ovan visar att även om de flesta arenor säljer mer än 80% av sina biljetter, vissa kämpar för att fylla mer än hälften av sina platser. Spela runt med Värden Well för att välja maximal eller minsta procentandel av biljetter som säljs för varje plats.

## <a name="embedding-analytics-in-your-apps"></a>Bädda in analyser i dina appar 
Den här självstudien har fokuserat på analys mellan innehavare som används för att förbättra programvaruleverantörens förståelse av sina klienter. Analytics kan också ge insikter till _hyresgästerna_, för att hjälpa dem att hantera sin verksamhet mer effektivt själva. 

I wingtip biljetter exempel, du tidigare upptäckt att biljettförsäljningen tenderar att följa förutsägbara mönster. Denna insikt kan användas för att underpresterande arenor öka biljettförsäljningen. Kanske finns det en möjlighet att använda maskininlärningstekniker för att förutsäga biljettförsäljning för evenemang. Effekterna av prisförändringar skulle också kunna modelleras, så att effekterna av att erbjuda rabatter kan förutsägas. Power BI Embedded kan integreras i ett händelsehanteringsprogram för att visualisera förväntad försäljning, inklusive effekten av rabatter på de totala antalet sålda platser och intäkter på lågförsäljningshändelser. Med Power BI Embedded kan du även integrera den som faktiskt tillämpar rabatten på biljettpriserna, direkt i visualiseringsupplevelsen.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Distribuera ett SQL Data Warehouse som fylls med ett stjärnschema för klientanalys.
> * Använd Azure Data Factory för att extrahera data från varje klientdatabas till analysdatalagret.
> * Optimera extraherade data (omorganisera till ett stjärnschema).
> * Fråga analysdatalagret. 
> * Använd Power BI för att visualisera trender i data för alla klienter.

Grattis!

## <a name="additional-resources"></a>Ytterligare resurser

- Ytterligare [tutorials som bygger på Wingtip SaaS ansökan](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
