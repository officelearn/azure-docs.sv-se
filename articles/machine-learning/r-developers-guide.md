---
title: R utvecklarens guide till Azure - R-programmering | Microsoft Docs
description: Den här artikeln innehåller en översikt över de olika sätten att dataforskare kan utnyttja sina befintliga kompetenser med R programmeringsspråket i Azure. Azure erbjuder många tjänster som R-utvecklare kan använda för att utöka sina data science-arbetsbelastningar till molnet.
services: machine-learning
documentationcenter: ''
author: AnalyticJeremy
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: R
ms.topic: article
ms.date: 09/12/2018
ms.author: jepeach
ms.openlocfilehash: 102191b885d2a4a9234b7783b0a51b09903d3abd
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54807464"
---
# <a name="r-developers-guide-to-azure"></a>R utvecklarens guide till Azure
<img src="media/r-developers-guide/logo_r.svg" alt="R logo" align="right" width="200" />

Många dataanalytiker som hanterar allt större mängder data som söker efter sätt att dra nytta av molnbaserad databehandling för sina analyser.  Den här artikeln innehåller en översikt över de olika sätt som datatekniker kan använda sina befintliga kompetenser med den [R-programspråket](https://www.r-project.org) i Azure.

Microsoft har helt tog R-programspråket som ett första klassens verktyg för dataexperter.  Företaget är när dataexperter att utöka sina data science-arbetsbelastningar till molnet när du hanterar storskaliga projekt genom att tillhandahålla många olika alternativ för R-utvecklare att köra koden i Azure.

Låt oss nu undersöka de olika alternativen och de mest övertygande scenarierna för vart och ett.

## <a name="azure-services-with-r-language-support"></a>Azure-tjänster med stöd för R-språk
Den här artikeln beskriver följande Azure-tjänster som har stöd för R-språket:

|Tjänst                                                          |Beskrivning                                                                       |
|-----------------------------------------------------------------|----------------------------------------------------------------------------------|
|[Virtuell dator för datavetenskap](#data-science-virtual-machine)    |en anpassad virtuell dator att använda som en data science-dator eller en anpassad beräkningsmål|
|[ML-tjänster på HDInsight](#ml-services-on-hdinsight)            |kluster-dator för att köra R-analyser av stora datamängder över flera noder   |
|[Azure Databricks](#azure-databricks)                            |Spark samarbetsmiljö har stöd för R och andra språk               |
|[Azure Machine Learning Studio](#azure-machine-learning-studio)  |köra anpassade R-skript i Azure machine learning-experiment                      |
|[Azure Batch](#azure-batch)                                      |erbjuder en mängd olika alternativ för att köra ekonomiskt R-kod på flera noder i ett kluster|
|[Azure Notebooks](#azure-notebooks)                              |en kostnadsfri molnbaserad version av Jupyter-anteckningsböcker                  |
|[Azure SQL Database](#azure-sql-database)                        |köra R-skript i SQL Server-databasmotorn                            |

## <a name="data-science-virtual-machine"></a>Virtuell dator för datavetenskap
Den [Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) (DSVM) är en anpassad VM-avbildning i Azure Microsofts molnplattform som skapats specifikt för datavetenskap. Den har många populära verktyg för datavetenskap, inklusive:
* [Microsoft R Open](https://mran.microsoft.com/open/)
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)
* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop)
* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server)

DSVM kan etableras med antingen Windows eller Linux som operativsystem.  Du kan använda DSVM på två olika sätt: som en interaktiv arbetsstation eller som en plattform för databearbetning för ett anpassade kluster.

### <a name="as-a-workstation"></a>Som en arbetsstation
Om du vill komma igång med R i molnet snabbt och enkelt är det bästa alternativet.  Miljön är välkända för alla som har arbetat med R på en lokal arbetsstation.  Istället för att använda lokala resurser, är det dock R-miljö som körs på en virtuell dator i molnet.  Om dina data lagras redan i Azure, har den ytterligare fördelen med att tillåta R-skript att köra ”närmare data”. Data kan nås via Azures interna nätverket, vilket ger mycket snabbare åtkomsttider i stället för att överföra data via Internet.

DSVM kan vara särskilt användbart för små grupper med R-utvecklare.  Varje utvecklare kan i stället för investeringar i kraftfulla arbetsstationer för varje utvecklare och som kräver teammedlemmar möjlighet att synkronisera på vilka versioner av de olika programvarupaket som de ska använda, skapa en instans av DSVM när så krävs.

### <a name="as-a-compute-platform"></a>Som en plattform för databearbetning
Förutom som används som arbetsstation, används också DSVM som ett Elastiskt skalbara beräknings-plattform för R-projekt.  Med hjälp av den <code>[AzureDSVM](https://github.com/Azure/AzureDSVM)</code> R-paket, du kan styra skapandet och borttagningen av DSVM-instanser.  Du kan skapa instanser i ett kluster och distribuera en distribuerad analys som ska utföras i molnet.  Den här processen kan styras av R-kod som körs på den lokala arbetsstationen.

Mer information om DSVM finns i [”introduktion till Azure Data Science Virtual Machine för Linux och Windows”.](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)

## <a name="ml-services-on-hdinsight"></a>ML Services på HDInsight
[Microsoft ML Services](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-overview) ger dataforskare, statistiker och R-programmerare med åtkomst på begäran till skalbara och distribuerade analysmetoder på HDInsight.  Den här lösningen ger de senaste funktionerna för R-baserad analys på datauppsättningar med valfri storlek lästes in i Azure Blob eller Data Lake storage.

Det här är en lösning i företagsklass som gör det möjligt att skala din R-kod i ett kluster.  Genom att utnyttja funktionerna i Microsofts
<code>[RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)</code> paketet, R-skript på HDInsight kan köra databearbetning functions parallellt på flera noder i ett kluster.  Detta gör att R kan bearbeta data på en mycket större skala än vad som är möjligt med R-enkla trådar som körs på en arbetsstation.

Den här möjligheten att skala gör ML-tjänster på HDInsight ett bra alternativ för R-utvecklare med enorma datauppsättningar.  Det ger en flexibel och skalbar plattform för att köra R-skript i molnet.

En genomgång om hur du skapar ett kluster med ML-tjänster finns det [”Kom igång med ML-tjänster på Azure HDInsight”](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-get-started) artikeln.

## <a name="azure-databricks"></a>Azure Databricks
[Azure Databricks](https://azure.microsoft.com/services/databricks/) är en Apache Spark-baserad analysplattform som är optimerad för Microsoft Azures plattform för molntjänster.  Databricks har utformats med grundarna av Apache Spark och är integrerat med Azure för att tillhandahålla konfiguration med ett klick, effektiva arbetsflöden och en interaktiv arbetsyta som möjliggör samarbete mellan dataanalytiker, dataingenjörer och affärsanalytiker.

Samarbete i Databricks aktiveras av plattformens notebook system.  Användare kan skapa, dela och redigera anteckningsböcker med andra användare av systemen.  Dessa anteckningsböcker Tillåt användare att skriva kod som körs mot Spark-kluster som hanteras i Databricks-miljö.  Dessa anteckningsböcker fullständigt stöd för R och ge användare åtkomst till Spark via både den `SparkR` och `sparklyr` paket.

Eftersom Databricks bygger på Spark och fokuserar på samarbete, används plattformen ofta av för datautvecklarteam som fungerar tillsammans på komplexa analyser av stora datauppsättningar.  Eftersom anteckningsböcker i Databricks stöd för andra språk utöver R, är det särskilt användbart för team där analytiker använder olika språk för sitt primära arbete.

Artikeln [”vad är Azure Databricks”?](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks)
kan ge mer information om plattformen och hjälp att komma igång.

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[Azure Machine Learning Studio](https://azure.microsoft.com/services/machine-learning-studio/) är ett där flera användare kan dra och släpp verktyg som du kan använda för att bygga, testa och distribuera prediktiva Analyslösningar i molnet.  Det gör att nya dataexperter att skapa och distribuera maskininlärningsmodeller utan att behöva skriva mycket kod.

ML Studio har stöd för både R och Python.  Du kan använda R med ML Studio på två sätt.

### <a name="custom-r-scripts-in-your-experiments"></a>Anpassade R-skript i dina experiment
Först måste utöka du datamodifieringar och machine learning-funktionerna i ML Studio genom att skriva anpassade R-skript.
Även om ML Studio innehåller en mängd olika moduler för att förbereda och analysera data, går inte att det matcha funktionerna i en mogen språk som R.  Därför att tjänsten har utformats för att presentera dina egna anpassade R-skript i fall där de angivna modulerna inte uppfyller dina behov.

Om du vill använda den här funktionen, dra och släpp en ”kör R-skript”-modul i experimentet.  Använd sedan Kodredigeraren i fönstret ”Egenskaper” att skriva ett nytt R-skript eller klistra in ett befintligt skript.  Du kan referera till externa R-paket från skriptet.  Du kan använda skriptet att manipulera data eller för att skapa komplexa ML-modeller som inte är en del av standardbibliotek för ML Studio-modell.

En mer omfattande introduktion om att använda R i ML Studio-experiment, ta en titt på [”snabbsjälvstudien till r-programmeringsspråket för Azure Machine Learning”.](https://docs.microsoft.com/azure/machine-learning/studio/r-quickstart)

### <a name="create-manage-and-deploy-experiments-from-your-local-r-environment"></a>Skapa, hantera och distribuera experiment från den lokala R-miljön
Det andra sättet att du kan använda R med ML Studio är att använda den
<code>[AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html)</code> paket och övervaka och kontrollera hur experiment med R-programmeringsmiljö.  Det här paketet som underhålls av Microsoft, kan du ladda upp och ned datauppsättningar till och från Azure ML att förfråga experiment, om du vill publicera R-funktioner som Azure ML-webbtjänster, och köra R-data via befintliga webbtjänster och hämta utdata.

Det här paketet gör det mycket enklare att använda Azure ML som en plattform för skalbar distribution för din R-kod.  I stället för att klicka och dra i Användargränssnittet, kan du automatisera hela distributionsprocessen med hjälp av verktyg du redan känner till.

## <a name="azure-batch"></a>Azure Batch
Du kan använda för storskaliga R-jobb [Azure Batch](https://azure.microsoft.com/services/batch/).  Den här tjänsten tillhandahåller jobbet jobbschemaläggning och beräkningshantering i molnskala så att du kan skala din R-arbetsbelastning till tiotals, hundratals eller tusentals virtuella datorer.  Eftersom det är en generaliserad databehandlingsplattform alternativ några det för att köra R-jobb på Azure Batch.

Ett alternativ är att använda Microsofts <code>[doAzureParallel](https://github.com/Azure/doAzureParallel)</code> paketet.  Det här R-paketet är en parallell serverdel för den `foreach` paketet.  Det gör att varje iteration av den `foreach` loop parallell körning på en nod i klustret för Azure Batch.  En introduktion till paketet, kan du läsa den [”doAzureParallel: Dra nytta av Azures flexibla beräkning direkt från din R-session ”](https://azure.microsoft.com/blog/doazureparallel/) blogginlägg.

Ett annat alternativ för att köra ett R-skript i Azure Batch är att paketera din kod med ”RScript.exe” som en Batch-App i Azure-portalen.  En detaljerad genomgång finns [”R arbetsbelastningar på Azure Batch”.](https://azure.microsoft.com/blog/r-workloads-on-azure-batch/)

Ett tredje alternativ är att använda den [Azure distribuerade Data Engineering Toolkit](https://github.com/Azure/aztk) (AZTK), vilket gör att du kan etablera på begäran Spark-kluster med Docker-behållare i Azure Batch.  Detta ger ett ekonomiskt sätt att köra Spark-jobb i Azure.  Med hjälp av [SparklyR med AZTK](https://github.com/Azure/aztk/wiki/SparklyR-on-Azure-with-AZTK), R-skript kan skaländras ut i molnet enkelt och ekonomiskt.

## <a name="azure-notebooks"></a>Azure Notebooks

[Azure anteckningsböcker](https://notebooks.azure.com) är en låg kostnad, smidig metod för R-utvecklare som föredrar att arbeta med bärbara datorer att ta med sin kod till Azure.  Det är en kostnadsfri tjänst för alla att utveckla och köra kod i sin webbläsare med hjälp av [Jupyter](https://jupyter.org/), vilket är ett projekt med öppen källkod som gör att nyhetsnotiser genomsöks markdown prose och körbar kod grafik på en enda arbetsyta.

Kostnadsfria tjänstenivå för Azure-datorer är ett genomförbart alternativ för småskalig projekt, eftersom det begränsar varje notebook processen till 4GB minne och 1GB datauppsättningar. Om du behöver beräknings- och power utöver dessa begränsningar, men kan du köra anteckningsböcker i en Data Science Virtual Machine-instans. Mer information finns i [hantera och konfigurera Azure anteckningsböcker projekt - Beräkningsnivån](/azure/notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

## <a name="azure-sql-database"></a>Azure SQL Database
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) är databastjänst för Microsofts intelligent, fullt hanterad relationsdatabastjänst i molnet.  På så sätt kan du använda den fulla kraften hos SQL Server utan någon besväret med att konfigurera infrastrukturen.  Detta inkluderar [Maskininlärningstjänster](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning?view=sql-server-2017), vilket är en av de nyare tillägg till SQL-tjänsten.

Den här funktionen erbjuder en inbäddad, förutsägande analys och data science motor som kan köra R-kod i en SQL Server-databas som lagrade procedurer, T-SQL-skript som innehåller R-instruktioner eller R-kod som innehåller T-SQL.  I stället för att extrahera data från databasen och läser in dem i R-miljön, att läsa in din R-kod direkt i databasen och låt den köras så fort tillsammans med data.

Machine Learning Services är en del av en lokal SQL Server sedan 2016, är det relativt nya för Azure SQL Database.  Det är för närvarande i [begränsad förhandsversion](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#azure-sql-database-roadmap) men fortsätter att utvecklas.


### <a name="next-steps"></a>Nästa steg
* [Köra R-kod på Azure med mrsdeploy](http://blog.revolutionanalytics.com/2017/03/running-your-r-code-azure.html)
* [Machine Learning Server i molnet](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-in-the-cloud)
* [Ytterligare resurser för Machine Learning Server och Microsoft R](https://docs.microsoft.com/machine-learning-server/resources-more)
* [R på Azure](https://github.com/yueguoguo/r-on-azure) – en översikt av paket och verktyg för att använda R med Azure-fallstudier

---

<sub>R-logotypen är &copy; 2016 The R Foundation och används i enlighet med den [Creative Commons Attribution-ShareAlike 4.0 International licens](https://creativecommons.org/licenses/by-sa/4.0/).</sub>
