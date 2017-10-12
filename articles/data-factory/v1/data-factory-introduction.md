---
title: "Introduktion till Data Factory, en dataintegreringstjänst | Microsoft Docs"
description: "Detta är vad Azure Data Factory är: en molnbaserad dataintegreringstjänst som samordnar och automatiserar förflyttning och transformering av data."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/14/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 8d4d574dee4b993d8a464482b244e1f63ade8a57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-data-factory"></a>introduktion till Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-introduction.md)
> * [Version 2 – förhandsversion](../introduction.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av tjänsten Data Factory, som finns i förhandsversion, läser du [introduktionen till Data Factory V2](../introduction.md).


## <a name="what-is-azure-data-factory"></a>Vad är Azure Data Factory?
Hur utnyttjas befintliga data i företag? Är det möjligt att utöka information som genereras i molnet med referensdata från lokala datakällor eller andra olika datakällor? Ett spelföretag till exempel samlar in många loggar som producerats av spel i molnet. Företaget vill analysera loggarna för att få insikt i kundens önskemål, kundområden, användningsbeteende osv. för att identifiera möjligheter till merförsäljning och korsförsäljning, utveckla nya attraktiva funktioner för att öka tillväxten och ge kunderna en bättre upplevelse. 

När företaget ska analysera loggarna måste det använda referensdata, till exempel kundinformation, spelinformation eller marknadsföringskampanjinformation som finns i ett lokalt datalager. Därför behöver företaget mata in loggdata från molndatalagret och referensdata från det lokala datalagret. Sedan bearbetar företaget data med Hadoop i molnet (Azure HDInsight) och publicerar resultatdata i ett molndatalager som Azure SQL Data Warehouse eller i ett lokalt datalager som SQL Server. De vill att arbetsflödet ska köras en gång i veckan. 

Det behövs en plattform som ger företaget möjlighet att skapa ett arbetsflöde som kan föra in data både från lokala datalager och datalager i molnet och omvandla eller bearbeta data med befintliga beräkningstjänster som Hadoop, och publicera resultatet för ett lokalt datalager eller molndatalager som BI-program kan använda. 

![Data Factory-översikt](media/data-factory-introduction/what-is-azure-data-factory.png) 

Azure Data Factory är plattformen för sådana typer av scenarier. Det är en **molnbaserad dataintegreringstjänst som gör att du kan skapa datadrivna arbetsflöden i molnet för att samordna och automatisera dataförflyttning och dataomvandling**. Med Azure Data Factory kan du skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som kan föra in data från olika datalager, bearbeta/omvandla data med beräkningstjänster som Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics och Azure Machine Learning och publicera utgående data till datalager som Azure SQL Data Warehouse för BI-program (business intelligence) kan använda.  

Det är snarare som en EL-plattform (Extract-and-Load) och sedan en TL-plattform (Transform-and-Load) än en traditionell ETL-plattform (Extract-Transform-and-Load). De omvandlingar som utförs görs för att omvandla/bearbeta data med beräkningstjänster istället för att utföra omvandlingar som dem för att lägga till härledda kolumner, räkna antal rader, ordna data osv. 

För närvarande är data som används och produceras av arbetsflöden i Azure Data Factory **tidsuppdelade** (varje timme, dagligen, varje vecka osv.). En pipeline kan till exempel läsa indata, bearbeta data och skapa utdata en gång om dagen. Du kan också köra ett arbetsflöde bara en gång.  
  

## <a name="how-does-it-work"></a>Hur fungerar det? 
Pipelines (datadrivna arbetsflöden) i Azure Data Factory utför vanligen följande tre steg:

![Tre nivåer i Azure Data Factory](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>Ansluta och samla in
Företag har olika typer av data lagrade i olika källor. Det första steget när det gäller att skapa ett informationsproduktionssystem är att skapa en anslutning till alla nödvändiga data- och bearbetningskällor, exempelvis SaaS-tjänster, filresurser, FTP och webbtjänster och att kunna flytta data till en central plats för bearbetning när det behövs.

Utan Data Factory måste företag skapa egna dataöverföringskomponenter eller skriva anpassade tjänster för att integrera dessa data- och bearbetningskällor. Det är dyrt och svårt att integrera och underhålla sådana system, och ofta saknas de funktioner för övervakning, avisering och kontroll som en fullt hanterad tjänst kan erbjuda.

Med Data Factory kan du använda kopieringsaktiviteten i en datapipeline för att flytta data från datalager lokalt och molnet till ett centralt datalager i molnet där du kan analysera dem. Du kan till exempel samla in data i en Azure Data Lake Store och sedan omvandla dessa data med en Azure Data Lake Analytics-databearbetningstjänst. Eller så kan du samla data i en Azure Blob Storage och sedan omvandla data med ett Azure HDInsight Hadoop-kluster.

### <a name="transform-and-enrich"></a>Omvandla och berika
När data presenteras i ett centraliserat datalager i molnet kan insamlade data bearbetas eller omvandlas med beräkningstjänster som HDInsight Hadoop, Spark, Data Lake Analytics och Machine Learning. Du kan producera omvandlade data på ett tillförlitligt sätt enligt ett anpassningsbart schema och fylla på med betrodda data i produktionsmiljöer. 

### <a name="publish"></a>Publicera 
Leverera omvandlade data från molnet till lokala källor som SQL Server, eller behåll dem i dina molnlagringskällor för konsumtion av Business Intelligence (BI) och analysverktyg och andra program.

## <a name="key-components"></a>Nyckelkomponenter
En Azure-prenumeration kan ha en eller flera Azure Data Factory-instanser (eller datafabriker). Azure Data Factory består av fyra nyckelkomponenter som samverkar för att tillhandahålla en plattform för att skapa datadrivna arbetsflöden med steg för att flytta och omvandla data. 

### <a name="pipeline"></a>Pipeline
En datafabrik kan ha en eller flera pipelines. En pipeline är en grupp av aktiviteter. Aktiviteterna i en pipeline utför en uppgift tillsammans. En pipeline kan till exempel innehålla en grupp med aktiviteter som matar in data från en Azure-blob och som sedan kör en Hive-fråga på ett HDInsight-kluster för att partitionera data. Fördelen med detta är att pipelinen låter dig hantera aktiviteterna som en grupp i stället för enskilt. Du kan till exempel distribuera och schemalägga en hel pipeline i stället för att göra detta separat för varje aktivitet. 

### <a name="activity"></a>Aktivitet
En pipeline kan innehålla en eller flera aktiviteter. Aktiviteter definierar de åtgärder som ska utföras på dina data. Du kan till exempel använda en kopieringsaktivitet för att kopiera data från ett datalager till ett annat. På samma sätt kan du använda en Hive-aktivitet som kör en Hive-fråga på ett Azure HDInsight-kluster för att transformera eller analysera dina data. Data Factory stöder två typer av aktiviteter: aktiviteter för dataförflyttning och datatransformering.

### <a name="data-movement-activities"></a>Dataförflyttningsaktiviteter
Kopieringsaktiviteten i Data Factory kopierar data från källans datalager till mottagarens datalager. Data Factory har stöd för följande datalager. Data kan skrivas från valfri källa till valfri mottagare. Klicka på ett datalager om du vill veta hur du kopierar data till och från det datalagret.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Mer information finns i artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Datatransformeringsaktiviteter
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Mer information finns i artikeln [Datatransformeringsaktiviteter](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Anpassa .NET-aktiviteter
Om du behöver flytta data till/från ett datalager som Kopiera aktivitet inte stöder, eller transformera data med egen logik, kan du skapa en **anpassad .NET-aktivitet**. Mer information om att skapa och använda en anpassad aktivitet finns i [Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)](data-factory-use-custom-activities.md).

### <a name="datasets"></a>Datauppsättningar
En aktivitet har noll eller flera datauppsättningar som indata och en eller flera datauppsättningar som utdata. Datauppsättningar representerar datastrukturer i datalager som pekar på eller refererar till de data som du vill använda i dina aktiviteter som indata eller utdata. En Azure Blob-datauppsättning anger till exempel blobbehållaren och -mappen i Azure Blob Storage som pipelinen ska läsa data från. Eller så kan en Azure SQL-tabelldatauppsättning ange vilken tabell utdata ska skrivas till av aktiviteten. 

### <a name="linked-services"></a>Länkade tjänster
Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Man kan se det som att datamängden representerar strukturen för data och den länkade tjänsten definierar anslutningen till datakällan. Till exempel anger en länkad Azure Storage-tjänst en anslutningssträng för att ansluta till ett Azure Storage-konto. Och en Azure Blob-datauppsättning anger vilken blobbehållare och mapp som innehåller data.   

Länkade tjänster används för två syften i Data Factory:

* Att representera ett **datalager** inklusive, men inte begränsat till, en lokal SQL Server, Oracle-databas, filresurs eller ett Azure Blob Storage-konto. I avsnittet [Dataförflyttningsaktiviteter](#data-movement-activities) finns en lista över datalager som stöds.
* Så här visar du en **beräkningsresurs** som kan vara värd för körningen av en aktivitet. HDInsightHive-aktiviteten körs till exempel på ett HDInsight Hadoop-kluster. Se avsnittet [Datatransformeringsaktiviteter](#data-transformation-activities), för en lista över compute-miljöer som stöds.

### <a name="relationship-between-data-factory-entities"></a>Förhållande mellan Data Factory-enheter
![Diagram: Data Factory, en molnbaserad dataintegreringstjänst – nyckelbegrepp](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**Figur 2.** Relationer mellan datauppsättning, aktivitet, pipeline och länkad tjänst

## <a name="supported-regions"></a>Regioner som stöds
För närvarande kan du skapa datafabriker i regionerna **västra USA**, **östra USA** och **Norra Europa**. Dock kan en datafabrik ha åtkomst till datalager och beräkna tjänster i andra Azure-regioner för att flytta data mellan datalager eller bearbeta data med hjälp av beräkningstjänster.

Azure Data Factory lagrar inte själv några data. Du kan använda den för att skapa datadrivna arbetsflöden som samordnar flödet av data mellan [datalager som stöds](#data-movement-activities) och bearbetning av data med hjälp av [beräkningstjänster](#data-transformation-activities) i andra regioner eller i en lokal miljö. Du kan också [övervaka och hantera arbetsflöden](data-factory-monitor-manage-pipelines.md) med både program- och användargränssnittsmetoder.

Även om Data Factory bara finns i regionerna **USA, västra**, **USA, östra** och **Europa, norra**, är dataförflyttning i Data Factory tillgängligt [globalt](data-factory-data-movement-activities.md#global) i flera regioner. Om ett datalager finns bakom en brandvägg kan en [gateway för datahantering](data-factory-move-data-between-onprem-and-cloud.md) som installerats i din lokala miljö flytta data i stället.

Exempelvis kan vi anta att dina beräkningsmiljöer, som t.ex. Azure HDInsight-kluster och Azure Machine Learning, körs utanför Västeuropas region. Du kan skapa och använda en Azure Data Factory-instans i norra Europa och använda den för att schemalägga jobb i dina beräkningsmiljöer i Västeuropa. Det tar några millisekunder för Data Factory att utlösa jobbet i din beräkningsmiljö, men den tid det tar för att köra jobbet ändras inte.

## <a name="get-started-with-creating-a-pipeline"></a>Kom igång med att skapa en pipeline
Du kan använda något av dessa verktyg eller API:er för att skapa datapipelines i Azure Data Factory: 

- Azure Portal
- Visual Studio
- PowerShell
- .NET-API
- REST API
- Azure Resource Manager-mall. 

Följ de stegvisa anvisningarna i följande självstudier om du vill lära dig hur du skapar datafabriker med datapipeliner:

| Självstudier | Beskrivning |
| --- | --- |
| [Flytta data mellan två molndatalager](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |I de här självstudierna skapar du en datafabrik med en pipeline som **flyttar data** från Blob Storage till SQL Database. |
| [Omvandla data med Hadoop-kluster](data-factory-build-your-first-pipeline.md) |I de här självstudierna skapar du din första Azure Data Factory med en datapipeline som **bearbetar data** genom att köra Hive-skriptet på ett Azure HDInsight-kluster (Hadoop). |
| [Flytta data mellan ett lokalt datalager och ett molndatalager med hjälp av Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) |I de här självstudierna skapar du en datafabrik med en pipeline som **flyttar data** från en **lokal** SQL Server-databas till en Azure-blob. Som en del av den här genomgången kommer du att installera och konfigurera Data Management Gateway på din dator. |
