---
title: Introduktion till ML-tjänster i Azure HDInsight | Microsoft Docs
description: Lär dig hur du använder ML-tjänster på HDInsight för att skapa program för stordataanalys.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 9633a7df1cb72f3e9e5ee79be0c332565e7e8f2a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054110"
---
# <a name="introduction-to-ml-services-and-open-source-r-capabilities-on-hdinsight"></a>Introduktion till ML-tjänster och funktioner för öppen källkod R i HDInsight

> [!NOTE]
> I September 2017 Microsoft R Server släpptes med det nya namnet för **Microsoft Machine Learning Server** eller ML-Server. Därför kallas nu R Server-kluster i HDInsight **Machine Learning Services** eller **ML Services** kluster i HDInsight. Mer information om ändringen av R Server finns [Microsoft R Server är nu Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/rebranding-microsoft-r-server#get-support-for-r-server).

Microsoft Machine Learning-servern är tillgänglig som ett distributionsalternativ när du skapar HDInsight-kluster i Azure. Den typ av kluster som innehåller det här alternativet kallas **ML Services**. Den här funktionen ger dataanalytiker, statistiker och R-programmerare på begäran åtkomst till skalbara, distribuerade metoder för analyser på HDInsight.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

ML-tjänster på HDInsight ger de senaste funktionerna för analys av R-baserade på datauppsättningar av valfri storlek som har lästs in till Azure Blob eller Data Lake lagringsutrymme. Eftersom ML Services klustret bygger på öppen källkod R kan R-baserade program som du skapar utnyttja 8000 + öppen källkod R-paket. Rutiner i ScaleR Microsofts big analytics datapaketet är också tillgängliga.

Edge-nod i ett kluster ger en lämplig plats att ansluta till klustret och köra R-skript. Med en kantnod har möjlighet att köra parallelized distribuerade funktioner ScaleR över kärnor på noden gränsservern. Du kan också köra dem mellan noder i klustret med hjälp av Scaler's Hadoop kartan minska eller compute kontexter för Spark.

Modeller eller förutsägelser som resulterar från analys kan hämtas för lokal användning. De kan också vara operationalized någon annanstans i Azure, i synnerhet via [Azure Machine Learning Studio](http://studio.azureml.net) [webbtjänsten](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Komma igång med ML Services på HDInsight

Om du vill skapa ett kluster ML tjänster i Azure HDInsight, Välj den **ML Services** kluster typ när du skapar ett HDInsight-kluster med Azure-portalen. Klustertyp ML Services innehåller ML-Server på datanoder i klustret och på en kantnod som fungerar som en stannplan för ML Services-baserade analytics. Se [komma igång med ML-tjänster på HDInsight](r-server-get-started.md) stegvisa anvisningar för hur du skapar klustret.

## <a name="why-choose-ml-services-in-hdinsight"></a>Varför ska jag välja ML tjänster i HDInsight?

Ml – tjänster i HDInsight ger följande fördelar:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>AI innovation från Microsoft och öppen källkod

  ML-tjänster omfattar skalbara, distribuerade uppsättning algoritmer som [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package), och [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) som kan arbeta med data är större än den storlek för fysiskt minne och kör på en mängd olika plattformar i en distribuerad sätt. Lär dig mer om insamling av Microsoft har anpassade [R-paket](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) och [Python-paket](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) medföljer produkten.
  
  Ml – tjänster få dessa Microsoft innovationer och avgifter som kommer från öppen källkod community (Verktyg R, Python och AI) alla ovanpå en enda företagsklass plattform. Alla R eller Python öppen källkod machine learning-paket kan arbeta sida vid sida med alla egna innovation från Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Enkel, säker och hög skalbarhet operationalization och administration

  Företag förlita dig på miljöer och traditionella böjningsmönster investera mycket tid och arbete mot operationalization. Detta resulterar i luftfyllda kostnader och fördröjningar inklusive översättning tills modeller, iterationer för att hålla dem giltig aktuella, regelverk godkännande och att hantera behörigheter via operationalization.

  Ml – tjänster erbjuder enterprise-klass [operationalization](https://docs.microsoft.com/machine-learning-server/what-is-operationalization), att när en modell för maskininlärning är klar tar bara några klickningar att generera webbtjänster API: er. Dessa [webbtjänster](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) finns i ett rutnät för server i molnet och kan integreras med line-of-business-program. Möjligheten att distribuera till en elastisk rutnätet kan du skala sömlöst med mot dina behov, både för batch- och realtid bedömningen. Instruktioner finns i [Operationalisera ML-tjänster på HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>Viktiga funktioner i ML-tjänster på HDInsight

Följande funktioner ingår i ML-tjänster på HDInsight.

| Funktionen kategori | Beskrivning |
|------------------|-------------|
| R-aktiverad | [R-paket](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) för lösningar som är skrivna i R, med en öppen källkod fördelning av R och körning infrastrukturen för körning av skript. |
| Python-aktiverad | [Python-moduler](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) för lösningar som skrivits i Python, med en öppen källkod fördelning av Python och körning infrastrukturen för körning av skript.
| [Före tränade modeller](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | För visuell analys och text sentiment analys redo att samla in data du anger. |
| [Distribuera och använda](r-server-operationalize.md) | Operationalisera din server och distribuera lösningar som en webbtjänst. |
| [Fjärrkörning](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Starta fjärrsessioner i klustret ML tjänster i nätverket från klientens arbetsstation. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Alternativen för datalagring för ML-tjänster på HDInsight

Standardlagring för HDFS filsystemet på HDInsight-kluster kan associeras med ett Azure Storage-konto eller ett Azure Data Lake Store. Den här associationen säkerställer att data överförs till klustret lagring under analysen görs beständiga och data är tillgängliga även när klustret har tagits bort. Det finns flera olika verktyg för att hantera dataöverföring till lagringsalternativ du väljer, inklusive portal-baserade överför användandet av lagringskontot och [AzCopy](../../storage/common/storage-use-azcopy.md) verktyget.

Har du möjlighet att aktivera åtkomst till ytterligare Blob och Data lake lagrar under klustret etableringsprocessen oavsett primära lagringsalternativ används. Se [komma igång med ML-tjänster på HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) information om att lägga till åtkomst till ytterligare konton. Se [Azure lagringsalternativ för ML-tjänster på HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) artikeln om du vill veta mer om hur du använder flera lagringskonton.

Du kan också använda [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) som ett lagringsalternativ för användning på kantnoden. Azure Files kan du montera en filresurs som skapats i Azure Storage till Linux-filsystem. Mer information om dessa alternativ för lagring av data för ML-tjänster på HDInsight-kluster finns [Azure lagringsalternativ för ML-tjänster på HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Åtkomst ML Services kantnod

Du kan ansluta till Microsoft ML Server på kantnod med en webbläsare. Den installeras som standard när klustret skapas. Mer information finns i [hämta stared ML-tjänster på HDInsight](r-server-get-started.md). Du kan också ansluta till klustret kantnod från kommandoraden med hjälp av SSH/PuTTY åtkomst till R-konsolen.

## <a name="develop-and-run-r-scripts"></a>Utveckla och köra R-skript

R-skript som du skapar och kör kan använda 8000 + öppen källkod R-paket förutom paralleliserad och distribuerade-rutiner finns i ScaleR-biblioteket. I allmänhet körs ett skript som körs med ML-tjänster på kantnoden inom R-tolken på noden. Undantagen är de steg som måste anropa en ScaleR funktion med en kontext för beräkning som har angetts till Hadoop kartan minska (RxHadoopMR) eller Spark (RxSpark). I det här fallet körs funktionen i ett distribuerat sätt över dessa (aktivitet) datanoder i klustret som är associerade med de data som refererar till. Mer information om alternativ för olika beräkning kontexten finns [Compute-kontexten alternativ för ML-tjänster på HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operationalisera en modell

När dina datamodellering är klar, kan du operationalisera modellen för att göra förutsägelser för nya data från Azure eller lokalt. Den här processen kallas bedömningen. Bedömningen kan göras i HDInsight, Azure Machine Learning eller lokalt.

### <a name="score-in-hdinsight"></a>Poäng i HDInsight

Skriva ett R-funktion som anropar din modell för att göra förutsägelser för en ny datafil som du har läst in till ditt lagringskonto för att poängsätta i HDInsight. Spara förutsägelser tillbaka till lagringskontot. Du kan köra den här rutinunderhåll på begäran på kantnod på klustret eller med hjälp av ett schemalagt jobb.

### <a name="score-in-azure-machine-learning-aml"></a>Poäng i Azure Maskininlärning (AML)

För att poängsätta med hjälp av Azure Machine Learning använder öppen källkod Azure Machine Learning R paketet kallas [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) att publicera din modell som Azure-webbtjänst. Det här paketet har förinstallerats kantnoden för enkelhetens skull. Sedan använda funktionerna i Azure Machine Learning för att skapa ett användargränssnitt för webbtjänsten och sedan anropa webbtjänsten som krävs för resultatfunktioner.

Om du väljer det här alternativet måste du konvertera alla ScaleR modellobjekt till motsvarande öppen källkod modellobjekt för användning med webbtjänsten. Använda ScaleR tvång funktioner, till exempel `as.randomForest()` för ensemble-baserade modeller för den här konverteringen.

### <a name="score-on-premises"></a>Poäng på lokalt

För att poängsätta lokalt när du har skapat din modell du serialisera modellen i R, ladda ned det, deserialisera den och sedan använda datorn för resultatfunktioner nya data. Du kan samla in nya data med hjälp av den metod som beskrivs tidigare i [Resultatfunktioner i HDInsight](#scoring-in-hdinsight) eller genom att använda [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Underhåll klustret

### <a name="install-and-maintain-r-packages"></a>Installera och underhålla R-paket

De flesta av de R-paket som du använder krävs på noden edge eftersom de flesta stegen för din R-skript körs det. Du kan använda för att installera ytterligare R-paket på kantnoden på `install.packages()` metod i R.

Om du bara använder rutiner från biblioteket ScaleR hela klustret kan behöver du vanligtvis inte installera ytterligare R-paket på datanoder. Du kan dock behöva ytterligare paket för att ge stöd åt **rxExec** eller **RxDataStep** körning på datanoder.

I sådana fall kan ytterligare paket installeras med en skriptåtgärd när du har skapat klustret. Mer information finns i [ML hantera tjänster i HDInsight-kluster](r-server-hdinsight-manage.md).

### <a name="change-hadoop-mapreduce-memory-settings"></a>Ändra inställningarna för Hadoop-MapReduce-minne

Ett kluster kan ändras om du vill ändra mängden minne som är tillgängligt för ML tjänster när det körs ett MapReduce-jobb. Om du vill ändra ett kluster, använda Apache Ambari-användargränssnitt som är tillgängliga via Azure portalbladet för klustret. Instruktioner om hur du kommer åt Ambari UI för klustret, se [hantera HDInsight-kluster med Ambari-Webbgränssnittet](../hdinsight-hadoop-manage-ambari.md).

Det är också möjligt att ändra mängden minne som är tillgängligt för ML-tjänster med hjälp av Hadoop-växlar i anropet till **RxHadoopMR** på följande sätt:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Skala ditt kluster

Ett befintligt ML Services-kluster i HDInsight kan skalas upp eller ned via portalen. Du får ytterligare kapacitet som kan behövas för större bearbetningar genom att skala eller du kan skala tillbaka ett kluster när den är inaktiv. Instruktioner om hur du skalar ett kluster, se [hantera HDInsight-kluster](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Underhåll av systemet

Underhåll tillämpa operativsystemskorrigeringar och andra uppdateringar utförs på den underliggande virtuella Linux-datorer i ett HDInsight-kluster kontorstid. Normalt görs Underhåll på 3:30 AM (baserat på den lokala tiden för den virtuella datorn) varje måndag och torsdag. Uppdateringar utförs så att de inte påverkar mer än ett kvartal i klustret samtidigt.

Eftersom huvudnoderna är redundant och påverkas inte alla datanoder, kan alla jobb som körs under den här tiden långsammare. De bör dock fortfarande köra kan slutföras. Anpassade programvara eller lokala data som du har bevaras över händelserna underhåll om ett oåterkalleligt fel inträffar som kräver att klustret.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-alternativ för ML-tjänster på HDInsight

Linux-kantnod för ett HDInsight-kluster är stannplan för R-baserade analys. Nya versioner av HDInsight ger en standardinstallation av RStudio Server på kantnoden som en webbläsarbaserad IDE. Användning av RStudio Server som IDE-miljö för utveckling och körning av R-skript kan vara betydligt mer produktiv än att bara använda R-konsolen.

Du kan dessutom installera desktop IDE och använda den för att få åtkomst till klustret med hjälp av en fjärransluten MapReduce eller Spark beräknings-kontext. Alternativen är Microsofts [R Tools för Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS) RStudio och Walware datorns Eclipse-baserade [StatET](http://www.walware.de/goto/statet).

Dessutom kan du öppna R-konsolen på noden edge genom att skriva **R** Linux Kommandotolken efter anslutning via SSH eller PuTTY. När gränssnittet i konsolen, är det praktiskt att köra en textredigerare för utveckling av R-skript i ett annat fönster och klippa och klistra in avsnitt i ditt skript i konsolen R efter behov.

## <a name="pricing"></a>Prissättning

De priser som är associerade med ett ML Services HDInsight-kluster är strukturerade på samma sätt priser för andra typer av HDInsight-kluster. De är baserade på storleksändring av de underliggande virtuella datorerna över namn, data och edge noder med att lägga till en timme core-höjning. Mer information finns i [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder ML-tjänster på HDInsight-kluster finns i följande avsnitt:

* [Kom igång med ML Serices kluster i HDInsight](r-server-get-started.md)
* [Compute-kontexten alternativ för ML Services kluster i HDInsight](r-server-compute-contexts.md)
* [Lagringsalternativ för ML Services-kluster i HDInsight](r-server-storage.md)