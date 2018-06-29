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
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: ebe8745db06113d0508d86554bf031a4235c8e44
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045957"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory vanliga frågor och svar
Den här artikeln innehåller svar på vanliga frågor och svar om Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Vad är Azure Data Factory? 
Data Factory är en helt hanterad, molnbaserad, dataintegrering-tjänst som automatiserar rörelser och transformering av data. Azure Data Factory samordnar befintliga tjänster som samlar in rådata och omvandla det till färdiga att använda information som en fabrik som kör utrustning för att omvandla raw material till färdiga varor. 

Du kan skapa datadrivna arbetsflöden för att flytta data mellan lokalt och i molnet med hjälp av Azure Data Factory-datalager. Du kan bearbeta och transformera data med hjälp av compute-tjänster som Azure HDInsight, Azure Data Lake Analytics och SQL Server Integration Services (SSIS) integration runtime. 

Du kan köra din databehandling på en Azure-baserade molntjänst eller i din egen automatisk beräkning värdmiljö, till exempel SSIS, SQL Server eller Oracle med Data Factory. När du har skapat en pipeline som utför den åtgärd som du behöver schemalägga du kör regelbundet (t.ex, per timme, varje dag eller varje vecka), tid fönstret schemaläggning eller utlösare pipeline från en händelseförekomst. Mer information finns i [Introduktion till Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Kontrollen flöden och skala 
För att stödja olika integration flöden och mönster i moderna datalagret, aktiverar Data Factory flexibla data pipeline modellering som innehåller fullständig Kontrollflöde programming böjningsmönster inklusive villkorlig körning förgrening i data pipelines och explicit skicka parametrar inom och över flödena. Kontrollflöde omfattar också Omforma data via aktiviteten avsändning till externa körning motorer och funktioner för flödet av data, inklusive flytt av data i skala, via aktiviteten kopiera.

Data Factory ger friheten att modellera ett flöde format som krävs för dataintegrering och som kan skickas på begäran eller upprepade gånger enligt ett schema. Några vanliga flöden som gör att den här modellen är:   

- Kontrollen flöden:
    - Kedjan aktiviteter i en sekvens inom en pipeline.
    - Branch aktiviteter i en pipeline.
    - Parametrar
        - Definiera parametrar nivån pipeline och skicka argument medan du anropa pipelinen på begäran eller från en utlösare.
        - Aktiviteter kan använda argumenten som skickas till pipelinen.
    - Skicka anpassade tillstånd
        - Aktivitetsutdata inklusive tillstånd kan användas av en efterföljande aktivitet i pipelinen.
    - Loopningsbehållare
        - For-each 
- Utlösaren-baserade flöden:
    - Pipelines kan aktiveras på begäran eller av brandvägg systemtid.
- Delta flöden:
    - Använda parametrar och definiera dina delta kopiera vattenmärke när du flyttar tabeller för dimensionen eller referens från en relationsdatabas store, antingen lokalt eller i molnet, att läsa in data till lake. 

Mer information finns i [Självstudier: kontrollera flöden](tutorial-control-flow.md).

### <a name="transform-your-data-at-scale-with-code-free-pipelines"></a>Transformera data i skala med kod ledigt pipelines
Den nya upplevelsen webbläsarbaserad verktygsuppsättning innehåller koden utan pipeline skapande och distribution med en modern, interaktiva webbaserade upplevelse.

För utvecklare av visuella data och data tekniker är Webbgränssnittet ADF kod utan designmiljön som du använder för att skapa pipelines. Den är helt integrerat med Visual Studio Online Git och ger CI/CD-integrering och iterativ utveckling med alternativen för felsökning.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Omfattande cross platform SDK: er för avancerade användare
Om du är en avancerad användare söker efter ett programmeringsgränssnitt ADF V2 ger en omfattande uppsättning SDK: er som kan utnyttjas för att skapa, hantera och övervaka pipelines som använder din favorit IDE
1.  Python SDK
2.  PowerShell CLI
3.  C# SDK användare kan också använda dokumenterade REST API: er i gränssnittet med ADF V2

### <a name="iterative-development-and-debugging-using-visual-tools"></a>Iterativ utveckling och felsökning med hjälp av visual verktyg
Azure Data Factory (ADM) visual verktyg kan du göra iterativ utveckling och felsökning. Du kan skapa din pipelines och testa körs med Debug-funktionen i pipeline-arbetsytan utan att skriva en enda rad kod. Du kan visa resultatet av din testkörningar i utdatafönstret för pipeline-arbetsytan. När du kör testet lyckas, du lägger till flera aktiviteter i din pipeline och fortsätta felsökningen iterativ sätt. Du kan också avbryta din testkörningar när de är pågående. Du behöver inte publicera ändringarna data factory-tjänsten innan du klickar på felsökning. Det här är användbart i scenarier där du vill kontrollera att nya tillägg eller ändringar arbetet som förväntat innan du uppdaterar din data factory arbetsflöden i dev-, test eller prod miljöer. 

### <a name="deploy-ssis-packages-to-azure"></a>Distribuera SSIS-paket till Azure 
Om du vill flytta SSIS-arbetsbelastningar du skapa en Datafabrik och etablera en Azure-SSIS-integrering körning. Azure-SSIS-integrering runtime är en helt hanterad kluster på virtuella Azure-datorer (noder) som är dedikerad för att köra SSIS-paket i molnet. Stegvisa instruktioner finns i [distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md) kursen. 
 
### <a name="sdks"></a>SDK:er
Om du är en avancerad användare och söker efter ett programmeringsgränssnitt ADF ger en omfattande uppsättning SDK: er som du kan använda för att skapa, hantera eller övervaka pipelines med hjälp av din favorit IDE. Språkstöd innehåller .NET, PowerShell, Python och REST.

### <a name="monitoring"></a>Övervakning
Du kan övervaka dina Datafabriker via PowerShell, SDK eller Visual övervakningsverktyg i användargränssnittet för webbläsaren. Du kan övervaka och hantera på begäran, utlösning baserat och klockan drivs anpassade flöden på ett effektivt sätt. Avbryt befintliga aktiviteter, se fel en överblick över detaljnivån att hämta detaljerade felmeddelanden och felsöka problem allt från en och samma plats utan kontext växlar eller bläddra fram och tillbaka mellan skärmar. 

### <a name="new-features-for-ssis-in-adf"></a>Nya funktioner för SSIS i ADF
Data Factory har sedan den första versionen förhandsversion i 2017 lägga till följande funktioner för SSIS:

-   Stöd för tre flera konfigurationer/varianter av Azure SQL Database (DB) till värden SSIS-katalogen för projekt/paket (SSISDB):
-   Azure SQL-databas med slutpunkter för virtuella nätverk
-   Hanterade instansen (MI)
-   Elastisk pool
-   Stöd för Azure Resource Manager-nätverk (VNet) ovanpå klassiska VNet som kommer att inaktualiseras i framtiden – kan du mata in/ansluta till din Azure-SSIS Integration Runtime (IR) till ett virtuellt nätverk som har konfigurerats för Azure SQL-databas med VNet service slutpunkter/MI / lokal åtkomst till data, se: https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network 
-   Stöd för Azure Active Directory (AAD) autentisering ovanpå SQL-autentisering för att ansluta till din SSIDB - kan du använda AAD-autentisering med din ADF hanterade tjänsten identitet (MSI)
-   Stöd för att skapa egna lokala SQL Server-licens för att få betydande besparingar från Azure Hybrid förmånen (AHB) alternativet
-   Stöd för Enterprise Edition av Azure-SSIS IR som låter dig använda avancerade/premium-funktioner, anpassad installation för att installera ytterligare komponenter-tillägg och 3 part ekosystem, se: https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/ 
-   Djupare integrering av SSIS i ADF där du kan anropa/utlösare förstklassigt köra SSIS-paket aktiviteter i ADF pipelines och schemalägger dem via SSMS, se: https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/ 


## <a name="what-is-integration-runtime"></a>Vad är integration runtime?
Integration runtime är beräkningsinfrastrukturen som används av Azure Data Factory för att tillhandahålla följande funktioner för integration av data över olika nätverksmiljöer:

- **Dataflyttning**: för flytt av data, Integration Runtime flyttar data mellan käll- och datalager, samtidigt som man har stöd för inbyggda kopplingar, konvertering av format, kolumnmappningen, och performant och skalbar dataöverföring.
- **Skicka aktiviteter**: för omvandling, Integration Runtime funktioner för att köra internt SSIS-paket.
- **Köra SSIS-paket**: internt kör SSIS-paket i en hanterad Azure compute-miljö. Med Integration Runtime kan du också skicka och övervaka transformeringsaktiviteter som körs i en rad olika beräkningstjänster som Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server och fler.

Du kan distribuera en eller flera instanser av integration runtime som krävs för att flytta och transformera data. Integration körning kan köras på ett Azure offentligt nätverk eller i ett privat nätverk (lokalt, Azure Virtual Network eller Amazon Web Services virtuella privat moln [Virtual PC]). 

Mer information finns i [Integration Runtime i Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Vad är gränsen för antalet integration körningar?
Det finns ingen hård gräns för antalet integration runtime-instanser i en datafabrik. Det finns dock en gräns för antalet Virtuella kärnor integration körningen kan använda per prenumeration för körning av SSIS-paket. Mer information finns i [Data Factory begränsar](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Vad är Azure Data Factory översta begreppen?
En Azure-prenumeration kan ha en eller flera Azure Data Factory-instanser (eller datafabriker). Azure Data Factory innehåller fyra viktiga komponenter som arbetar tillsammans som en plattform som du kan skapa datadrivna arbetsflöden med steg för att flytta och transformera data.

### <a name="pipelines"></a>Pipelines
En datafabrik kan ha en eller flera pipelines. En pipeline är en logisk gruppering av aktiviteter för att utföra en arbetsenhet. Aktiviteterna i en pipeline utför en uppgift tillsammans. Till exempel kan en pipeline innehålla en grupp av aktiviteter som kan mata in data från en Azure blob och sedan köra en Hive-fråga på ett HDInsight-kluster för att partitionera data. Fördelen är att du använder en pipeline för att hantera aktiviteterna som en uppsättning i stället för att hantera varje aktivitet individuellt. Du kan länka tillsammans aktiviteterna i en rörledning för att använda dem sekventiellt eller du kan använda dem oberoende av varandra, parallellt.

### <a name="activity"></a>Aktivitet
Aktiviteter representerar ett bearbetningssteg i en pipeline. Du kan till exempel använda en *kopiera* aktivitet för att kopiera data från ett datalager till ett annat datalager. På liknande sätt kan du använda en Hive-aktivitet som körs en Hive-fråga på ett Azure HDInsight-kluster att transformera och analysera dina data. Data Factory stöder tre typer av aktiviteter: dataförflyttning, datatransformering och kontroll.

### <a name="datasets"></a>Datauppsättningar
Datauppsättningar representerar datastrukturer i datalager som pekar på eller refererar till de data som du vill använda i dina aktiviteter som indata eller utdata. 

### <a name="linked-services"></a>Länkade tjänster
Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Se det här sättet: en länkad tjänst definierar anslutningen till datakällan och en datauppsättning representerar strukturen för data. Till exempel anger en länkad Azure Storage-tjänst anslutningssträngen att ansluta till Azure Storage-konto. Och en uppsättning i Azure Blob-data anger blob-behållaren och mappen som innehåller data.

Länkade tjänster har två syften i Data Factory:

- Att representera en *datalagret* som inkluderar, men är inte begränsat till en lokal SQL Server-instans, en Oracle-databasinstans, en filresurs eller ett Azure Blob storage-konto. En lista över datakällor som stöds, se [Kopieringsaktiviteten i Azure Data Factory](copy-activity-overview.md).
- Så här visar du en *beräkningsresurs* som kan vara värd för körningen av en aktivitet. Till exempel körs HDInsight Hive-aktiviteten på ett HDInsight Hadoop-kluster. En lista över omvandling aktiviteter och stöds beräknings-miljöer finns [Transformera data i Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Utlösare
Utlösare representerar enheter av bearbetning som bestämmer när en pipelinekörningen har inletts. Det finns olika typer av utlösare för olika typer av händelser. 

### <a name="pipeline-runs"></a>Pipelinekörningar
En pipeline kör är en instans av en pipeline-åtgärd. Du vanligtvis skapa en instans av en pipeline som kör genom att skicka argumenten till de parametrar som definierats i pipelinen. Du kan överföra argument manuellt eller i definitionen för utlösare.

### <a name="parameters"></a>Parametrar
Parametrarna är nyckel / värde-par i en skrivskyddad konfiguration. Du definierar parametrar i en pipeline och du skickar argumenten för definierade parametrar under körning från en kontext som kör. Kör kontexten har skapats med en utlösare eller från en pipeline som du kör manuellt. Aktiviteter i pipelinen använder parametervärdena.

En datauppsättning är en starkt typifierad parameter och en enhet som du kan återanvända eller en referenstyp. En aktivitet kan referera till datamängder och den kan använda de egenskaper som är definierade i datauppsättningen definition.

En länkad tjänst är också en starkt typifierad parameter som innehåller anslutningsinformationen för ett datalager eller en beräknings-miljön. Det är också en entitet som du kan återanvända eller refererar till.

### <a name="control-flows"></a>Kontrollen flöden
Kontrollen flöden samordna pipeline-aktiviteter som innehåller länkning aktiviteter i en sekvens, förgrening, parametrar som du anger på pipeline-nivå och argument som du skickar som du anropa pipelinen på begäran eller från en utlösare. Kontrollen flöden omfattar också anpassad tillstånd skicka och slingor behållare (det vill säga för varje Iteratorer).


Mer information om Data Factory-begrepp finns i följande artiklar:

- [Datauppsättning och länkade tjänster](concepts-datasets-linked-services.md)
- [Pipelines och aktiviteter](concepts-pipelines-activities.md)
- [Integration runtime](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Vad är priserna för Data Factory?
Azure Data Factory prisinformation finns [Data Factory prisinformation](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Hur kan jag hålla dig uppdaterad med information om Data Factory?
Den senaste informationen om Azure Data Factory finns på följande webbplatser:

- [Blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Startsidan för dokumentation](/azure/data-factory)
- [Startsidan för produkten](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Tekniska ingående 

### <a name="how-can-i-schedule-a-pipeline"></a>Hur kan jag schemalägga en pipeline? 
Du kan använda Schemaläggaren utlösare eller tid fönstret utlösare för att schemalägga en pipeline. Utlösaren använder ett schema för brandvägg klockan kalender och du kan använda för att schemalägga pipelines regelbundet eller genom att använda kalender-baserade återkommande mönster (t.ex, för varje vecka på måndagen vid 18: 00 och torsdagen vid 21: 00). Mer information finns i [pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Kan jag skicka parametrar till en pipeline-körning?
Ja, kan ett förstklassigt, översta koncept i ADF. Du kan definiera parametrar nivån pipeline och skicka argument när du kör pipeline kör på begäran eller genom att använda en utlösare.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Kan jag ange standardvärden för parametrarna pipeline? 
Ja. Du kan definiera standardvärden för parametrar i pipelines. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Kan använda en aktivitet i en pipeline argument som skickas till en pipeline kör? 
Ja. Varje aktivitet i pipelinen kan använda parametervärdet som har överförts till pipeline och fungerar med den `@parameter` konstruera. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>En aktivitetsegenskap utdata förbrukas i en annan aktivitet? 
Ja. Aktivitetsutdata kan användas i en efterföljande aktivitet med den `@activity` konstruera.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Hur jag hantera null-värden i aktivitetens utdata? 
Du kan använda den `@coalesce` konstruera i uttryck för att hantera null-värden utan problem. 

## <a name="next-steps"></a>Nästa steg
Stegvisa instruktioner för att skapa en datafabrik, finns i följande kurser:

- [Snabbstart: Skapa en datafabrik](quickstart-create-data-factory-dot-net.md)
- [Självstudier: Kopiera data i molnet](tutorial-copy-data-dot-net.md)
