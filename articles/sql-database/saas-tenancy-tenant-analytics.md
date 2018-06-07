---
title: Kör mellan klient analytics med hjälp av data som extraheras | Microsoft Docs
description: Mellan klient analytics-frågor med data som hämtats från flera databaser i Azure SQL Database.
keywords: sql database tutorial
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: anjangsh; billgib; genemi
ms.openlocfilehash: 68057a2ae5925aa16288844759a34592aa7c7573
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644968"
---
# <a name="cross-tenant-analytics-using-extracted-data"></a>Mellan klient analytics med hjälp av data som hämtas

I den här kursen går igenom ett scenario med fullständig analytics. Scenariot visar hur analytics kan aktivera företag att fatta smarta beslut. Med data som hämtats från databasen för varje klient kan använda du analytics och få insikter om klient beteende och program. Det här scenariot omfattar tre steg: 

1.  **Extrahera** data från varje klient-databasen och **belastningen** till en analytics-arkivet.
2.  **Transformera data som extraheras** för analytics-bearbetning.
3.  Använd **business intelligence** verktyg för att rita ut användbar information som hjälper beslutsfattande. 

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> - Skapa analytics butik om du vill extrahera data till klienten.
> - Använd elastiska jobb för att extrahera data från varje klient-databas till arkivet analytics.
> - Optimera extraherade data (reorganize till en stjärna schemat).
> - Fråga analytics-databas.
> - Använd Power BI för visualisering av data och markera trender i klientdata och ge rekommendation för förbättringar.

![architectureOverView](media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Offline klient analytics mönster

Flera innehavare SaaS-program har vanligtvis mängder med klientdata som lagras i molnet. Dessa data innehåller en omfattande källa för insikter om åtgärden och användningen av ditt program och beteendet för dina klienter. Dessa insights hjälper funktionen utveckling, användbarhet förbättringar och andra investeringar i appen och plattform.

Det är enkelt att komma åt data för alla klienter när alla data i en databas för flera innehavare. Men åtkomst är mer komplexa distribuerade i större skala över tusentals databaser. Ett sätt att Undvik komplexitet och för att minimera effekten av analytics frågor om transaktionsdata är att extrahera data till ett syfte som utformats för analytics-databasen eller data warehouse.

Den här kursen behandlas en fullständig analytics scenario för Wingtip biljetter SaaS-program. Första, *elastiska jobb* används för att extrahera data från varje klient-databas och läsa in det i mellanlagringstabellerna i en butik analytics. Arkivet analytics kan antingen vara en SQL-databas eller ett SQL Data Warehouse. För stora data extrahering [Azure Data Factory](../data-factory/introduction.md) rekommenderas.

Därefter sammanställda data omvandlas till en uppsättning [star-schema](https://www.wikipedia.org/wiki/Star_schema) tabeller. Tabellerna består av en central faktatabell plus relaterade dimensionstabeller.  För Wingtip biljetter:

- Central faktatabell i stjärnan-schemat innehåller biljett data.
- Dimensionstabellerna beskriver handelsplatser, händelser, kunder och köpa datum.

Central fakta- och dimensionstabeller tabellerna aktivera tillsammans effektivt analytisk bearbetning. Star-schema som används i den här självstudiekursen visas i följande bild:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/StarSchema.png)

Slutligen arkivet analytics efterfrågas med **PowerBI** om du vill markera insikter om klient beteende och deras användning av programmet Wingtip biljetter. Du kör frågor som:
 
- Visa relativa popularitet för varje plats
- Markera mönster i biljett sales för olika händelser
- Visa relativa genomförandet av olika handelsplatser säljer ut sina händelse

Förstå hur varje klient använder tjänsten används för att utforska alternativ för monetizing tjänsten och förbättra tjänsten för att klienter ska lyckas bättre. Den här kursen ger grundläggande exempel på typerna av insikter som kan vara uppnår från innehavaren data.

## <a name="setup"></a>Konfiguration

### <a name="prerequisites"></a>Förutsättningar

Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

- Wingtip biljetter SaaS databas Per klient programmet distribueras. För att distribuera på mindre än fem minuter finns [distribuera och utforska Wingtip SaaS-program](saas-dbpertenant-get-started-deploy.md)
- Wingtip biljetter SaaS databas Per klient skript och programmet [källkod](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) laddas ned från GitHub. Se Hämta instruktioner. Se till att *avblockera zip-filen* innan du extraherar innehållet. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera Wingtip biljetter SaaS-skript.
- Power BI Desktop har installerats. [Hämta Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- I gruppen med ytterligare klienter har etablerats, finns det [ **etablera hyresgäster kursen**](saas-dbpertenant-provision-and-catalog.md).
- Ett jobb konto och databas för jobbet har skapats. Se anvisningarna i den [ **schemat management kursen**](saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Skapa data för demonstrationen

I den här självstudiekursen utförs analys på försäljningsdata för biljett. I det aktuella steget generera biljett data för alla klienter.  Dessa data extraheras senare för analys. *Se till att du har etablerat i gruppen med klienter som beskrivs ovan, så att du har en beskrivande mängd data*. En tillräckligt stor mängd data kan exponera en mängd olika biljett köp mönster.

1. I PowerShell ISE öppnar *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*, och anger följande värde:
    - **$DemoScenario** = **1** köpa biljetter för händelser på alla handelsplatser
2. Tryck på **F5** att köra skriptet och skapa biljett köp historik för varje händelse i varje plats.  Skriptet körs under flera minuter att generera tiotusentals biljetter.

### <a name="deploy-the-analytics-store"></a>Distribuera arkivet analytics
Det är ofta många transaktionella databaser som tillsammans innehåller alla klientdata. Du måste samla klientdata från många transaktionella databaser till en analytics arkivet. Aggregeringen kan effektivt fråga data. I den här kursen är en Azure SQL Database-databas används för att lagra sammanställda data.

I följande steg ska du distribuera analytics store, som kallas **tenantanalytics**. Du kan också distribuera fördefinierade tabeller som fylls senare under kursen:
1. I PowerShell ISE öppnar *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Ställ in variabeln $DemoScenario i skript för att matcha valet av analytics store:
    - Om du vill använda SQL-databas utan kolumnen store **$DemoScenario** = **2**
    - Om du vill använda SQL-databas med kolumnen store **$DemoScenario** = **3**  
3. Tryck på **F5** att köra skriptet demo (som anropar den *distribuera TenantAnalytics<XX>.ps1* skript) som skapar klient analytics store. 

Nu när du har distribuerat programmet och fylls med intressanta klientdata, använda [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) att ansluta **tenants1-dpt -&lt;användare&gt;**  och **katalog-dpt -&lt;användare&gt;**  servrar med inloggningen = *developer*, lösenord = *P@ssword1*. Finns det [inledande kursen](saas-dbpertenant-wingtip-app-overview.md) för mer hjälp.

![architectureOverView](media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

I Object Explorer, utför du följande steg:

1. Expandera den *tenants1-dpt -&lt;användare&gt;*  server.
2. Expandera noden databaser och se en lista över databaser som klient.
3. Expandera den *katalog-dpt -&lt;användare&gt;*  server.
4. Kontrollera att du ser analytics arkivet och jobaccount-databasen.

Se följande databasen i SSMS Object Explorer genom att expandera noden analytics store:

- Tabeller **TicketsRawData** och **EventsRawData** håller extraherade rådata från klient-databaser.
- Tabellerna star-schema är **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, och **dim_Dates** .
- Den lagrade proceduren används för att fylla i tabellerna star-schema från rådata tabeller.

![architectureOverView](media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Extrahering av data 

### <a name="create-target-groups"></a>Skapa målgrupper 

Innan du fortsätter bör du kontrollera att du har distribuerat den konto och jobaccount databasen. I nästa uppsättning steg används elastiska jobb att extrahera data från varje klient-databasen och lagra data i arkivet analytics. Sedan det andra jobbet shreds data och lagrar den i tabeller i stjärnan-schemat. Dessa två jobb körs mot två olika målgrupper nämligen **TenantGroup** och **AnalyticsGroup**. Extrahera projektet körs mot TenantGroup som innehåller alla klient-databaser. Förstöring jobbet körs mot AnalyticsGroup som innehåller bara arkivet analytics. Skapa målgrupper med hjälp av följande steg:

1. I SSMS, ansluta till den **jobaccount** databas i katalogen-dpt -&lt;användaren&gt;.
2. Öppna i SSMS, *...\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql* 
3. Ändra den @User variabeln överst i skriptet ersätter <User> med användaren värdet används när du har distribuerat Wingtip SaaS-appen.
4. Tryck på **F5** att köra skriptet som skapar två målgrupper.

### <a name="extract-raw-data-from-all-tenants"></a>Extrahera rådata från alla klienter

Omfattande dataändringar uppstå oftare för *biljetter och kunden* data än för *händelse och platsen* data. Bör du extraherar biljetter och kunden data separat och oftare än du extrahera händelse och platsen data. I det här avsnittet definierar och två separata schemalägga:

- Extrahera biljetter och kundens data.
- Extrahera data om händelser och plats.

Varje jobb extraherar data och publicerar till arkivet analytics. Ett separat jobb shreds det extraherade data till analytics star-schemat.

1. I SSMS, ansluta till den **jobaccount** databas i katalogen-dpt -&lt;användaren&gt; server.
2. Öppna i SSMS, *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Ändra @User överst i skript och Ersätt <User> med det användarnamn som används när du har distribuerat Wingtip SaaS-appen 
4. Tryck på F5 för att köra skriptet som skapar och kör jobbet som hämtar biljetter och kunder data från varje klient-databas. Jobbet att spara data i arkivet för analys.
5. Fråga tabellen TicketsRawData i tenantanalytics-databasen så att tabellen fylls med biljetter information från alla klienter.

![ticketExtracts](media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Upprepa föregående steg, förutom den här gången Ersätt **\ExtractTickets.sql** med **\ExtractVenuesEvents.sql** i steg 2.

Körs jobbet fyller tabellen EventsRawData i arkivet analytics nya händelser och handelsplatser information från alla klienter. 

## <a name="data-reorganization"></a>Data om

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Hjälp extraherade data fylls i stjärnan schemat tabeller

Nästa steg är att bevisa extraherade rådata till en uppsättning tabeller som är optimerade för analytics-frågor. En stjärna-schema används. En central faktatabell innehåller enskilda biljett försäljning poster. Andra tabeller fylls i med relaterade data om handelsplatser, händelser och kunder. Och det finns tid dimensionstabeller. 

I det här avsnittet av kursen definierar och kör ett jobb som sammanfogas extraherade rådata med data i tabellerna star-schema. När merge-jobbet är klart rådata tas bort, lämnar tabellerna som är redo att fyllas med nästa klientdata extrahera jobb.

1. I SSMS, ansluta till den **jobaccount** databas i katalogen-dpt -&lt;användaren&gt;.
2. Öppna i SSMS, *...\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Tryck på **F5** att köra skriptet för att definiera ett jobb som anropar sp_ShredRawExtractedData lagrad procedur i arkivet analytics.
4. Tillåt tillräckligt med tid för att jobbet ska köras.
    - Kontrollera den **livscykel** kolumn jobs.jobs_execution tabell för status för jobbet. Se till att jobbet **lyckades** innan du fortsätter. En lyckad körning visar data som liknar följande diagram:

![fragmentering](media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Datagranskning

### <a name="visualize-tenant-data"></a>Visualisera klientdata

Data i tabellen star schema innehåller alla biljett säljdata behövs för din analys. Om du vill göra det lättare att se trender i stora datamängder, som du behöver visualisera den grafiskt.  I det här avsnittet får du lära dig hur du använder **Power BI** att ändra och visualisera klientdata du har extraherat och ordnas.

Använd följande steg för att ansluta till Power BI och för att importera de vyer som du skapade tidigare:

1. Starta Power BI desktop.
2. Välj Home-menyfliksområdet **hämta Data**, och välj **mer...** från menyn.
3. I den **hämta Data** fönstret Välj Azure SQL Database.
4. Ange namnet på servern i fönstret databas inloggning (katalog-dpt -&lt;användaren&gt;. database.windows.net). Välj **importera** för **Data anslutningsläget**, och klicka sedan på OK. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Välj **databasen** sedan ange användarnamn i det vänstra fönstret = *developer*, och ange lösenord = *P@ssword1*. Klicka på **Anslut**.  

    ![databasesignin](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. I den **Navigator** rutan under analytics-databasen, Välj tabellerna som star-schema: fact_Tickets dim_Events, dim_Venues, dim_Customers och dim_Dates. Välj sedan **belastningen**. 

Grattis! Data har lästs in Power BI. Nu kan du börja utforska intressanta visualiseringar för att få insikter om dina klienter. Därefter går igenom hur analytics gör att du kan ge datadrivna rekommendationer för Wingtip biljetter business-teamet. Rekommendationerna som hjälper dig för att optimera business modell och customer experience.

Börja med att analysera biljett försäljningsdata för att se variationen i användning över handelsplatser. Välj följande alternativ i Power BI för att rita ett stapeldiagram av det totala antalet biljetter säljs av varje plats. På grund av slumpmässig variation i biljett generator kan resultaten vara olika.
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

Föregående ritytans bekräftar att varierar antalet biljetter säljs av varje plats. Handelsplatser som säljer mer biljetter använder tjänsten tyngre än handelsplatser som säljer färre biljetter. Det kan finnas en möjlighet att skräddarsy resursallokering efter behov för olika innehavare.

Du kan ytterligare analysera data för att se hur Biljettförsäljning variera över tid. Välj följande alternativ i Power BI för att rita det totala antalet biljetter säljs varje dag i 60 dagar.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

Föregående diagram visar att biljetten försäljning topp för vissa handelsplatser. Dessa toppar förstärka idé att vissa handelsplatser kan förbrukar systemresurser oproportionerligt. Hittills är inget uppenbara mönster när toppar inträffar.

Därefter vill undersöka betydelsen av dessa försäljning dagar med hög belastning. När dessa toppar, sker när biljetter går du vidare försäljning? Välj följande alternativ i Power BI om du vill rita biljetter säljs per dag.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

Föregående ritytans visar att vissa handelsplatser säljer mycket biljetter på den första dagen i försäljning. Så snart biljetter går du vidare försäljning på dessa handelsplatser, verkar vara en får går lite för snabbt. Den här burst av aktivitet efter några handelsplatser kan det påverka tjänst för andra klienter.

Detaljer om data igen för att se om det här får går lite för snabbt gäller för alla händelser som dessa handelsplatser värd. I tidigare områden sett att Contoso samklang Hall säljer mycket biljetter och att Contoso även har en topp i biljett sales på vissa dagar. Experimentera med Power BI-alternativ för att rita kumulativa Biljettförsäljning för Contoso samklang Hall, fokuserar på försäljning trender för var och en händelse. Följ samma mönster för försäljning i alla händelser?

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Föregående området för Contoso samklang Hall visar att den får går lite för snabbt inte sker för alla händelser. Experimentera med filteralternativ att se trender försäljning för mera.

Insikter om biljett sälja mönster leda Wingtip biljetter för att optimera sin affärsmodell. I stället för att ladda alla klienter lika bör kanske Wingtip installera tjänstnivåer med olika prestandanivåer. Större handelsplatser som behöver sälja mer biljetter per dag skulle kunna erbjudas en högre nivå med ett högre servicenivåavtal (SLA). De handelsplatser kan ha sina databaser som placerats i pool med högre gränserna för per databas. Varje tjänstnivå kan ha en timvis allokering, med ytterligare avgifter debiteras för tilldelning. Större handelsplatser som har periodiska belastning av försäljningen skulle dra nytta av de högre nivåerna och Wingtip biljetter kan tjäna pengar på sina service mer effektivt.

Vissa kunder Wingtip biljetter klagar under tiden de behöva kämpa sälja tillräckligt med biljetter om du vill justera kostnaden för tjänsten. Kanske i dessa insikter har möjlighet att öka försäljningen biljett för presterar handelsplatser som förväntat. Högre försäljning ökar upplevd värdet för tjänsten. Högerklicka på fact_Tickets och välj **nytt mått**. Ange följande uttryck för det nya måttet som kallas **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Välj följande alternativ för visualisering att rita procentandel biljetterna säljs av varje plats för att fastställa deras relativa resultat.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

Föregående ritytans visar att även om de flesta handelsplatser sälja mer än 80% för biljetten, vissa kämpar fylla mer än hälften platser. Experimentera med den värden bra att välja högsta eller lägsta procentandelen biljetter säljs för varje plats.

Tidigare deepened du analyser för att identifiera att Biljettförsäljning tenderar att följa förutsägbar mönster. Den här identifieringen kan Wingtip biljetter hjälp presterar som förväntat handelsplatser öka biljett försäljningen av rekommenderar dynamiska priser. Den här discover kan avslöja möjlighet att använda machine learning-tekniker att förutsäga Biljettförsäljning för varje händelse. Förutsägelser kan även göras för påverkan på intäkter av erbjudandet rabatt på Biljettförsäljning. Power BI Embedded kan integreras i ett program för hantering av händelsen. Integrationen kan hjälpa till att visualisera förväntade försäljning och effekten av olika rabatter. Programmet kan hjälpa dig att utforma en optimal rabatt som ska användas direkt från analytics visningen.

Du har sett trender i klientdata från WingTip programmet. Du kan överväga andra sätt appen kan informera affärsbeslut för SaaS-programleverantörer. Leverantörer kan bättre tillgodose behoven för sina klienter. Den här självstudiekursen har förhoppningsvis utrustade du med verktyg som behövs för att utföra analyser på klientdata att möta ditt företag att fatta beslut om datadrivna.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> - Distribuera en klient analytics-databas med fördefinierade stjärnschema tabeller
> - Används för elastiska jobb för att extrahera data från alla klient-databas
> - Sammanfoga data som hämtas till tabeller i en stjärna-schema som utformats för analys
> - Fråga en analytics-databas 
> - Använd Power BI för visualisering av data för att se trender i klientdata 

Grattis!

## <a name="additional-resources"></a>Ytterligare resurser

- Ytterligare [självstudier som bygger på Wingtip SaaS-program](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [Elastiska jobb](sql-database-elastic-jobs-overview.md).
