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
ms.date: 01/15/2018
ms.author: shlo
ms.openlocfilehash: b7f654fb3b24954bb1c9222a4e97dc4b6844411c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619039"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory vanliga frågor och svar
Den här artikeln gäller för version 2 av Azure Data Factory-tjänsten. Den innehåller svar på vanliga frågor om Data Factory.  

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [vanliga frågor om Data Factory version 1](v1/data-factory-faq.md).

## <a name="what-is-azure-data-factory"></a>Vad är Azure Data Factory? 
Data Factory är en helt hanterad, molnbaserad, dataintegrering-tjänst som automatiserar rörelser och transformering av data. Azure Data Factory samordnar befintliga tjänster som samlar in rådata och omvandla det till färdiga att använda information som en fabrik som kör utrustning för att omvandla raw material till färdiga varor. 

Du kan skapa datadrivna arbetsflöden för att flytta data mellan lokalt och i molnet med hjälp av Azure Data Factory-datalager. Du kan bearbeta och transformera data med hjälp av compute-tjänster som Azure HDInsight, Azure Data Lake Analytics och SQL Server Integration Services (SSIS) integration runtime. 

Du kan köra din databehandling på en Azure-baserade molntjänst eller i din egen automatisk beräkning värdmiljö, till exempel SSIS, SQL Server eller Oracle med Data Factory. När du har skapat en pipeline som utför den åtgärd som du behöver schemalägga du ska köras regelbundet (t.ex, per timme, varje dag eller varje vecka) eller utlösa pipeline från en händelseförekomst. Mer information finns i [Introduktion till Azure Data Factory](introduction.md).

## <a name="whats-different-in-version-2"></a>Vad är nytt i version 2?
Azure Data Factory version 2 bygger vidare på den ursprungliga dataöverförings- och omvandlingstjänsten och utökar med en bredare uppsättning molninriktade dataintegreringsscenarier. Azure Data Factory version 2 innehåller följande funktioner:

- Kontrollen flöden och skala
- Distribuera och köra SSIS-paket i Azure

Följande 1 version av identifieras att kunder behöver design komplex eller hybrid dataintegrering scenarier som kräver både dataförflyttning och bearbetning i molnet, lokalt och i molnet virtuella maskiner (VMs). Dessa krav sätta behöver överföra och bearbeta data i nätverksmiljöer med skyddade virtuella och skalbar med processorkraft på begäran.

Eftersom data pipelines blir en mer viktig del av en affärsstrategi analytics, har vi sett att dessa aktiviteter kräver flexibla schemaläggning stöd för stegvisa databelastningar och händelsen utlöses körningar. När komplexitet ökar, så har för krav för tjänsten för att stödja vanliga arbetsflöde böjningsmönster som inkluderar förgrening, slingor och villkorliga.

Med version 2 kan du också migrera befintliga SSIS-paket till molnet. Den här åtgärden lyfter och flyttas SSIS som en Azure-tjänst som hanteras i Data Factory som använder en ny funktion för integrering av körningen. Av snurrande upp en SSIS-integrering körning i version 2 kan du köra, hantera, övervaka och skapa SSIS-paket i molnet.

### <a name="control-flows-and-scale"></a>Kontrollen flöden och skala 
För att stödja olika integration flöden och mönster i moderna datalagret, har Data Factory aktiverats i en ny, flexibel och pipeline datamodell som inte längre är knutet till time series-data. Med den här versionen kan du modellera villkorlig sats, grenen Kontrollflöde för en data-pipeline, och explicit skicka parametrar inom och över flödena.

Nu har du friheten att modellera ett flöde format som krävs för dataintegrering och som kan skickas på begäran eller upprepade gånger enligt ett schema. Ett par vanliga flöden som nu är tillgängliga:   

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

### <a name="deploy-ssis-packages-to-azure"></a>Distribuera SSIS-paket till Azure 
Om du vill flytta SSIS-arbetsbelastningar du skapa en Datafabrik version 2 och etablera en Azure-SSIS-integrering körning. Azure-SSIS-integrering runtime är en helt hanterad kluster på virtuella Azure-datorer (noder) som är dedikerad för att köra SSIS-paket i molnet. Stegvisa instruktioner finns i [distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md) kursen. 
 

### <a name="sdks"></a>SDK:er
Om du är en avancerad användare och söker efter ett programmeringsgränssnitt, version 2 ger en omfattande uppsättning SDK: er som du kan använda för att skapa, hantera eller övervaka pipelines med hjälp av din favorit IDE.

- **.NET SDK**: .NET SDK har uppdaterats för version 2. 
- **PowerShell**: PowerShell-cmdletarna har uppdaterats för version 2. Cmdletar för version 2 har *DataFactoryV2* i namnet. Till exempel *Get-AzureRmDataFactoryV2*. 
- **Python SDK**: Den här SDK:n är ny i version 2.
- **REST API**: REST API har uppdaterats för version 2.  

SDK:erna som har uppdaterats för version 2 är inte bakåtkompatibla med version 1-klienter. 

### <a name="monitoring"></a>Övervakning
Version 2 har för närvarande stöd för övervakning av datafabriker med hjälp av endast SDK:er. Portalen har inte stöd för övervakning av datafabriker med version 2 än. 

## <a name="what-is-integration-runtime"></a>Vad är integration runtime?
Integration runtime är beräkningsinfrastrukturen som används av Azure Data Factory för att tillhandahålla följande funktioner för integration av data över olika nätverksmiljöer:

- **Dataflyttning**: flytta data mellan datalager i offentliga nätverk och data som lagras i ett privat nätverk (lokalt eller virtuellt privat nätverk). Den ger stöd åt inbyggda anslutningsappar, konvertering av format, kolumnmappning och bättre och skalbar dataöverföring.
- **Skicka aktiviteter**: avsändande och Övervakare för omvandling av aktiviteter som körs på en mängd olika compute-tjänster, till exempel Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server och mycket mer.
- **Köra SSIS-paket**: internt kör SSIS-paket i en hanterad Azure compute-miljö.

Du kan distribuera en eller flera instanser av integration runtime som krävs för att flytta och transformera data. Integration körning kan köras på ett Azure offentligt nätverk eller i ett privat nätverk (lokalt, Azure Virtual Network eller Amazon Web Services virtuella privat moln [Virtual PC]). 

Mer information finns i [Integration Runtime i Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Vad är gränsen för antalet integration körningar?
Det finns ingen hård gräns för antalet integration runtime-instanser i en datafabrik. Det finns dock en gräns för antalet Virtuella kärnor integration körningen kan använda per prenumeration för körning av SSIS-paket. Mer information finns i [Data Factory begränsar](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="when-should-i-use-version-2-rather-than-version-1"></a>När bör jag använda version 2 i stället för version 1? 
Om du är nybörjare på Azure Data Factory starta direkt med version 2. Om du redan använder version 1, återskapa din datafabriker på version 2.

> [!WARNING]
> Det är inte i allmänhet tillgänglighet (GA) version 2 av Data Factory är i förhandsgranskningen. Därför kan omfattas den inte av samma Azure-tjänsten servicenivåavtal (SLA) åtaganden som version 1 av Data Factory i GA. 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>Vilka är de översta principerna för version 2?
En Azure-prenumeration kan ha en eller flera Azure Data Factory-instanser (eller datafabriker). Azure Data Factory innehåller fyra viktiga komponenter som arbetar tillsammans som en plattform som du kan skapa datadrivna arbetsflöden med steg för att flytta och transformera data.

### <a name="pipelines"></a>Pipelines
En datafabrik kan ha en eller flera pipelines. En pipeline är en logisk gruppering av aktiviteter för att utföra en arbetsenhet. Aktiviteterna i en pipeline utför en uppgift tillsammans. Till exempel kan en pipeline innehålla en grupp av aktiviteter som kan mata in data från en Azure blob och sedan köra en Hive-fråga på ett HDInsight-kluster för att partitionera data. Fördelen är att du använder en pipeline för att hantera aktiviteterna som en uppsättning i stället för att hantera varje aktivitet individuellt. Du kan länka tillsammans aktiviteterna i en rörledning för att använda dem sekventiellt eller du kan använda dem oberoende av varandra, parallellt.

### <a name="activity"></a>Aktivitet
Aktiviteter representerar ett bearbetningssteg i en pipeline. Du kan till exempel använda en *kopiera* aktivitet för att kopiera data från ett datalager till ett annat datalager. På liknande sätt kan du använda en Hive-aktivitet som körs en Hive-fråga på ett Azure HDInsight-kluster att transformera och analysera dina data. Data Factory stöder tre typer av aktiviteter: dataförflyttning, datatransformering och kontroll.

### <a name="data-sets"></a>Datauppsättningar
Datauppsättningar representerar datastrukturer i datalager som helt enkelt peka eller refererar till de data som du vill använda i dina aktiviteter som indata eller utdata. 

### <a name="linked-services"></a>Länkade tjänster
Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Se det här sättet: en länkad tjänst definierar anslutningen till datakällan och en datauppsättning representerar strukturen för data. Till exempel anger en länkad Azure Storage-tjänst anslutningssträngen att ansluta till Azure Storage-konto. Och en uppsättning i Azure Blob-data anger blob-behållaren och mappen som innehåller data.

Länkade tjänster har två syften i Data Factory:

- Att representera en *datalagret* som inkluderar, men är inte begränsat till en lokal SQL Server-instans, en Oracle-databasinstans, en filresurs eller ett Azure Blob storage-konto. En lista över datakällor som stöds, se [Kopieringsaktiviteten i Azure Data Factory](copy-activity-overview.md).
- Så här visar du en *beräkningsresurs* som kan vara värd för körningen av en aktivitet. Till exempel körs HDInsight Hive-aktiviteten på ett HDInsight Hadoop-kluster. En lista över omvandling aktiviteter och stöds beräknings-miljöer finns [Transformera data i Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Utlösare
Utlösare representerar enheter av bearbetning som bestämmer när en pipelinekörningen har inletts. Det finns olika typer av utlösare för olika typer av händelser. Vi stöder en brandvägg klockan scheduler-utlösare för förhandsgranskning. 


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

## <a name="what-regions-support-azure-data-factory-version-2"></a>Vilka regioner stöd för Azure Data Factory version 2?
För närvarande kan du skapa datafabriker för version 2 i östra USA, östra USA 2 och Västeuropa regioner. En datafabrik kan dock använda integration körning i en annan region för att flytta data mellan datalager, dispatch aktiviteter mot beräkning tjänster eller dispatch SSIS-paket. Mer information finns i [Data Factory platser](concepts-integration-runtime.md#integration-runtime-location).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Hur kan jag hålla dig uppdaterad med information om Data Factory?
Den senaste informationen om Azure Data Factory finns på följande webbplatser:

- [Blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Startsidan för dokumentation](/azure/data-factory)
- [Startsidan för produkten](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Tekniska ingående 

### <a name="can-version-1-and-version-2-pipelines-run-side-by-side"></a>Kan version 1 och version 2 pipelines köra sida vid sida?
Nej. Version 2 och version 1 datafabriker får inte innehålla entiteter (till exempel länkade tjänster, datauppsättningar eller pipelines) av den andra versionen.   

### <a name="do-i-still-need-to-define-data-sets-in-version-2"></a>Behöver jag definiera datauppsättningarna i version 2?
En datauppsättning är inte längre en obligatorisk entitet för de flesta aktiviteter. Det krävs för att kopiera, maskininlärning, sökning, verifiering och anpassade aktiviteter som använder schemat och andra metadata-information i datauppsättningen för omvandling. Övriga aktiviteter kräver inte längre datauppsättningar.

### <a name="can-i-chain-two-activities-without-a-data-set-in-version-2"></a>Kan jag kedja två aktiviteter utan en datamängd i version 2?
Ja. Du kan länka aktiviteter i version 2 utan datamängder. Du kopplar aktiviteter med hjälp av den **dependsOn** egenskap i JSON-definitionen av din pipeline. 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>Är versionen 1 aktiviteter som stöds i version 2? 
Ja, stöds alla version 1 aktiviteter i version 2.

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>Hur kan jag schemalägga en version 2-pipeline? 
Du kan använda Schemaläggaren utlösaren för att schemalägga en version 2-pipeline. Utlösaren använder ett schema för brandvägg klockan kalender och du kan använda för att schemalägga pipelines regelbundet eller genom att använda kalender-baserade återkommande mönster (t.ex, för varje vecka på måndagen vid 18: 00 och torsdagen vid 21: 00). Mer information finns i [pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>Kan jag skicka parametrar för en pipeline som körs i version 2?
Ja, kan ett förstklassigt, översta koncept i version 2. Du kan definiera parametrar nivån pipeline och skicka argument när du kör pipeline kör på begäran eller genom att använda en utlösare.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Kan jag ange standardvärden för parametrarna pipeline? 
Ja. Du kan definiera standardvärden för parametrar i pipelines. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Kan använda en aktivitet i en pipeline argument som skickas till en pipeline kör? 
Ja. Varje aktivitet i pipelinen kan använda parametervärdet som har överförts till pipeline och fungerar med den `@parameter` konstruera. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>En aktivitetsegenskap utdata förbrukas i en annan aktivitet? 
Ja. Aktivitetsutdata kan användas i en efterföljande aktivitet med den `@activity` konstruera.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Hur jag hantera null-värden i aktivitetens utdata? 
Du kan använda den `@coalesce` konstruera i uttryck för att hantera null-värden utan problem. 

### <a name="can-i-use-retry-and-timeout-at-the-activity-level-in-version-2"></a>Kan jag använda försök och timeout på aktivitetsnivå i version 2?
Ja. För att reglera körning av aktiviteter i version 2 som du gör i version 1, kan du konfigurera försök och timeout på aktivitetsnivå. 

## <a name="next-steps"></a>Nästa steg
Stegvisa instruktioner för att skapa en datafabrik för version 2 finns i följande kurser:

- [Snabbstart: Skapa en datafabrik](quickstart-create-data-factory-dot-net.md)
- [Självstudier: Kopiera data i molnet](tutorial-copy-data-dot-net.md)

