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
ms.openlocfilehash: cdd5c7592ebbc092c8e7be01a0fdd16e9c78aeaf
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240804"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory vanliga frågor och svar
Den här artikeln innehåller svar på vanliga frågor och svar om Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Vad är Azure Data Factory? 
Data Factory är en fullständigt hanterad, molnbaserad, dataintegrering-tjänst som automatiserar flytt och omvandling av data. Som en fabrik som använder maskiner för att omvandla råmaterial till färdiga produkter, samordnar Azure Data Factory befintliga tjänster som samlar in rådata och omvandla det till färdiga att använda information. 

Med hjälp av Azure Data Factory kan du kan skapa datadrivna arbetsflöden för att flytta data mellan lokala och molnbaserade datalager. Och du kan bearbeta och omvandla data med hjälp av Beräkningstjänster som Azure HDInsight, Azure Data Lake Analytics och SQL Server Integration Services (SSIS) integration runtime. 

Du kan köra din databearbetning på en Azure-baserade cloud Services eller i din egen egenhanterad beräkningsmiljö som SSIS, SQL Server eller Oracle med Data Factory. När du har skapat en pipeline som utför den åtgärd som du behöver schemalägga du den körs regelbundet (till exempel varje timme, varje dag eller varje vecka), schemaläggning av tid fönster eller utlösaren pipelinen från en händelsen. Mer information finns i [Introduktion till Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Kontrollen flöden och skala 
Om du vill använda olika integreringsflöden och mönster i ett modernt informationslager har Data Factory kan flexibla pipeline datamodellering med fullständig Kontrollflöde programming paradigm inklusive villkorlig körning branchning i datapipelines, och explicit skicka parametrar inom och mellan dessa flöden. Kontrollflöde omfattar även hur du transformerar data via aktivitetssändning till externa körning motorer och data flow-funktioner, inklusive dataförflyttning i skala, via Kopieringsaktiviteten.

Data Factory tillhandahåller friheten att alla flödesstilar som krävs för dataintegrering och som kan skickas på begäran eller regelbundet enligt ett schema. Några vanliga flöden som gör att den här modellen är:   

- Kontrollen flöden:
    - Kedja aktiviteter i sekvens inom en pipeline.
    - Gren aktiviteter inom en pipeline.
    - Parametrar
        - Definiera parametrar på pipelinenivå och skicka argument när du startar pipelinen på begäran eller från en utlösare.
        - Aktiviteter kan använda argumenten som skickas till pipelinen.
    - Skicka anpassade tillstånd
        - Aktivitetsutdata inklusive tillstånd kan användas av en efterföljande aktivitet i pipelinen.
    - Loopningscontainrar
        - For-each 
- Utlösningsbaserade flöden:
    - Pipelines kan utlösas på begäran eller genom wall-clock-tid.
- Deltaflöden:
    - Använd parametrar och definiera ditt övre vattenmärke för deltakopiering vid förflyttning av dimensions- eller referenstabeller från en relationslagringsplats, antingen lokalt eller i molnet, för att läsa in data i sjön. 

Mer information finns i [Självstudie: Kontrollera flöden](tutorial-control-flow.md).

### <a name="transform-your-data-at-scale-with-code-free-pipelines"></a>Transformera data i skala med kod kostnadsfria pipelines
Den nya webbläsarbaserade verktyg-guiden innehåller kodfria pipeline skapande och distribution med en modern, interaktiv webbaserad upplevelse.

För utvecklare för visuella data och datatekniker är Webbgränssnittet ADF kodfria designmiljön som du använder för att skapa pipeliner. Den är helt integrerat med Visual Studio Online Git och ger integrering för CI/CD och iterativ utveckling med felsökning av alternativ.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Omfattande cross platform SDK: er för avancerade användare
Om du är en avancerad användare och behöver ett programmeringsgränssnitt, ADF V2 ger en omfattande uppsättning SDK: er som kan utnyttjas för att skapa, hantera, övervaka pipelines i din favorit-IDE
1.  Python SDK
2.  PowerShell CLI
3.  C# SDK användare kan också dra nytta av dokumenterade REST-API till gränssnitt med ADF V2

### <a name="iterative-development-and-debugging-using-visual-tools"></a>Iterativ utveckling och felsökning med visual tools
Azure Data Factory (ADF) visuella verktyg kan du göra iterativ utveckling och felsökning. Du kan skapa dina pipelines och testa med funktionen för felsökning i rityta för pipelinen utan att behöva skriva en enda rad kod. Du kan visa resultatet av dina testkörningar i utdatafönstret för rityta för pipelinen. När du kör testet lyckas, kan du lägga till fler aktiviteter i din pipeline och fortsätta felsökning i en iterativ sätt. Du kan också avbryta din testkörningar när de inte pågår. Du behöver inte att publicera ändringarna till data factory-tjänsten innan du klickar på felsökning. Det här är användbart i scenarier där du vill kontrollera att det nya tillägg eller ändringar av arbetet som förväntat innan du uppdaterar din data factory-arbetsflöden i utveckling, test eller prod miljöer. 

### <a name="deploy-ssis-packages-to-azure"></a>Distribuera SSIS-paket till Azure 
Om du vill flytta SSIS-arbetsbelastningar kan du skapa en Datafabrik och etablera en Azure-SSIS integration runtime. Azure-SSIS integration runtime är ett fullständigt hanterat kluster av virtuella Azure-datorer (noder) dedikerade för att köra dina SSIS-paket i molnet. Stegvisa instruktioner finns i den [distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md) självstudien. 
 
### <a name="sdks"></a>SDK:er
Om du är en avancerad användare och behöver ett programmeringsgränssnitt har ADF en omfattande uppsättning SDK: er som du kan använda för att skapa, hantera och övervaka pipelines med hjälp av din favorit-IDE. Språk som stöds omfattar .NET, PowerShell, Python och REST.

### <a name="monitoring"></a>Övervakning
Du kan övervaka dina Datafabriker via PowerShell, SDK eller Visual övervakningsverktyg i det webbläsarbaserade användargränssnittet. Du kan övervaka och hantera på begäran, utlösare baserat och klockan driven anpassade flöden på ett effektivt sätt. Avbryt befintliga aktiviteter, se fel på ett ögonblick, granska nedåt för att få detaljerade felmeddelanden och felsöka problem med allt från en enda glasruta utan kontext växlar eller navigera fram och tillbaka mellan skärmar. 

### <a name="new-features-for-ssis-in-adf"></a>Nya funktioner för SSIS i ADF
Data Factory har sedan den första offentliga förhandsversionen 2017 lägger till följande funktioner för SSIS:

-   Stöd för tre flera konfigurationer/varianter av Azure SQL Database (DB) till värden SSIS-katalogen med projekt /-paket (SSISDB):
-   Azure SQL-databas med tjänstslutpunkter för virtuellt nätverk
-   Hanterad instans (MI)
-   Elastisk pool
-   Stöd för Azure Resource Manager-nätverk (VNet) ovanpå klassiskt virtuellt nätverk som upphör att gälla i framtiden – på så sätt kan du mata in/anslutning till din Azure-SSIS Integration Runtime (IR) till ett virtuellt nätverk som har konfigurerats för Azure SQL DB med VNet-tjänsten slutpunkter/MI / lokal åtkomst till data, se: https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network 
-   Stöd för Azure Active Directory (AAD)-autentisering på SQL-autentisering för att ansluta till din SSISDB - det här kan du använda AAD-autentisering med din ADF-hanterad identitet för Azure-resurser
-   Stöd för att leverera dina egna lokala SQL Server-licens och få betydande kostnadsbesparingar från Azure Hybrid Benefit (AHB) alternativet
-   Stöd för Enterprise Edition av Azure-SSIS IR som låter dig använda avancerade/premium-funktioner, anpassade installationsprogrammet för att installera ytterligare komponenter/tillägg och 3 part ekosystem kan du läsa: https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/ 
-   Djupare integrering av SSIS i ADF där du kan anropa/utlösare förstklassig köra SSIS-paket aktiviteter i ADF pipelines och schemalägger dem via SSMS, se: https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/ 


## <a name="what-is-integration-runtime"></a>Vad är integreringskörningen?
Integration runtime är beräkningsinfrastrukturen som används av Azure Data Factory för att tillhandahålla följande funktioner för dataintegrering i olika nätverksmiljöer:

- **Dataförflyttning**: Vid dataförflyttning flyttar Integration Runtime data mellan käll- och måldatalagren. Infrastrukturen har även stöd för inbyggda anslutningsprogram, formatkonvertering, kolumnmappning och effektiv och skalbar dataöverföring.
- **Skicka ut aktiviteter**: För transformering har Integration Runtime inbyggt stöd för körning av SSIS-paket.
- **Köra SSIS-paket**: Internt kör SSIS-paket i en hanterad Azure-beräkningsmiljö. Med Integration Runtime kan du också skicka och övervaka transformeringsaktiviteter som körs i en rad olika beräkningstjänster som Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server och fler.

Du kan distribuera en eller flera instanser av integration runtime som krävs för att flytta och omvandla data. Integration runtime kan köras på en Azures offentliga nätverk eller i ett privat nätverk (lokalt, Azure Virtual Network eller Amazon Web Services virtuellt privat moln [VPC]). 

Mer information finns i [Integration Runtime i Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Vad är gränsen för antalet integreringskörningar?
Det finns ingen hård gräns för antalet integration runtime-instanser som du kan ha i en data factory. Det finns dock en gräns för antalet VM-kärnor som integration runtime kan använda per prenumeration för körning av SSIS-paket. Mer information finns i [Data Factory begränsar](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Vad är toppnivåbegrepp i Azure Data Factory?
En Azure-prenumeration kan ha en eller flera Azure Data Factory-instanser (eller datafabriker). Azure Data Factory innehåller fyra nyckelkomponenter som samverkar som en plattform där du kan skapa datadrivna arbetsflöden med steg för att flytta och omvandla data.

### <a name="pipelines"></a>Pipelines
En datafabrik kan ha en eller flera pipelines. En pipeline är en logisk gruppering av aktiviteter för att utföra en arbetsprocess. Aktiviteterna i en pipeline utför en uppgift tillsammans. En pipeline kan till exempel innehålla en grupp med aktiviteter som matar in data från en Azure-blob och sedan köra en Hive-fråga på ett HDInsight-kluster för att partitionera data. Fördelen är att du kan använda en pipeline för att hantera aktiviteterna som en uppsättning i stället för att hantera varje aktivitet enskilt. Du kan länka ihop aktiviteter i en pipeline kan köras sekventiellt dem, eller du kan använda dem oberoende av varandra, parallellt.

### <a name="activity"></a>Aktivitet
Aktiviteter representerar ett bearbetningssteg i en pipeline. Du kan till exempel använda en *kopia* och kopiera data från ett datalager till ett annat. På samma sätt kan du använda en Hive-aktivitet som kör en Hive-fråga på ett Azure HDInsight-kluster för att transformera eller analysera dina data. Data Factory stöder tre typer av aktiviteter: dataförflyttning, datatransformering och kontroll.

### <a name="datasets"></a>Datauppsättningar
Datauppsättningar representerar datastrukturer i datalager som pekar på eller refererar till de data som du vill använda i dina aktiviteter som indata eller utdata. 

### <a name="linked-services"></a>Länkade tjänster
Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Se det på så sätt: länkade tjänsten definierar anslutningen till datakällan och en datauppsättning representerar strukturen för data. Till exempel anger en länkad Azure Storage-tjänsten anslutningssträngen för att ansluta till Azure Storage-kontot. Och en Azure Blob-datauppsättning anger vilken blobbehållare och mapp som innehåller data.

Länkade tjänster har två syften i Data Factory:

- Att representera en *datalager* som omfattar men är inte begränsat till, en lokal SQL Server-instans, en Oracle database-instans, en filresurs eller ett Azure Blob storage-konto. En lista över datalager som stöds finns i [Kopieringsaktivitet i Azure Data Factory](copy-activity-overview.md).
- Så här visar du en *beräkningsresurs* som kan vara värd för körningen av en aktivitet. Till exempel körs HDInsight Hive-aktiviteten på ett HDInsight Hadoop-kluster. En lista över transformeringsaktiviteter och beräkningsmiljöer som stöds finns i [Transformera data i Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Utlösare
Utlösare representerar enheter av bearbetning som avgör när en pipeline-åtgärd har startats. Det finns olika typer av utlösare för olika typer av händelser. 

### <a name="pipeline-runs"></a>Pipelinekörningar
En pipelinekörning är en instans av en pipelinekörning. Du vanligtvis skapa en instans av en pipeline som körs genom att skicka argument till parametrar som definierats i pipelinen. Du kan skicka argumenten manuellt eller i en utlösardefinition.

### <a name="parameters"></a>Parametrar
Parametrar är nyckel / värde-par i en skrivskyddad konfiguration. Du kan definiera parametrar i en pipeline och argumenten för de definierade parametrarna skickas vid körning från en körningskontexten. Körningskontexten skapas av en utlösare eller från en pipeline som du kör manuellt. Aktiviteter i pipelinen använder parametervärdena.

En datauppsättning är en starkt typifierad parameter och en entitet som du kan återanvända eller referera till. En aktivitet kan referera till datauppsättningar och den kan använda egenskaperna som definierats i definitionen datauppsättning.

En länkad tjänst är också en starkt typifierad parameter som innehåller anslutningsinformationen till antingen ett datalager eller en beräkningsmiljö. Det är också en entitet som du kan återanvända eller referera till.

### <a name="control-flows"></a>Kontrollen flöden
Kontrollen flöden dirigera pipelineaktiviteter som innehåller kedjesammansättning av aktiviteter i en sekvens, branchning, parametrar som du anger på pipelinenivå och argument som du skickar du anropa pipeline på begäran eller från en utlösare. Kontrollen flöden kan även innehålla anpassade tillstånd skicka och loopbehållare (d.v.s. for-each-Iteratorer).


Mer information om Data Factory-begrepp finns i följande artiklar:

- [Datauppsättning och länkade tjänster](concepts-datasets-linked-services.md)
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
Du kan använda scheduler-utlösare eller utlösare för tid fönster för att schemalägga en pipeline. Utlösaren använder ett wall-Clock för kalender och du kan använda den för att schemalägga pipelines med jämna mellanrum eller med hjälp av kalender-baserade återkommande mönster (till exempel varje vecka på måndagen vid 18: 00 och torsdagar kl 9). Mer information finns i [pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Kan jag skicka parametrar till en pipeline-körning?
Ja, parametrar är ett förstklassigt, översta koncept i ADF. Du kan definiera parametrar på pipelinenivå och skicka argument när du kör den pipeline som körs på begäran eller genom att använda en utlösare.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Kan jag ange standardvärden för pipeline-parametrar? 
Ja. Du kan definiera standardvärden för parametrar i pipelines. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>En aktivitet i en pipeline kan använda argumenten som skickas till en pipelinekörning? 
Ja. Varje aktivitet i pipeline kan använda det parametervärde som har skickats till pipelinen och fungerar med den `@parameter` konstruera. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Kan en aktivitetsegenskap utdata användas i en annan aktivitet? 
Ja. Aktivitetsutdata kan användas i en efterföljande aktivitet med den `@activity` konstruera.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Hur ska jag ett smidigt sätt hantera null-värden i en aktivitetsutdata? 
Du kan använda den `@coalesce` konstruera i uttryck att hantera null-värden utan problem. 

## <a name="mapping-data-flows"></a>Mappningen dataflöden

### <a name="which-adf-version-do-i-use-to-create-data-flows"></a>Vilken ADF-version ska jag använda för att skapa Data flödar?
Använd ADF V2-version för att skapa Data flödar
  
### <a name="i-was-a-previous-private-preview-customer-using-data-flows-and-i-used-the-adf-v2-wdata-flows-preview-version"></a>Jag har en tidigare av privata förhandsversionskund som använder Data flödar och jag har använt förhandsversionen ADF V2 w/Data flöden
Den här versionen är nu föråldrad. Använd ADF V2 för dataflöden
  
### <a name="what-has-changed-from-private-preview-to-limited-public-preview-in-data-flows"></a>Vad har ändrats från privat förhandsgranskning till begränsad offentlig förhandsversion i Data flödar?
Du kommer inte längre behöva ta med din egen Databricks-kluster. ADF att hantera klustret har skapats och tear nedåt. BLOB-datauppsättningar och ADLS datauppsättningar är indelade i avgränsad Text och Parquet-datauppsättningar. Du kan fortfarande använda ADLS & Blob Store för att lagra filerna. Använd lämplig länkad tjänst för de motorerna för lagring.

### <a name="can-i-migrate-my-private-preview-factories-to-adf-v2"></a>Kan jag migrera min privat förhandsgranskning fabriker till ADF V2?

[Ja, följer du instruktionerna här](https://www.slideshare.net/kromerm/adf-mapping-data-flow-private-preview-migration)

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-do-you-need"></a>Jag behöver hjälp med att felsöka min logik för flödet av data, vad behöver du?

När Microsoft tillhandahåller hjälp eller felsökning med Data flödar, anger du den ”DSL kod plan (supportavtal). Det gör du genom att följa dessa steg:

* Från Data flöda Designer klickar du på ”Code” i det övre högra hörnet. Då visas redigerbar JSON-koden för dataflödet.
* Kodvy, klicka på ”planera” i det övre högra hörnet. Plan växlingsknappen växlar från JSON till skrivskyddad formaterade DSL skriptet planen.
* Kopiera och klistra in det här skriptet eller spara den i en textfil.

### <a name="how-do-i-access-data-using-the-other-80-dataset-types-in-adf"></a>Hur kommer jag åt data med andra 80 datauppsättningstyper i ADF?

Dataflöde för närvarande kan Azure SQL DB, Azure SQL DW, avgränsade textfiler från Blob eller ADLS och Parquet-filer från Blob eller ADLS internt för källa och mottagare. Använda Kopieringsaktiviteten för att organisera data från någon av de andra anslutningsapparna och sedan köra aktiviteten dataflöde för att omvandla data när det mellanlagras. Till exempel din pipeline för att först kopiera till Blob och sedan aktiviteten dataflöde använder en datauppsättning i källan för att transformera data.

## <a name="next-steps"></a>Nästa steg
Stegvisa instruktioner för att skapa en datafabrik, finns i följande Självstudier:

- [Snabbstart: Skapa en datafabrik](quickstart-create-data-factory-dot-net.md)
- [Självstudier: Kopieringsdata i molnet](tutorial-copy-data-dot-net.md)
