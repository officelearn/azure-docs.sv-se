---
title: 'Azure Data Factory: Vanliga frågor och svar | Microsoft Docs'
description: Få svar på vanliga frågor och svar om Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: d704c32ee7417c6460ad6cc880e451adddfa61de
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59048222"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory vanliga frågor och svar
Den här artikeln innehåller svar på vanliga frågor och svar om Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Vad är Azure Data Factory? 
Data Factory är en fullständigt hanterad, molnbaserad, dataintegrering-tjänst som automatiserar flytt och omvandling av data. Som en fabrik som använder maskiner för att omvandla råmaterial till färdiga produkter, samordnar Azure Data Factory befintliga tjänster som samlar in rådata och omvandla det till färdiga att använda information. 

Med hjälp av Azure Data Factory kan du kan skapa datadrivna arbetsflöden för att flytta data mellan lokala och molnbaserade datalager. Och du kan bearbeta och omvandla data med hjälp av Beräkningstjänster som Azure HDInsight, Azure Data Lake Analytics och SQL Server Integration Services (SSIS) integration runtime. 

Du kan köra din databearbetning på en Azure-baserade cloud Services eller i din egen egenhanterad beräkningsmiljö som SSIS, SQL Server eller Oracle med Data Factory. När du har skapat en pipeline som utför den åtgärd som du behöver kan du schemalägga den så att den körs regelbundet (per timme, varje dag eller varje vecka, till exempel), tidsperioden för schemaläggning eller utlösa pipeline från en händelsen. Mer information finns i [Introduktion till Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Kontrollen flöden och skala 
Som stöd för integreringsflöden och mönster i ett modernt informationslager, aktiverar Data Factory pipeline flexibel data modellering. Detta innebär att fullständig Kontrollflöde programming paradigm, bland annat villkorlig körning, branchning i datapipelines och möjligheten att explicit skicka parametrar inom och mellan dessa flöden. Kontrollflöde omfattar även hur du transformerar data via aktivitetssändning till externa körning motorer och data flow-funktioner, inklusive dataförflyttning i skala, via kopieringsaktiviteten.

Data Factory tillhandahåller friheten att alla flödesstilar som krävs för dataintegrering och som kan skickas på begäran eller regelbundet enligt ett schema. Några vanliga flöden som gör att den här modellen är:   

- Kontrollen flöden:
    - Aktiviteter kan sammanlänkas i en sekvens inom en pipeline.
    - Aktiviteter kan vara förgrenade inom en pipeline.
    - Parametrar:
        - Parametrar kan definieras på pipelinenivå och argument kan skickas när du anropar pipelinen på begäran eller från en utlösare.
        - Aktiviteter kan använda argumenten som skickas till pipelinen.
    - Skicka anpassade tillstånd:
        - Aktivitetsutdata inklusive tillstånd, kan användas av en efterföljande aktivitet i pipelinen.
    - Loopningsbehållare:
        - Foreach-aktiviteten upprepas över en angiven samling av aktiviteter i en loop. 
- Utlösningsbaserade flöden:
    - Pipelines kan utlösas på begäran eller genom wall-clock-tid.
- Deltaflöden:
    - Parametrar kan användas för att definiera ditt övre vattenmärke för deltakopiering vid förflyttning av dimensions- eller referenstabeller från en relationslagringsplats, antingen lokalt eller i molnet, för att läsa in data i sjön. 

Mer information finns i [Självstudie: Kontrollera flöden](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Data omvandlas i stor skala med kodfria pipelines
Den nya webbläsarbaserade verktyg-guiden innehåller kodfria pipeline skapande och distribution med en modern, interaktiv webbaserad upplevelse.

För utvecklare för visuella data och datatekniker är Data Factory-webbgränssnittet kodfria designmiljön som du använder för att skapa pipeliner. Den är helt integrerat med Visual Studio Online Git och ger integrering för CI/CD och iterativ utveckling med startalternativen.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Omfattande plattformsoberoende SDK: er för avancerade användare
Data Factory V2 tillhandahåller en omfattande uppsättning SDK: er som kan användas för att skapa, hantera och övervaka pipelines med hjälp av din favorit-IDE, inklusive:
* Python SDK
* PowerShell CLI
* C#-SDK

Användare kan också använda dokumenterade REST API: er för gränssnittet med Data Factory V2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Iterativ utveckling och felsökning med hjälp av visuella verktyg
Azure Data Factory visuella verktyg aktivera iterativ utveckling och felsökning. Du kan skapa dina pipelines och testa körningar med hjälp av den **felsöka** funktionen i rityta för pipelinen utan att behöva skriva en enda rad kod. Du kan visa resultatet av dina testkörningar i den **utdata** tidsfönster rityta för pipelinen. När du kör testet lyckas, kan du lägga till fler aktiviteter i din pipeline och fortsätta felsökning i en iterativ sätt. Du kan också avbryta din testkörningar när de har pågår. 

Du behöver inte att publicera ändringarna till data factory-tjänsten innan du väljer **felsöka**. Det här är användbart i scenarier där du vill kontrollera att den nya tillägg eller ändringar kommer att fungera som förväntat innan du uppdaterar din data factory-arbetsflöden i miljöer för utveckling, testning eller produktion. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Möjligheten att distribuera SSIS-paket till Azure 
Om du vill flytta SSIS-arbetsbelastningar kan du skapa en Datafabrik och etablera en Azure-SSIS integration runtime. En Azure-SSIS integration runtime är ett fullständigt hanterat kluster av virtuella Azure-datorer (noder) dedikerade för att köra dina SSIS-paket i molnet. Stegvisa instruktioner finns i den [distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md) självstudien. 
 
### <a name="sdks"></a>SDK:er
Om du är en avancerad användare och behöver ett programmeringsgränssnitt har Data Factory tillhandahåller en omfattande uppsättning SDK: er som du kan använda för att skapa, hantera och övervaka pipelines med hjälp av din favorit-IDE. Språk som stöds omfattar .NET, PowerShell, Python och REST.

### <a name="monitoring"></a>Övervakning
Du kan övervaka dina Datafabriker via PowerShell, SDK eller Visual övervakningsverktyg i det webbläsarbaserade användargränssnittet. Du kan övervaka och hantera på begäran, utlösningsbaserade och clock-drivna anpassade flöden på ett effektivt sätt. Avbryt befintliga aktiviteter, se fel på ett ögonblick, granska nedåt för att få detaljerade felmeddelanden och felsöka problem, allt från en enda glasruta utan kontext växlar eller navigera fram och tillbaka mellan skärmar. 

### <a name="new-features-for-ssis-in-data-factory"></a>Nya funktioner för SSIS i Data Factory
Eftersom det första offentligt förhandsversionen 2017, Data Factory har lagt till följande funktioner för SSIS:

-   Stöd för tre flera konfigurationer/varianter av Azure SQL Database som värd för SSIS-databasen (SSISDB) med projekt /-paket:
-   SQL-databas med tjänstslutpunkter i virtuella nätverk
-   Hanterad instans
-   Elastisk pool
-   Stöd för en Azure Resource Manager-nätverk på ett klassiskt virtuellt nätverk till att bli inaktuella i framtiden, där du kan mata in/anslutning till din Azure-SSIS integration runtime till ett virtuellt nätverk som konfigurerats för SQL-databas med virtual network-tjänsten åtkomst till slutpunkter MI/och lokala data. Mer information finns också [ansluta en Azure-SSIS integration runtime till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).
-   Stöd för Azure Active Directory (Azure AD)-autentisering och SQL-autentisering för att ansluta till SSISDB, vilket gör att Azure AD-autentisering med din Data Factory hanterad identitet för Azure-resurser
-   Stöd för att leverera dina egna lokala SQL Server-licens och få betydande kostnadsbesparingar från alternativet Azure Hybrid-förmånen
-   Stöd för Enterprise-versionen av Azure-SSIS integration runtime som låter dig använda avancerade/premium-funktioner, en anpassad installation-gränssnittet för att installera ytterligare komponenter/tillägg och ett partnerekosystem. Mer information finns också [Enterprise Edition, anpassad installation och 3 part utökningsbarhet för SSIS i ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-   Djupare integrering av SSIS i Data Factory som låter dig anropa/utlösare förstklassig köra SSIS-paket aktiviteter i Data Factory-pipeliner och schemalägger dem via SSMS. Mer information finns också [Modernize och utöka dina ETL/ELT-arbetsflöden med SSIS-aktiviteter i ADF pipelines](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Vad är integreringskörningen?
Integration runtime är beräkningsinfrastrukturen som Azure Data Factory använder för att tillhandahålla följande funktioner för dataintegrering i olika nätverksmiljöer:

- **Dataförflyttning**: Vid dataförflyttning flyttar integration runtime data mellan käll- och datalager, även stöd för inbyggda anslutningsprogram, Formatkonvertering, kolumnmappning och bättre och skalbar dataöverföring.
- **Skicka ut aktiviteter**: För transformering har innehåller integration runtime internt körning av SSIS-paket.
- **Köra SSIS-paket**: Integration runtime körs internt SSIS-paket i en hanterad Azure-beräkningsmiljö. Integration runtime har även stöd för att skicka och övervaka transformeringsaktiviteter som körs på en rad olika Beräkningstjänster som Azure HDInsight, Azure Machine Learning, SQL Database och SQL Server.

Du kan distribuera en eller flera instanser av integration runtime som krävs för att flytta och omvandla data. Integration runtime kan köras på en Azures offentliga nätverk eller i ett privat nätverk (lokalt, Azure Virtual Network eller Amazon Web Services virtuellt privat moln [VPC]). 

Mer information finns i [Integration Runtime i Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Vad är gränsen för antalet integreringskörningar?
Det finns ingen hård gräns för antalet integration runtime-instanser som du kan ha i en data factory. Det finns dock en gräns för antalet VM-kärnor som integration runtime kan använda per prenumeration för körning av SSIS-paket. Mer information finns i [Data Factory begränsar](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Vad är toppnivåbegrepp i Azure Data Factory?
En Azure-prenumeration kan ha en eller flera Azure Data Factory-instanser (eller datafabriker). Azure Data Factory innehåller fyra nyckelkomponenter som samverkar som en plattform där du kan skapa datadrivna arbetsflöden med steg för att flytta och omvandla data.

### <a name="pipelines"></a>Pipelines
En datafabrik kan ha en eller flera pipelines. En pipeline är en logisk gruppering av aktiviteter för att utföra en arbetsprocess. Aktiviteterna i en pipeline utför en uppgift tillsammans. En pipeline kan till exempel innehålla en grupp med aktiviteter som matar in data från en Azure-blob och sedan köra en Hive-fråga på ett HDInsight-kluster för att partitionera data. Fördelen är att du kan använda en pipeline för att hantera aktiviteterna som en uppsättning i stället för att hantera varje aktivitet enskilt. Du kan länka ihop aktiviteter i en pipeline kan köras sekventiellt dem, eller du kan använda dem oberoende av varandra, parallellt.

### <a name="activities"></a>Aktiviteter
Aktiviteter representerar ett bearbetningssteg i en pipeline. Du kan till exempel använda en Kopieringsaktivitet för att kopiera data från ett datalager till ett annat datalager. På samma sätt kan du använda en Hive-aktivitet som kör en Hive-fråga på ett Azure HDInsight-kluster för att transformera eller analysera dina data. Data Factory stöder tre typer av aktiviteter: dataförflyttning, datatransformering och kontroll.

### <a name="datasets"></a>Datauppsättningar
Datauppsättningar representerar datastrukturer i datalager som pekar på eller refererar till de data som du vill använda i dina aktiviteter som indata eller utdata. 

### <a name="linked-services"></a>Länkade tjänster
Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Tänk på det sätt: Datamängden representerar strukturen för data och länkade tjänsten definierar anslutningen till datakällan. Till exempel anger en länkad Azure Storage-tjänsten anslutningssträngen för att ansluta till Azure Storage-kontot. Och en Azure blob-datauppsättning anger vilken blobbehållare och mapp som innehåller data.

Länkade tjänster har två syften i Data Factory:

- Att representera en *datalager* som omfattar men är inte begränsat till, en lokal SQL Server-instans, en Oracle database-instans, en filresurs eller ett Azure Blob storage-konto. En lista över datalager som stöds finns i [Kopieringsaktivitet i Azure Data Factory](copy-activity-overview.md).
- Så här visar du en *beräkningsresurs* som kan vara värd för körningen av en aktivitet. Till exempel körs HDInsight Hive-aktiviteten på ett HDInsight Hadoop-kluster. En lista över transformeringsaktiviteter och beräkningsmiljöer som stöds finns i [Transformera data i Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Utlösare
Utlösare representerar enheter av bearbetning som avgör när en pipeline-åtgärd har startats. Det finns olika typer av utlösare för olika typer av händelser. 

### <a name="pipeline-runs"></a>Pipelinekörningar
En pipelinekörning är en instans av en pipelinekörning. Du vanligtvis skapa en instans av en pipeline som körs genom att skicka argument till parametrar som definierats i pipelinen. Du kan skicka argumenten manuellt eller i en utlösardefinition.

### <a name="parameters"></a>Parametrar
Parametrar är nyckel / värde-par i en skrivskyddad konfiguration. Du kan definiera parametrar i en pipeline och argumenten för de definierade parametrarna skickas vid körning från en körningskontexten. Körningskontexten skapas av en utlösare eller från en pipeline som du kör manuellt. Aktiviteter i pipelinen använder parametervärdena.

En datauppsättning är en starkt typifierad parameter och en entitet som du kan återanvända eller referera till. En aktivitet kan referera till datauppsättningar och den kan använda egenskaperna som definierats i definitionen för datauppsättningen.

En länkad tjänst är också en starkt typifierad parameter som innehåller anslutningsinformationen till antingen ett datalager eller en beräkningsmiljö. Det är också en entitet som du kan återanvända eller referera till.

### <a name="control-flows"></a>Kontrollen flöden
Kontrollen flöden dirigera pipelineaktiviteter som innehåller kedjesammansättning av aktiviteter i en sekvens, branchning, parametrar som du anger på pipelinenivå och argument som du skickar du anropa pipeline på begäran eller från en utlösare. Kontrollen flöden inkluderar även skicka anpassade tillstånd och loopbehållare (det vill säga foreach-Iteratorer).


Mer information om Data Factory-begrepp finns i följande artiklar:

- [Datamängder och länkade tjänster](concepts-datasets-linked-services.md)
- [Pipelines och aktiviteter](concepts-pipelines-activities.md)
- [Integration runtime](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Vad är prismodellen för Data Factory?
Azure Data Factory prisinformation, se [Data Factory prisinformation](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Hur kan jag hålla dig uppdaterad med information om Data Factory?
Den senaste informationen om Azure Data Factory finns på följande webbplatser:

- [Blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Startsida för dokumentation](/azure/data-factory)
- [Startsida för produkten](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Teknisk djupdykning 

### <a name="how-can-i-schedule-a-pipeline"></a>Hur kan jag schemalägga en pipeline? 
Du kan använda scheduler-utlösare eller utlösare för tid fönster för att schemalägga en pipeline. Utlösaren använder en kalender tidsschema, som kan schemalägga pipelines med jämna mellanrum eller i kalender-baserade återkommande mönster till exempel på (måndagar kl 6:00) och torsdag kl. 9:00. Mer information finns i [pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Kan jag skicka parametrar till en pipeline-körning?
Ja, parametrar är ett förstklassigt, översta koncept i Data Factory. Du kan definiera parametrar på pipelinenivå och skicka argument när du kör den pipeline som körs på begäran eller genom att använda en utlösare.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Kan jag ange standardvärden för pipeline-parametrar? 
Ja. Du kan definiera standardvärden för parametrar i pipelines. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>En aktivitet i en pipeline kan använda argumenten som skickas till en pipelinekörning? 
Ja. Varje aktivitet i pipeline kan använda det parametervärde som har skickats till pipelinen och fungerar med den `@parameter` konstruera. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Kan en aktivitetsegenskap utdata användas i en annan aktivitet? 
Ja. Aktivitetsutdata kan användas i en efterföljande aktivitet med den `@activity` konstruera.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Hur ska jag ett smidigt sätt hantera null-värden i en aktivitetsutdata? 
Du kan använda den `@coalesce` konstruera i uttryck att hantera null-värden utan problem. 

## <a name="mapping-data-flows"></a>Mappningen dataflöden

### <a name="which-data-factory-version-do-i-use-to-create-data-flows"></a>Vilken Data Factory-version ska jag använda för att skapa dataflöden?
Använd Data Factory V2-version för att skapa dataflöden.
  
### <a name="i-was-a-previous-private-preview-customer-who-used-data-flows-and-i-used-the-data-factory-v2-preview-version-for-data-flows"></a>Jag har en tidigare av privata förhandsversionskund som har använt dataflöden och jag har använt förhandsversionen Data Factory V2 för dataflöden.
Den här versionen är nu föråldrad. Använd Data Factory V2 för dataflöden.
  
### <a name="what-has-changed-from-private-preview-to-limited-public-preview-in-regard-to-data-flows"></a>Vad har ändrats från privat förhandsgranskning för den begränsade offentliga förhandsversionen gäller dataflöden?
Du kommer inte längre behöva ta med din egen Azure Databricks-kluster. Data Factory att hantera klustret har skapats och tear nedåt. BLOB-datauppsättningar och Azure Data Lake Storage Gen2 datauppsättningar är indelade i avgränsad text och Apache Parquet-datauppsättningar. Du kan fortfarande använda Data Lake Storage Gen2 och Blob storage för att lagra filerna. Använd lämplig länkad tjänst för de motorerna för lagring.

### <a name="can-i-migrate-my-private-preview-factories-to-data-factory-v2"></a>Kan jag migrera min privat förhandsgranskning fabriker till Data Factory V2?

Ja. [Följ instruktionerna](https://www.slideshare.net/kromerm/adf-mapping-data-flow-private-preview-migration).

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Jag behöver hjälp med att felsöka min logik för flödet av data. Vilken information behöver jag för att få hjälp?

När Microsoft tillhandahåller hjälp eller felsökning med dataflöden, ange DSL kod planen. Det gör du genom att följa dessa steg:

1. Data flödar Designer väljer **kod** i det övre högra hörnet. Då visas redigerbar JSON-koden för dataflödet.
2. Välj kodvyn **planera** i det övre högra hörnet. Den här växlingsknappen växlar från JSON till skrivskyddad formaterade DSL skriptet planen.
3. Kopiera och klistra in det här skriptet eller spara den i en textfil.

### <a name="how-do-i-access-data-by-using-the-other-80-dataset-types-in-data-factory"></a>Hur kommer jag åt data med hjälp av andra 80 datauppsättningstyper i Data Factory?

Funktionen för mappning av dataflöde kan för närvarande Azure SQL Database, Azure SQL Data Warehouse avgränsade textfiler från Azure Blob storage eller Azure Data Lake Storage Gen2 och Parquet-filer från Blob storage eller Data Lake Storage Gen2 internt för källa och mottagare. 

Använda kopieringsaktiviteten för att organisera data från någon av de andra anslutningsapparna och sedan köra aktiviteten dataflöde för att omvandla data när det mellanlagras. Till exempel din pipeline kopieras först till Blob-lagring och sedan aktiviteten dataflöde använder en datauppsättning i källan för att transformera data.

## <a name="next-steps"></a>Nästa steg
Stegvisa instruktioner för att skapa en datafabrik, finns i följande Självstudier:

- [Snabbstart: Skapa en datafabrik](quickstart-create-data-factory-dot-net.md)
- [Självstudier: Kopieringsdata i molnet](tutorial-copy-data-dot-net.md)
