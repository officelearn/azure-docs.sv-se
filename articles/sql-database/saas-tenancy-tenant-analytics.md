---
title: Analys över flera innehavare med extraherade data
description: Analysfrågor för flera Azure SQL Database-databaser i en enda klientorganisationsapp med flera klientlicenser.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: anjangsh,billgib,genemi
ms.date: 12/18/2018
ms.openlocfilehash: c589d9619da8b5150d0fb4752625571c48393552
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826387"
---
# <a name="cross-tenant-analytics-using-extracted-data---single-tenant-app"></a>Analys över flera innehavare med extraherade data – app för en innehavare
 
I den här självstudien går du igenom ett komplett analysscenario för en enda klientimplementering. Scenariot visar hur analyser kan göra det möjligt för företag att fatta smarta beslut. Med hjälp av data som extraheras från varje klientdatabas använder du analyser för att få insikter om klientbeteende, inklusive deras användning av exempelprogrammet Wingtip Tickets SaaS. Det här scenariot omfattar tre steg: 

1.  **Extrahera** data från varje klientdatabas och **Läs** in i ett analysarkiv.
2.  **Omvandla extraherade data** för analysbearbetning.
3.  Använd **business intelligence-verktyg** för att dra ut användbara insikter, som kan vägleda beslutsfattandet. 

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> - Skapa klientanalysarkivet för att extrahera data till.
> - Använd elastiska jobb för att extrahera data från varje klientdatabas i analysarkivet.
> - Optimera extraherade data (omorganisera till ett stjärnschema).
> - Fråga analysdatabasen.
> - Använd Power BI för datavisualisering för att markera trender i klientdata och ge rekommendation för förbättringar.

![arkitekturÖversikt](media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Analysmönster för offlineklienter

SaaS-program med flera innehavare har vanligtvis en stor mängd klientdata som lagras i molnet. Dessa data ger en rik källa till insikter om hur ditt program fungerar och används och hur dina klienter fungerar. Dessa insikter kan vägleda funktionsutveckling, användbarhetsförbättringar och andra investeringar i appen och plattformen.

Det är enkelt att komma åt data för alla klienter när alla data finns i bara en databas med flera innehavare. Men åtkomsten är mer komplex när den distribueras i stor skala över potentiellt tusentals databaser. Ett sätt att tämja komplexiteten och minimera effekten av analysfrågor på transaktionsdata är att extrahera data till en ändamålsdesignad analysdatabas eller ett datalager.

Den här självstudien presenterar ett komplett analysscenario för Wingtip Tickets SaaS-programmet. Först används *elastiska jobb* för att extrahera data från varje klientdatabas och läsa in dem i mellanlagringstabeller i ett analysarkiv. Analysarkivet kan antingen vara en SQL-databas eller ett SQL Data Warehouse. För storskalig dataextrahering rekommenderas [Azure Data Factory.](../data-factory/introduction.md)

Därefter omvandlas de aggregerade data till en uppsättning [stjärnschematabeller.](https://www.wikipedia.org/wiki/Star_schema) Tabellerna består av en central faktatabell plus relaterade dimensionstabeller.  För Wingtip Biljetter:

- Den centrala faktatabellen i stjärnschemat innehåller biljettdata.
- Dimensionstabellerna beskriver platser, händelser, kunder och inköpsdatum.

Tillsammans möjliggör de centrala fakta- och dimensionstabellerna effektiv analytisk bearbetning. Stjärnschemat som används i den här självstudien visas i följande bild:
 
![arkitekturÖversikt](media/saas-tenancy-tenant-analytics/StarSchema.png)

Slutligen efterfrågas analysarkivet med **PowerBI** för att lyfta fram insikter om klientbeteende och deras användning av Wingtip Tickets-programmet. Du kör frågor som:
 
- Visa den relativa populariteten för varje plats
- Markera mönster i biljettförsäljningen för olika evenemang
- Visa den relativa framgången för olika arenor att sälja ut sitt evenemang

Förstå hur varje klient använder tjänsten används för att utforska alternativ för att tjäna pengar på tjänsten och förbättra tjänsten för att hjälpa hyresgäster att bli mer framgångsrika. Den här självstudien innehåller grundläggande exempel på de typer av insikter som kan hämtas från klientdata.

## <a name="setup"></a>Installation

### <a name="prerequisites"></a>Krav

Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

- Wingtip-biljetterna SaaS-databasen per klientprogram distribueras. Information om hur du distribuerar på mindre än fem minuter finns i [Distribuera och utforska Wingtip SaaS-programmet](saas-dbpertenant-get-started-deploy.md)
- Wingtip-biljetterna SaaS Database Per Tenant-skript och [programkällakod](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) hämtas från GitHub. Se hämtningsinstruktioner. Var noga med att *låsa upp zip-filen* innan du extraherar dess innehåll. Kolla in den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ner och låsa upp Wingtip Tickets SaaS skript.
- Power BI Desktop är installerat. [Ladda ner Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- Batchen med ytterligare klienter har etablerats, se [**självstudien för tillhandahållande klienter**](saas-dbpertenant-provision-and-catalog.md).
- Ett jobbkonto och jobbkontodatabas har skapats. Se lämpliga steg i [**självstudiekursen för schemahantering**](saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Skapa data för demon

I den här självstudien utförs analys på biljettförsäljningsdata. I det aktuella steget genererar du biljettdata för alla klienter.  Senare extraheras dessa data för analys. *Se till att du har etablerat batchen med klienter enligt beskrivningen ovan, så att du har en meningsfull mängd data*. En tillräckligt stor mängd data kan exponera en rad olika biljettinköpsmönster.

1. Öppna *...\Utbildningsmoduler\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*i PowerShell ISE och ange följande värde:
    - **$DemoScenario**1 Köp biljetter till evenemang på alla arenor**1**  = 
2. Tryck på **F5** för att köra skriptet och skapa biljettinköpshistorik för varje evenemang i varje lokal.  Skriptet körs i flera minuter för att generera tiotusentals biljetter.

### <a name="deploy-the-analytics-store"></a>Distribuera analysarkivet
Ofta finns det många transaktionsdatabaser som tillsammans innehåller alla klientdata. Du måste aggregera klientdata från de många transaktionsdatabaserna i ett analysarkiv. Aggregeringen möjliggör effektiv fråga för data. I den här självstudien används en Azure SQL Database-databas för att lagra aggregerade data.

I följande steg distribuerar du analysarkivet, som kallas **tenantanalytics**. Du distribuerar också fördefinierade tabeller som fylls i senare i självstudien:
1. I PowerShell ISE öppnar *du ...\Utbildningsmoduler\Operativa analyser\Klientanalys\Demo-TenantAnalytics.ps1* 
2. Ange $DemoScenario variabel i skriptet så att den matchar ditt val av analysbutik:
    - Om du vill använda SQL-databas utan kolumnarkiv anger du **$DemoScenario** = **2**
    - Om du vill använda SQL-databas med kolumnarkiv anger **du $DemoScenario** = **3**  
3. Tryck på **F5** för att köra demoskriptet (som *anropar XX-skriptet Deploy-TenantAnalytics\<XX>.ps1)* som skapar klientanalysarkivet. 

Nu när du har distribuerat programmet och fyllt den med intressanta klientdata, använd [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) för att ansluta **tenants1-dpt-&lt;Användare&gt; ** och **katalog-dpt-&lt;Användarservrar&gt; ** med Login = *utvecklare*, Lösenord = *P\@ssword1*. Mer vägledning finns i den [inledande självstudien.](saas-dbpertenant-wingtip-app-overview.md)

![arkitekturÖversikt](media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

Gör följande i Objektutforskaren:

1. Expandera *&lt;klienterna1-dpt-&gt; Användarserver.*
2. Expandera noden Databaser och se listan över klientdatabaser.
3. Expandera *katalog-dpt-&lt;&gt; Användarserver.*
4. Kontrollera att du ser analysarkivet och jobbkontodatabasen.

Se följande databasobjekt i SSMS Object Explorer genom att expandera noden för analysarkiv:

- Tabeller **TicketsRawData** och **EventsRawData** innehåller råutextraherade data från klientdatabaserna.
- Stjärnschematabellerna är **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**och **dim_Dates**.
- Den lagrade proceduren används för att fylla i stjärnschematabellerna från rådatatabellerna.

![arkitekturÖversikt](media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Data extrahering 

### <a name="create-target-groups"></a>Skapa målgrupper 

Innan du fortsätter kontrollerar du att du har distribuerat jobbkontot och jobbkontodatabasen. I nästa steg används elastiska jobb för att extrahera data från varje klientdatabas och för att lagra data i analysarkivet. Sedan strimlar det andra jobbet data och lagrar den i tabeller i stjärnschemat. Dessa två jobb körs mot två olika målgrupper, nämligen **TenantGroup** och **AnalyticsGroup**. Extrahera-jobbet körs mot TenantGroup, som innehåller alla klientdatabaser. Strimlingsjobbet körs mot AnalyticsGroup, som bara innehåller analysarkivet. Skapa målgrupperna med hjälp av följande steg:

1. I SSMS ansluter du till **jobaccount-databasen** &lt;i&gt;katalog-dpt- Användare .
2. Öppna *...\Utbildningsmoduler\Operational Analytics\Tenant Analytics\ TargetGroups.sql i* SSMS 
3. Ändra @User variabeln högst upp i `<User>` skriptet och ersätt med det användarvärde som användes när du distribuerade Wingtip SaaS-appen.
4. Tryck på **F5** för att köra skriptet som skapar de två målgrupperna.

### <a name="extract-raw-data-from-all-tenants"></a>Extrahera rådata från alla klienter

Omfattande dataändringar kan inträffa oftare för *biljett- och kunddata* än för *händelse- och platsdata.* Överväg därför att extrahera biljett- och kunddata separat och oftare än du extraherar händelse- och platsdata. I det här avsnittet definierar och schemalägger du två separata jobb:

- Extrahera biljett- och kunddata.
- Extrahera händelse- och platsdata.

Varje jobb extraherar sina data och publicerar dem i analysarkivet. Där strimlar ett separat jobb de extraherade data i analysstjärnschemat.

1. I SSMS ansluter du till **jobaccount-databasen** &lt;i&gt; katalog-dpt- Användarserver.
2. Öppna *...\Utbildningsmoduler\Operational Analytics\Tenant Analytics\ExtractTickets.sql*i SSMS .
3. Ändra @User högst upp i skriptet `<User>` och ersätt med det användarnamn som användes när du distribuerade Wingtip SaaS-appen 
4. Tryck på F5 för att köra skriptet som skapar och kör jobbet som extraherar biljetter och kunder data från varje klientdatabas. Jobbet sparar data i analysarkivet.
5. Fråga tabellen TicketsRawData i databasen tenantanalytics för att säkerställa att tabellen fylls med biljettinformation från alla klienter.

![biljettUttredar](media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Upprepa föregående steg, förutom den här gången ersätt **\ExtractTickets.sql** med **\ExtractVenuesEvents.sql** i steg 2.

Jobbet fyller i tabellen EventsRawData i analysarkivet med nya händelser och platsinformation från alla klienter. 

## <a name="data-reorganization"></a>Omorganisation av data

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Strimla extraherade data för att fylla i stjärnschematabeller

Nästa steg är att strimla extraherade rådata i en uppsättning tabeller som är optimerade för analysfrågor. Ett stjärnschema används. En central faktatabell innehåller individuella biljettförsäljningsposter. Andra tabeller fylls i med relaterade data om platser, händelser och kunder. Och det finns tidsdimensionstabeller. 

I det här avsnittet i självstudien definierar och kör du ett jobb som sammanfogar extraherade rådata med data i stjärnschematabellerna. När kopplingsjobbet är klart tas rådata bort, vilket gör att tabellerna är redo att fyllas i av nästa klientdatautdragsjobb.

1. I SSMS ansluter du till **jobaccount-databasen** &lt;i&gt;katalog-dpt- Användare .
2. Öppna *...\Utbildningsmoduler\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*i SSMS .
3. Tryck på **F5** för att köra skriptet för att definiera ett jobb som anropar sp_ShredRawExtractedData lagrad procedur i analysarkivet.
4. Tillåt tillräckligt med tid för att jobbet ska kunna köras.
    - Kontrollera **tabellen** Livscykelkolumn för jobb.jobs_execution för jobbets status. Se till att jobbet **lyckades** innan du fortsätter. En lyckad körning visar data som liknar följande diagram:

![Fragmentering](media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Datautforskning

### <a name="visualize-tenant-data"></a>Visualisera klientdata

Data i tabellen stjärnschema innehåller alla biljettförsäljningsdata som behövs för din analys. För att göra det lättare att se trender i stora datauppsättningar måste du visualisera den grafiskt.  I det här avsnittet får du lära dig hur du använder **Power BI** för att manipulera och visualisera klientdata som du har extraherat och organiserat.

Använd följande steg för att ansluta till Power BI och importera de vyer som du skapade tidigare:

1. Starta Power BI-skrivbordet.
2. Välj **Hämta data**i menyfliksområdet Hem och välj **Mer...** från menyn.
3. Välj Azure SQL Database i fönstret **Hämta data.**
4. I databasinloggningsfönstret anger du servernamnet&lt;(catalog-dpt- Användare&gt;.database.windows.net). Välj **Importera** för **dataanslutningsläge**och klicka sedan på OK. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Välj **Databas** i den vänstra rutan och ange sedan användarnamn = *utvecklare*och ange lösenord = *P\@ssword1*. Klicka på **Anslut**.  

    ![databastilldela](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. I **fönstret Navigator,** under analysdatabasen, väljer du stjärnschematabellerna: fact_Tickets, dim_Events, dim_Venues, dim_Customers och dim_Dates. Välj sedan **Läs in**. 

Grattis! Du har läst in data i Power BI. Nu kan du börja utforska intressanta visualiseringar för att få insikter i dina hyresgäster. Därefter går du igenom hur analyser kan göra det möjligt för dig att ge datadrivna rekommendationer till Wingtip Tickets affärsteam. Rekommendationerna kan bidra till att optimera affärsmodellen och kundupplevelsen.

Du börjar med att analysera biljettförsäljningsdata för att se variationen i användningen mellan arenorna. Välj följande alternativ i Power BI om du vill rita ett stapeldiagram över det totala antalet biljetter som säljs av varje plats. På grund av slumpmässig variation i biljettgeneratorn kan dina resultat vara annorlunda.
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

Den föregående tomten bekräftar att antalet biljetter som säljs av varje plats varierar. Arenor som säljer fler biljetter använder din tjänst tyngre än arenor som säljer färre biljetter. Det kan finnas en möjlighet här att skräddarsy resursallokering efter olika klientbehov.

Du kan ytterligare analysera data för att se hur biljettförsäljningen varierar över tiden. Välj följande alternativ i Power BI om du vill rita det totala antalet biljetter som sålts varje dag under en period av 60 dagar.
 
![FörsäljningVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

I föregående diagram visas att biljettförsäljningen ökar för vissa platser. Dessa spikar förstärker tanken att vissa arenor kan konsumera systemresurser oproportionerligt. Hittills finns det inget uppenbart mönster i när spikar inträffar.

Nästa du vill ytterligare undersöka betydelsen av dessa topp försäljning dagar. När sker dessa toppar efter att biljetterna börjar säljas? Om du vill rita ut biljetter som säljs per dag väljer du följande alternativ i Power BI.

![FörsäljningDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

Den föregående tomten visar att vissa arenor säljer en hel del biljetter på den första försäljningsdagen. Så fort biljetterna börjar säljas på dessa platser, verkar det finnas en galen rush. Denna explosion av aktivitet av några platser kan påverka tjänsten för andra klienter.

Du kan borra i data igen för att se om denna galna rusa är sant för alla evenemang som anordnas av dessa platser. I tidigare tomter, konstaterade du att Contoso Concert Hall säljer en hel del biljetter, och att Contoso har också en topp i biljettförsäljningen på vissa dagar. Lek med Power BI-alternativ för att rita kumulativ biljettförsäljning för Contoso Concert Hall, med fokus på försäljningstrender för var och en av dess evenemang. Följer alla evenemang samma försäljningsmönster?

![ContosoSales (ContosoSales)](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Den föregående tomten för Contoso Concert Hall visar att den galna rusa inte händer för alla händelser. Lek med filteralternativen för att se försäljningstrender för andra arenor.

Insikterna i biljettförsäljning mönster kan leda Wingtip Biljetter för att optimera sin affärsmodell. I stället för att debitera alla klienter lika, kanske Wingtip bör införa tjänstnivåer med olika beräkningsstorlekar. Större arenor som behöver sälja fler biljetter per dag kan erbjudas en högre nivå med ett högre servicenivåavtal (SLA). Dessa platser kan ha sina databaser placeras i poolen med högre per databas resursgränser. Varje tjänstnivå kan ha en timförsäljningsallokering, med extra avgifter som debiteras för att överskrida allokeringen. Större arenor som har periodiska skurar av försäljning skulle dra nytta av de högre nivåerna, och Wingtip Biljetter kan tjäna pengar på sin tjänst mer effektivt.

Samtidigt klagar vissa Wingtip Tickets kunder på att de kämpar för att sälja tillräckligt med biljetter för att motivera servicekostnaden. Kanske i dessa insikter finns det en möjlighet att öka biljettförsäljningen för underpresterande arenor. Högre försäljning skulle öka det upplevda värdet av tjänsten. Högerklicka fact_Tickets och välj **Nytt mått**. Ange följande uttryck för det nya måttet **AverageTicketsSold**:

```
AverageTicketsSold = AVERAGEX( SUMMARIZE( TableName, TableName[Venue Name] ), CALCULATE( SUM(TableName[Tickets Sold] ) ) )
```

Välj följande visualiseringsalternativ för att rita de procentuella biljetter som säljs av varje plats för att avgöra deras relativa framgång.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

Den föregående tomten visar att även om de flesta arenor säljer mer än 80% av sina biljetter, vissa kämpar för att fylla mer än hälften av platserna. Spela runt med Värden Well för att välja maximal eller minsta procentandel av biljetter som säljs för varje plats.

Tidigare har du fördjupat din analys för att upptäcka att biljettförsäljningen tenderar att följa förutsägbara mönster. Denna upptäckt kan låta Wingtip Biljetter hjälpa underpresterande arenor öka biljettförsäljningen genom att rekommendera dynamisk prissättning. Denna upptäckt kan avslöja en möjlighet att använda maskininlärningstekniker för att förutsäga biljettförsäljning för varje händelse. Förutsägelser kan också göras för effekterna på intäkterna av att erbjuda rabatter på biljettförsäljning. Power BI Embedded kan integreras i ett händelsehanteringsprogram. Integrationen kan hjälpa visualisera förväntad försäljning och effekten av olika rabatter. Programmet kan hjälpa till att utforma en optimal rabatt som ska tillämpas direkt från analysdisplayen.

Du har observerat trender i klientdata från WingTip-programmet. Du kan tänka över andra sätt som appen kan informera affärsbeslut för SaaS-programleverantörer. Leverantörer kan bättre tillgodose behoven hos sina hyresgäster. Förhoppningsvis har den här självstudien utrustat dig med verktyg som krävs för att utföra analyser av klientdata för att göra det möjligt för dina företag att fatta datadrivna beslut.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> - Distribuerade en klientanalysdatabas med fördefinierade stjärnschematabeller
> - Använde elastiska jobb för att extrahera data från alla klientdatabaser
> - Sammanfoga extraherade data i tabeller i ett stjärnschema som utformats för analys
> - Fråga en analysdatabas 
> - Använda Power BI för datavisualisering för att observera trender i klientdata 

Grattis!

## <a name="additional-resources"></a>Ytterligare resurser

- Ytterligare [tutorials som bygger på Wingtip SaaS ansökan](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [Elastiska jobb](elastic-jobs-overview.md).
- [Analys över flera innehavare med extraherade data – app för flera innehavare](saas-multitenantdb-tenant-analytics.md)