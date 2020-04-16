---
title: 'Azure Data Factory: Vanliga frågor och svar '
description: Få svar på vanliga frågor om Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 8d0b49b73ef6b67653fbf32db1174880a51d432d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81412948"
---
# <a name="azure-data-factory-faq"></a>Vanliga frågor och svar om Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln innehåller svar på vanliga frågor om Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Vad är Azure Data Factory? 
Data Factory är en fullständigt hanterad, molnbaserad ETL-tjänst för dataintegration som automatiserar förflyttning och omvandling av data. Precis som en fabrik som kör utrustning för att omvandla råmaterial till färdiga varor dirigerar Azure Data Factory befintliga tjänster som samlar in rådata och omvandlar den till färdig användningsinformation. 

Genom att använda Azure Data Factory kan du skapa datadrivna arbetsflöden för att flytta data mellan lokala datalager och molndatalager. Och du kan bearbeta och omvandla data med dataflöden. ADF stöder också externa beräkningsmotorer för handkodade omvandlingar med hjälp av beräkningstjänster som Azure HDInsight, Azure Databricks och SSIS-integreringskörningen (SQL Server Integration Services). 

Med Data Factory kan du köra databearbetningen antingen på en Azure-baserad molntjänst eller i din egen självvärderade beräkningsmiljö, till exempel SSIS, SQL Server eller Oracle. När du har skapat en pipeline som utför den åtgärd du behöver kan du schemalägga den så att den körs med jämna mellanrum (till exempel varje timme, varje dag eller varje vecka), tidsfönsterplanering eller utlösa pipelinen från en händelseförekomst. Mer information finns i [Introduktion till Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Kontrollera flöden och skala 
För att stödja de olika integrationsflödena och mönstren i det moderna datalagret möjliggör Data Factory flexibel datapipelinemodellering. Detta innebär full kontroll flöde programmering paradigm, som inkluderar villkorlig körning, förgrening i datapipelpipelar, och förmågan att uttryckligen passera parametrar inom och mellan dessa flöden. Kontrollflödet omfattar också omvandling av data genom aktivitetssändning till externa körningsmotorer och dataflödesfunktioner, inklusive dataflyttning i stor skala, via kopieringsaktiviteten.

Data Factory ger frihet att modellera alla flödesformat som krävs för dataintegrering och som kan skickas på begäran eller upprepade gånger enligt ett schema. Några vanliga flöden som den här modellen aktiverar är:   

- Kontrollflöden:
    - Aktiviteter kan kedjas ihop i en sekvens inom en pipeline.
    - Aktiviteter kan förgrenas inom en pipeline.
    - Parametrar:
        - Parametrar kan definieras på pipeline-nivå och argument kan skickas medan du anropar pipelinen på begäran eller från en utlösare.
        - Aktiviteter kan använda argumenten som skickas till pipelinen.
    - Anpassade tillståndspassning:
        - Aktivitetsutdata, inklusive tillstånd, kan förbrukas av en efterföljande aktivitet på pipelinen.
    - Slingbehållare:
        - Förföraktiviteten itererar över en angiven samling aktiviteter i en loop. 
- Trigger-baserade flöden:
    - Pipelines kan utlösas på begäran eller med väggklocka.
- Delta flöden:
    - Parametrar kan användas för att definiera ditt högvattenmärke för deltakopia när du flyttar dimensions- eller referenstabeller från ett relationslager, antingen lokalt eller i molnet, för att läsa in data i sjön. 

Mer information finns i [Självstudiekurs: Kontrollflöden](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Data som omvandlas i stor skala med kodfria rörledningar
Den nya webbläsarbaserade verktygsupplevelsen ger kodfri pipeline-redigering och distribution med en modern, interaktiv webbaserad upplevelse.

För visuella datautvecklare och datatekniker är webbgränssnittet Data Factory den kodfria designmiljö som du använder för att skapa pipelines. Det är helt integrerat med Visual Studio Online Git och ger integration för CI / CD och iterativ utveckling med felsökningsalternativ.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Omfattande SDK:er över flera plattformar för avancerade användare
Data Factory V2 innehåller en omfattande uppsättning SDK:er som kan användas för att skapa, hantera och övervaka pipelines med hjälp av din favorit-IDE, inklusive:
* Python SDK
* PowerShell CLI
* C#-SDK

Användare kan också använda dokumenterade REST API:er för att samverka med Data Factory V2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Iterativa utveckling och felsökning med hjälp av visuella verktyg
Visuella azure data factory-verktyg möjliggör iterativ utveckling och felsökning. Du kan skapa dina pipelines och göra testkörningar med hjälp av **felsökningsfunktionen** på pipeline-arbetsytan utan att skriva en enda kodrad. Du kan visa resultaten av dina testkörningar i **utdatafönstret** på pipeline-arbetsytan. När testkörningen har lyckats kan du lägga till fler aktiviteter i pipelinen och fortsätta felsökning på ett iterativt sätt. Du kan också avbryta dina testkörningar när de pågår. 

Du behöver inte publicera ändringarna i datafabrikstjänsten innan du väljer **Felsökning**. Detta är användbart i scenarier där du vill vara säker på att de nya tilläggen eller ändringarna fungerar som förväntat innan du uppdaterar arbetsflödena för datafabriken under utveckling, test eller produktionsmiljöer. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Möjlighet att distribuera SSIS-paket till Azure 
Om du vill flytta dina SSIS-arbetsbelastningar kan du skapa en Data Factory och etablera en Azure-SSIS-integreringskörning. En Azure-SSIS-integreringskörning är ett fullständigt hanterat kluster av virtuella Azure-datorer (noder) som är dedikerade för att köra dina SSIS-paket i molnet. Stegvisa instruktioner finns i [självstudien Distribuera SSIS till Azure.](tutorial-create-azure-ssis-runtime-portal.md) 
 
### <a name="sdks"></a>SDK:er
Om du är en avancerad användare och letar efter ett programmatiskt gränssnitt tillhandahåller Data Factory en omfattande uppsättning SDK:er som du kan använda för att skapa, hantera eller övervaka pipelines med hjälp av din favorit-IDE. Språkstöd omfattar .NET, PowerShell, Python och REST.

### <a name="monitoring"></a>Övervakning
Du kan övervaka dina datafabriker via PowerShell, SDK eller Visual Monitoring Tools i webbläsarens användargränssnitt. Du kan övervaka och hantera anpassade flöden på begäran, utlösare och klockdrivna anpassade flöden på ett effektivt och ändamålsenligt sätt. Avbryt befintliga uppgifter, se fel på ett ögonblick, öka detaljnivån för att få detaljerade felmeddelanden och felsöka problemen, allt från en enda glasruta utan sammanhangsbyte eller navigering fram och tillbaka mellan skärmarna. 

### <a name="new-features-for-ssis-in-data-factory"></a>Nya funktioner för SSIS i Data Factory
Sedan den första offentliga förhandsversionen 2017 har Data Factory lagt till följande funktioner för SSIS:

-    Stöd för ytterligare tre konfigurationer/varianter av Azure SQL Database som värd för SSIS-databasen (SSISDB) för projekt/paket:
-    SQL-databas med slutpunkter för virtuella nätverkstjänster
-    Hanterad instans
-    Elastisk pool
-    Stöd för ett virtuellt Azure Resource Manager-nätverk ovanpå ett klassiskt virtuellt nätverk som ska inaktuella i framtiden, vilket gör att du kan injicera/ansluta till din Azure-SSIS-integreringskörning till ett virtuellt nätverk som konfigurerats för SQL-databas med slutpunkter för virtuella nätverkstjänster/MI/lokal dataåtkomst. Mer information finns i [Gå med i en Azure-SSIS-integreringskörning till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).
-    Stöd för Azure Active Directory-autentisering (Azure AD) autentisering och SQL-autentisering för att ansluta till SSISDB, vilket möjliggör Azure AD-autentisering med din Data Factory-hanterade identitet för Azure-resurser
-    Stöd för att ta med din egen lokala SQL Server-licens för att få betydande kostnadsbesparingar från Azure Hybrid Benefit-alternativet
-    Stöd för Enterprise Edition av Azure-SSIS-integreringskörningen som gör att du kan använda avancerade/premiumfunktioner, ett anpassat installationsgränssnitt för att installera ytterligare komponenter/tillägg och ett partnerekosystem. Mer information finns i [Även Enterprise Edition, Anpassad installation och utökningsbarhet för tredje part för SSIS i ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-    Djupare integrering av SSIS i Data Factory som låter dig anropa/utlösa förstklassiga kör SSIS-paketaktiviteter i Data Factory-pipelines och schemalägga dem via SSMS. Mer information finns i Även [Modernisera och utöka ETL/ELT-arbetsflöden med SSIS-aktiviteter i ADF-pipelines](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Vad är integrationskörningen?
Integrationskörningen är den beräkningsinfrastruktur som Azure Data Factory använder för att tillhandahålla följande dataintegrationsfunktioner i olika nätverksmiljöer:

- **Dataförflyttning**: För dataförflyttning flyttar integrationskörningen data mellan käll- och måldatalager, samtidigt som stöd för inbyggda kopplingar, formatkonvertering, kolumnmappning och högpresterande och skalbar dataöverföring.
- **Leveransaktiviteter**: För omvandling ger integrationskörningen möjlighet att köra SSIS-paket inbyggt.
- **Kör SSIS-paket:** Integrationskörningen kör inbyggt SSIS-paket i en hanterad Azure-beräkningsmiljö. Integreringskörningen stöder också utskick och övervakning avvandlingsaktiviteter som körs på en mängd olika beräkningstjänster, till exempel Azure HDInsight, Azure Machine Learning, SQL Database och SQL Server.

Du kan distribuera en eller flera instanser av integrationskörningen efter behov för att flytta och omvandla data. Integrationskörningen kan köras i ett offentligt Azure-nätverk eller i ett privat nätverk (lokalt, Azure Virtual Network eller Amazon Web Services virtuellt privat moln [VPC]). 

Mer information finns i [Integration Runtime i Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Vad är gränsen för antalet integrationskörningar?
Det finns ingen hård gräns för antalet integrationskörningsinstanser som du kan ha i en datafabrik. Det finns dock en gräns för antalet VM-kärnor som integrationskörningen kan använda per prenumeration för SSIS-paketkörning. Mer information finns i [Begränsningar för Datafabriken](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Vilka är de vanligaste begreppen i Azure Data Factory?
En Azure-prenumeration kan ha en eller flera Azure Data Factory-instanser (eller datafabriker). Azure Data Factory innehåller fyra nyckelkomponenter som fungerar tillsammans som en plattform där du kan skapa datadrivna arbetsflöden med steg för att flytta och omvandla data.

### <a name="pipelines"></a>Pipelines
En datafabrik kan ha en eller flera pipelines. En pipeline är en logisk gruppering av aktiviteter för att utföra en arbetsenhet. Aktiviteterna i en pipeline utför en uppgift tillsammans. En pipeline kan till exempel innehålla en grupp aktiviteter som förtärde data från en Azure-blob och sedan köra en Hive-fråga på ett HDInsight-kluster för att partitionera data. Fördelen är att du kan använda en pipeline för att hantera aktiviteterna som en uppsättning i stället för att behöva hantera varje aktivitet individuellt. Du kan kedja ihop aktiviteterna i en pipeline för att driva dem sekventiellt, eller så kan du använda dem självständigt, parallellt.

### <a name="data-flows"></a>Dataflöden
Dataflöden är objekt som du skapar visuellt i Data Factory och som omvandlar data i skala på backend Spark-tjänster. Du behöver inte förstå programmering eller Spark interna. Utforma bara dataomvandlingsmetoden med hjälp av diagram (Mappning) eller kalkylblad (Käbbel).

### <a name="activities"></a>Aktiviteter
Aktiviteter representerar ett bearbetningssteg i en pipeline. Du kan till exempel använda en kopieringsaktivitet för att kopiera data från ett datalager till ett annat datalager. På samma sätt kan du använda en Hive-aktivitet som kör en Hive-fråga i ett Azure HDInsight-kluster för att omvandla eller analysera dina data. Data Factory stöder tre typer av aktiviteter: dataförflyttning, datatransformering och kontroll.

### <a name="datasets"></a>Datauppsättningar
Datauppsättningar representerar datastrukturer i datalager som pekar på eller refererar till de data som du vill använda i dina aktiviteter som indata eller utdata. 

### <a name="linked-services"></a>Länkade tjänster
Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Tänk på det så här: En länkad tjänst definierar anslutningen till datakällan och en datauppsättning representerar datastrukturen. En Azure Storage-länkad tjänst anger till exempel anslutningssträngen för att ansluta till Azure Storage-kontot. Och en Azure blob-datauppsättning anger blob-behållaren och mappen som innehåller data.

Länkade tjänster har två syften i Data Factory:

- Om du vill representera ett *datalager* som innehåller, men inte är begränsat till, en lokal SQL Server-instans, en Oracle-databasinstans, en filresurs eller ett Azure Blob-lagringskonto. En lista över datalager som stöds finns [i Kopiera aktivitet i Azure Data Factory](copy-activity-overview.md).
- Så här visar du en *beräkningsresurs* som kan vara värd för körningen av en aktivitet. Till exempel körs HDInsight Hive-aktiviteten på ett HDInsight Hadoop-kluster. En lista över omvandlingsaktiviteter och beräkningsmiljöer som stöds finns [i Omvandla data i Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Utlösare
Utlösare representerar bearbetningsenheter som avgör när en pipeline-körning startas. Det finns olika typer av utlösare för olika typer av händelser. 

### <a name="pipeline-runs"></a>Pipelinekörningar
En pipeline-körning är en instans av en pipeline-körning. Du instansierar vanligtvis en pipeline som körs genom att skicka argument till de parametrar som definieras i pipelinen. Du kan skicka argumenten manuellt eller inom utlösardefinitionen.

### <a name="parameters"></a>Parametrar
Parametrar är nyckel-värde-par i en skrivskyddad konfiguration.Du definierar parametrar i en pipeline och skickar argumenten för de definierade parametrarna under körningen från en körningskontext. Körningskontexten skapas av en utlösare eller från en pipeline som du kör manuellt. Aktiviteter i pipelinen använder parametervärdena.

En datauppsättning är en starkt maskinskriven parameter och en entitet som du kan återanvända eller referera till. En aktivitet kan referera till datauppsättningar och den kan använda de egenskaper som definieras i datauppsättningsdefinitionen.

En länkad tjänst är också en starkt skriven parameter som innehåller anslutningsinformation till antingen ett datalager eller en beräkningsmiljö. Det är också en entitet som du kan återanvända eller referera till.

### <a name="control-flows"></a>Kontrollera flöden
Kontrollflöden dirigerar pipeline-aktiviteter som inkluderar kedjeaktiviteter i en sekvens, förgrening, parametrar som du definierar på pipeline-nivå och argument som du skickar när du anropar pipelinen på begäran eller från en utlösare. Kontrollflöden inkluderar också anpassade tillståndspassnings- och loopningsbehållare (det vill säga förach iteratorer).


Mer information om Data Factory-begrepp finns i följande artiklar:

- [Datamängder och länkade tjänster](concepts-datasets-linked-services.md)
- [Pipelines och aktiviteter](concepts-pipelines-activities.md)
- [Integration runtime](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Vad är prismodellen för Data Factory?
Prisinformation för Azure Data Factory finns i [Information Factory-prisinformation](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Hur kan jag hålla mig uppdaterad med information om Data Factory?
Den senaste informationen om Azure Data Factory finns på följande platser:

- [Blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Startsida för dokumentation](/azure/data-factory)
- [Produktens hemsida](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Tekniska djupdykning 

### <a name="how-can-i-schedule-a-pipeline"></a>Hur schemalägger jag en pipeline? 
Du kan använda schedulerutlösaren eller tidsfönstrets utlösare för att schemalägga en pipeline. Utlösaren använder ett kalenderschema för väggklocka, som kan schemalägga pipelines med jämna mellanrum eller i kalenderbaserade återkommande mönster (till exempel på måndagar klockan 18:00 och torsdagar klockan 21:00). Mer information finns i [pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Kan jag skicka parametrar till en pipeline-körning?
Ja, parametrar är ett förstklassigt toppnivåkoncept i Data Factory. Du kan definiera parametrar på pipeline-nivå och skicka argument när du kör pipeline-körningen på begäran eller med hjälp av en utlösare.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Kan jag definiera standardvärden för pipelineparametrarna? 
Ja. Du kan definiera standardvärden för parametrarna i pipelines. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Kan en aktivitet i en pipeline förbruka argument som skickas till en pipeline-körning? 
Ja. Varje aktivitet i pipelinen kan använda parametervärdet som skickas till `@parameter` pipelinen och köras med konstruktionen. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Kan en aktivitetsutdataegenskap förbrukas i en annan aktivitet? 
Ja. En aktivitetsutdata kan förbrukas `@activity` i en efterföljande aktivitet med konstruktionen.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Hur hanterar jag graciöst null-värden i en aktivitetsutdata? 
Du kan `@coalesce` använda konstruktionen i uttrycken för att hantera null-värden på ett smidigt sätt. 

## <a name="mapping-data-flows"></a>Mappa dataflöden

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Jag behöver hjälp med att felsöka min dataflödeslogik. Vilken information behöver jag ge för att få hjälp?

När Microsoft tillhandahåller hjälp eller felsökning med dataflöden anger du dataflödesskriptet. Det här är kodnamnet bakom från dataflödesdiagrammet. Öppna dataflödet från ADF-användargränssnittet och klicka sedan på knappen "Script" längst upp till höger. Kopiera och klistra in skriptet eller spara det i en textfil.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Hur kommer jag åt data med hjälp av de andra 90 datauppsättningstyperna i Data Factory?

Funktionen för dataflöde för mappningsdata tillåter för närvarande Azure SQL Database, Azure SQL Data Warehouse, avgränsade textfiler från Azure Blob storage eller Azure Data Lake Storage Gen2 och Parquet-filer från Blob-lagring eller Data Lake Storage Gen2 internt för källa och diskho. 

Använd aktiviteten Kopiera för att arrangera data från någon av de andra kopplingarna och kör sedan en dataflödesaktivitet för att omvandla data när de har mellanlagats. Pipelinen kopieras till exempel först till Blob-lagring och sedan använder en dataflödesaktivitet en datauppsättning i källan för att omvandla dessa data.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>Är den självvärderade integrationskörningen tillgänglig för dataflöden?

Självvärdbaserad IR är en ADF-pipelinekonstruktion som du kan använda med kopieringsaktiviteten för att hämta eller flytta data till och från on-prem- eller VM-baserade datakällor och sänkor. Stega data först med en kopia, sedan Dataflöde för omvandling och sedan en efterföljande kopia om du behöver flytta tillbaka de omformade data till arkivet on-prem.

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>Tjänar beräkningsmotorn för dataflödet flera klienter?
Kluster delas aldrig. Vi garanterar isolering för varje jobb som körs i produktionskörningar. I händelse av felsökningsscenario får en person ett kluster, och alla felsökningar går till det kluster som initieras av den användaren.

## <a name="wrangling-data-flows"></a>Käbbel dataflöden

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>Vilka regioner stöds för att käbbel dataflöde?

Dataflödet för käbbel stöds för närvarande i datafabriker som skapats i följande regioner:

* Australien, östra
* Kanada, centrala
* Indien, centrala
* USA, östra
* USA, östra 2
* Japan, östra
* Europa, norra
* Sydostasien
* USA, södra centrala
* Storbritannien, södra
* USA, västra centrala
* Europa, västra
* USA, västra
* USA, västra 2

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>Vilka är begränsningarna och begränsningarna med käbbel dataflöde?

Datauppsättningsnamn kan bara innehålla alfanumeriska tecken. Följande datalager stöds:

* Avgränsadtextdatauppsättning i Azure Blob Storage med autentisering av kontonyckel
* Avgränsadtextdatauppsättning i Azure Data Lake Storage gen2 med hjälp av autentisering av kontonyckel eller tjänsthuvudnamn
* Avgränsadtextdatauppsättning i Azure Data Lake Storage gen1 med hjälp av tjänsthuvudnamnsautentisering
* Azure SQL Database och Data Warehouse med sql-autentisering. Se SQL-typer som stöds nedan. Det finns inget PolyBase- eller mellanlagringsstöd för informationslager.

För närvarande stöds inte integrering av länkade nyckelvalv för länkade nyckelvalv i käbbel av dataflöden.

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>Vad är skillnaden mellan att kartlägga och käbbel dataflöden?

Mappning av dataflöden är ett sätt att omvandla data i stor skala utan att någon kodning krävs. Du kan utforma ett dataomvandlingsjobb i dataflödesarbetsytan genom att skapa en serie omvandlingar. Börja med valfritt antal källomvandlingar följt av dataomvandlingssteg. Slutför ditt dataflöde med en diskbänk för att landa dina resultat i en destination. Att mappa dataflöde är bra på att mappa och omvandla data med både kända och okända scheman i diskhoar och källor.

Med käbbeldataflöden kan du göra agila dataförberedelser och utforskning med hjälp av Power Query Online mashup-redigeraren i stor skala via gnistkörning. Med ökningen av datasjöar ibland behöver du bara utforska en datauppsättning eller skapa en datauppsättning i sjön. Du mappar inte till ett känt mål. Käbbel dataflöden används för mindre formella och modellbaserade analysscenarier.

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>Vad är skillnaden mellan Power Platform-dataflöden och käbbeldataflöden?

Power Platform-dataflöden gör det möjligt för användare att importera och omvandla data från ett brett spektrum av datakällor till Common Data Service och Azure Data Lake för att skapa PowerApps-program, Power BI-rapporter eller Flödesautomationer. Power Platform-dataflöden använder de etablerade Power Query-dataförberedelserna, liknande Power BI och Excel. Power Platform-dataflöden möjliggör också enkel återanvändning inom en organisation och hanterar automatiskt orkestrering (t.ex. automatiskt uppdatera dataflöden som är beroende av ett annat dataflöde när det förstnämnda uppdateras).

Azure Data Factory (ADF) är en hanterad dataintegrationstjänst som gör det möjligt för datatekniker och medborgare dataintegratör att skapa komplexa hybridextrakt-transform-load (ETL) och utvinning-load-transform (ELT) arbetsflöden. Käbbel dataflödet i ADF ger användare en kodfri, serverlös miljö som förenklar dataförberedelser i molnet och skalar till alla datastorlekar utan infrastrukturhantering krävs. Den använder Power Query-dataförberedelseteknik (används även i Power Platform-dataflöden, Excel, Power BI) för att förbereda och forma data. Byggd för att hantera alla komplexiteter och utmaningar skala av stordataintegrering, käbbel dataflöden tillåter användare att snabbt förbereda data i stor skala via gnista utförande. Användare kan bygga elastiska datapipelpipels i en tillgänglig visuell miljö med vårt webbläsarbaserade gränssnitt och låta ADF hantera komplexiteten i Spark-körningen. Skapa scheman för dina pipelines och övervaka dina dataflödeskörningar från ADF-övervakningsportalen. Hantera enkelt datatillgänglighets-SLA:er med ADF:s omfattande tillgänglighetsövervakning och aviseringar och utnyttja inbyggda funktioner för kontinuerlig integrering och distribution för att spara och hantera dina flöden i en hanterad miljö. Upprätta aviseringar och visa körningsplaner för att verifiera att logiken fungerar som planerat när du justerar dina dataflöden.

### <a name="supported-sql-types"></a>SQL-typer som stöds

Käbbel dataflöde stöder följande datatyper i SQL. Du får ett valideringsfel för att använda en datatyp som inte stöds.

* short
* double
* real
* float
* char
* nchar
* varchar
* nvarchar
* heltal
* int
* bit
* boolean
* smallint
* tinyint
* bigint
* long
* text
* date
* datetime
* datetime2
* smalldatetime
* timestamp
* uniqueidentifier
* xml

Andra datatyper kommer att stödjas i framtiden.

## <a name="next-steps"></a>Nästa steg
Steg-för-steg-instruktioner för att skapa en datafabrik finns i följande självstudier:

- [Snabbstart: Skapa en datafabrik](quickstart-create-data-factory-dot-net.md)
- [Självstudiekurs: Kopiera data i molnet](tutorial-copy-data-dot-net.md)
