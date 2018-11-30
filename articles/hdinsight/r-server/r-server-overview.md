---
title: Introduktion till ML-tjänster på Azure HDInsight
description: Lär dig hur du använder ML-tjänster på HDInsight för att skapa program för stordataanalys.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 1f53d87d6de76c0ff6954c9bd38d95e05981d822
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52499473"
---
# <a name="introduction-to-ml-services-and-open-source-r-capabilities-on-hdinsight"></a>Introduktion till ML-tjänster och funktioner för öppen källkod-R på HDInsight

> [!NOTE]
> I September 2017 Microsoft R Server släpptes under det nya namnet på **Microsoft Machine Learning Server** eller ML Server. Därför kallas nu R Server-kluster i HDInsight **Maskininlärningstjänster** eller **ML tjänster** -kluster i HDInsight. Mer information om ändringen av R Server finns i [Microsoft R Server är nu Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/rebranding-microsoft-r-server#get-support-for-r-server).

Microsoft Machine Learning Server är tillgänglig som ett alternativ för distribution när du skapar HDInsight-kluster i Azure. Den typ av kluster som innehåller det här alternativet kallas **ML Services**. Den här funktionen ger dataforskare, statistiker och R-programmerare med åtkomst på begäran till skalbara och distribuerade analysmetoder på HDInsight.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

ML-tjänster på HDInsight innehåller de senaste funktionerna för R-baserad analys på datauppsättningar med valfri storlek lästes in i Azure Blob eller Data Lake storage. Eftersom ML tjänster kluster bygger på öppen källkod R, kan de R-baserade program som du skapar utnyttja 8000 + open source-R-paket. Rutiner i ScaleR paket för Microsofts big data analytics är också tillgängliga.

Edge-nod i ett kluster är ett bra ställe att ansluta till klustret och köra R-skript. Med en kantnod har alternativet att köra parallelliserad distribuerade funktionerna i ScaleR över kärnor i noden gränsservern. Du kan också köra dem mellan noderna i klustret med hjälp av Scaler's Hadoop Map Reduce eller Apache Spark-beräkningskontext.

Du kan hämta modeller eller förutsägelser som uppstår vid analys för lokal användning. De kan också ska operationaliseras någon annanstans i Azure, i synnerhet via [Azure Machine Learning Studio](http://studio.azureml.net) [webbtjänsten](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Kom igång med ML-tjänster på HDInsight

För att skapa en ML-Services-kluster i Azure HDInsight, Välj den **ML tjänster** typ av kluster när du skapar ett HDInsight-kluster med Azure portal. Klustertypen ML-tjänster omfattar ML Server på datanoder i klustret och på en kantnod, vilket fungerar som en startsida zon för ML-Services-baserad analys. Se [komma igång med ML-tjänster på HDInsight](r-server-get-started.md) stegvisa anvisningar för hur du skapar klustret.

## <a name="why-choose-ml-services-in-hdinsight"></a>Varför välja ML-tjänster i HDInsight?

ML-tjänster i HDInsight har följande fördelar:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>AI-innovationer från Microsoft och öppen källkod

  ML-tjänster omfattar mycket skalbar och distribuerad uppsättning algoritmer som [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package), och [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) som kan arbeta med data som är större än den mängden fysiskt minne, och kan köras på en mängd olika plattformar i en distribuerad sätt. Lär dig mer om insamling av Microsoft azures anpassade [R-paket](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) och [Python-paket](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) medföljer produkten.
  
  ML-tjänster få dessa Microsofts senaste innovationer och bidrag som kommer från diskussionsgruppen med öppen källkod (R, Python och AI-verktyg) allt på en enda företagsklass-plattformen. Paketinformation R eller Python maskininlärning med öppen källkod kan du arbeta sida vid sida med alla upphovsrättsskyddad innovationer från Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Enkel, säker och mycket skalbar driftsättning och administration

  Företag som förlitar sig på traditionella paradigm och miljöer investera mycket tid och arbete för driftsättning. Detta resulterar i luftfyllt kostnader och fördröjer inklusive translation tills modeller, iterationer så att de är giltiga och aktuella, föreskrifter godkännande och hantera behörigheter via driftsättningen.

  ML-tjänster erbjuder företagsklass [driftsättning](https://docs.microsoft.com/machine-learning-server/what-is-operationalization), i att när en maskininlärningsmodell har slutförts, det tar bara några klick för att generera webbtjänster API: er. Dessa [webbtjänster](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) finns på en server rutnätet i molnet och kan integreras med line-of-business-program. Möjligheten att distribuera till en elastisk grid kan du skala smidigt med behov för företaget, både för batch och bedömning i realtid. Anvisningar finns i [Operationalisera ML-tjänster på HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>Viktiga funktioner i ML-tjänster på HDInsight

Följande funktioner ingår i ML-tjänster på HDInsight.

| Funktionskategori | Beskrivning |
|------------------|-------------|
| R-aktiverad | [R-paket](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) för lösningar som är skrivna i R, med en öppen källkod fördelning av R- och körning infrastrukturen för körning av skript. |
| Python-aktiverad | [Python-moduler](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) för lösningar som skrivits i Python med en öppen källkod fördelning av Python och körning infrastrukturen för körning av skript.
| [Förtränade modeller](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | För visuella analyser och textkänsloanalys, redo att bedöma de data du anger. |
| [Distribuera och använda](r-server-operationalize.md) | Utföra åtgärder för din server och distribuera lösningar som en webbtjänst. |
| [Fjärrkörning](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Starta fjärrsessioner på ML Services-kluster i ditt nätverk från din arbetsstation för klienten. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Alternativen för datalagring för ML-tjänster på HDInsight

Standardlagring för HDFS-filsystemets av HDInsight-kluster kan associeras med ett Azure Storage-konto eller ett Azure Data Lake Store. Den här associationen säkerställer att data har överförts till klustret lagring under analysen görs beständiga och data är tillgängliga även när klustret tas bort. Det finns olika verktyg för hantering av dataöverföring till lagringsalternativ du väljer, inklusive funktionen portalbaserad uppladdning av storage-konto och [AzCopy](../../storage/common/storage-use-azcopy.md) verktyget.

Har du möjlighet att bevilja åtkomst till ytterligare Blob och Data lake lagras under klustret etableringsprocessen oavsett alternativet primär lagring som används. Se [komma igång med ML-tjänster på HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) information om hur du lägger till åtkomst till ytterligare konton. Se [alternativ för Azure Storage för ML-tjänster på HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) du lär dig mer om hur du använder flera lagringskonton.

Du kan också använda [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) som ett lagringsalternativ för användning på gränsnoden. Azure Files kan du montera en filresurs som har skapats i Azure Storage till Linux-filsystem. Mer information om dessa alternativ för lagring av data för ML-tjänster på HDInsight-kluster finns i [alternativ för Azure Storage för ML-tjänster på HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Kantnod ML-tjänster för åtkomst

Du kan ansluta till Microsoft ML Server på gränsnoden med en webbläsare. Den installeras som standard när klustret skapas. Mer information finns i [hämta stared ML-tjänster på HDInsight](r-server-get-started.md). Du kan också ansluta till en klustergränsnoden från kommandoraden med hjälp av SSH/PuTTY åtkomst till R-konsolen.

## <a name="develop-and-run-r-scripts"></a>Utveckla och köra R-skript

R-skript som du skapar och kör kan använda någon av 8000 + open source-R-paketen utöver de parallelliserad och distribuerade rutinerna som är tillgängliga i ScaleR-biblioteket. I allmänhet körs ett skript som körs med ML-tjänster på gränsnoden inom interpret R på noden. Undantagen är de steg som måste anropa en ScaleR-funktion med en beräkningskontext som har angetts till Hadoop Map Reduce (RxHadoopMR) eller Spark (RxSpark). I det här fallet körs funktionen på ett sätt som är distribuerade över dessa (aktivitet) datanoder i klustret som är associerade med de data som refererar till. Mer information om de olika alternativ för beräkningskontexter finns i [alternativ för beräkningskontexter för ML-tjänster på HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operationalisera en modell

När datamodelleringen är klar kan driftsätta du modellen för att göra förutsägelser för nya data från Azure eller lokalt. Den här processen kallas bedömning. Bedömning kan göras i HDInsight, Azure Machine Learning eller lokalt.

### <a name="score-in-hdinsight"></a>Poängen på HDInsight

Skriva ett R-funktion som anropar din modell för att göra förutsägelser för en ny datafil som du har läst in till ditt lagringskonto för att bedöma i HDInsight. Spara sedan förutsägelserna tillbaka till lagringskontot. Du kan köra den här rutinunderhåll på begäran på gränsnoden för ditt kluster eller med hjälp av ett schemalagt jobb.

### <a name="score-in-azure-machine-learning-aml"></a>Poäng i Azure Machine Learning (AML)

För att bedöma med hjälp av Azure Machine Learning, använder du open source-Azure Machine Learning R-paketet kallas [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) att publicera din modell som en Azure-webbtjänst. För att underlätta för är detta paket förinstallerade på gränsnoden. Sedan använder anläggningarna i Azure Machine Learning för att skapa ett användargränssnitt för webbtjänsten och sedan anropa webbtjänsten som behövs för bedömning.

Om du väljer det här alternativet måste du omvandla valfri ScaleR modellobjekt till motsvarande öppen källkod modellobjekt för användning med webbtjänsten. Använd tvång förutom scaler, som `as.randomForest()` för ensemble-baserade modellen för den här konverteringen.

### <a name="score-on-premises"></a>Poäng på plats

För att bedöma lokala när du har skapat din modell du serialisera modellen i R, ladda ned det, deserialisera det och sedan använda den för bedömning av nya data. Du kan samla in nya data genom att använda den metod som beskrivs tidigare i [poängen på HDInsight](#score-in-hdinsight) eller genom att använda [webbtjänster](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Underhålla klustret

### <a name="install-and-maintain-r-packages"></a>Installera och underhålla R-paket

De flesta av de R-paket som du använder krävs eftersom de flesta stegen R-skript körs det på gränsnoden. Om du vill installera ytterligare R-paket på kantnoden kan du använda den `install.packages()` -metod i R.

Om du bara använder rutiner från ScaleR-biblioteket i klustret kan behöver du vanligtvis inte installera ytterligare R-paket på datanoder. Men du kanske måste ytterligare paket för att ge stöd åt **rxExec** eller **RxDataStep** körning på datanoder.

I sådana fall kan ytterligare paket installeras med en skriptåtgärd när du har skapat klustret. Mer information finns i [hantera ML-tjänster i HDInsight-kluster](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Ändra inställningar för Apache Hadoop MapReduce-minne

Ett kluster kan ändras om du vill ändra mängden minne som är tillgängliga för ML-tjänster när den körs ett MapReduce-jobb. Använd Apache Ambari UI som är tillgänglig via bladet för Azure portal för klustret om du vill ändra ett kluster. Anvisningar om hur du kommer åt Ambari UI för klustret finns i [hantera HDInsight-kluster med Ambari-Webbgränssnittet](../hdinsight-hadoop-manage-ambari.md).

Det är också möjligt att ändra mängden minne som är tillgängliga för ML-tjänster med hjälp av Hadoop-växlar i anropet till **RxHadoopMR** på följande sätt:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Skala ditt kluster

Ett befintligt ML-Services-kluster i HDInsight kan skalas upp eller ned via portalen. Du kan få den ytterligare kapaciteten som du kan behöva för större uppgifter genom att skala upp eller du kan skala tillbaka ett kluster när det är inaktiv. Anvisningar om hur du skalar ett kluster finns i [hantera HDInsight-kluster](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Underhålla systemet

Underhåll att tillämpa korrigeringsfiler för operativsystem och andra uppdateringar utförs på den underliggande virtuella Linux-datorer i ett HDInsight-kluster under kontorstid. Normalt görs Underhåll kl. 3:30 (baserat på den lokala tiden för den virtuella datorn) varje måndag och torsdag. Uppdateringarna utförs så att de inte påverkar mer än ett kvartal i klustret vid ett tillfälle.

Eftersom huvudnoderna är redundant och inte alla datanoder som påverkas, kan alla jobb som körs under den här tiden sakta ned. De bör dock fortfarande köra kan slutföras. Alla anpassade program och lokala data som du har bevaras mellan dessa underhållshändelser, såvida inte ett oåterkalleligt fel inträffar som kräver att klustret.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-alternativ för ML-tjänster på HDInsight

Linux-gränsnoden för ett HDInsight-kluster är zonen startsida för R-baserad analys. De senaste versionerna av HDInsight för att ange en standardinstallation av RStudio Server på gränsnoden som en webbläsarbaserade IDE. Användningen av RStudio Server som en IDE-miljö för utveckling och körning av R-skript kan vara betydligt mer produktiv än att bara använda R-konsolen.

Dessutom kan du installera en skrivbords-IDE och använda den för att få åtkomst till klustret med hjälp av en fjärransluten MapReduce eller Spark-beräkningskontext. Alternativ inkluderar Microsofts [R Tools för Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS), RStudio och Walware är Eclipse-baserade [StatET](http://www.walware.de/goto/statet).

Dessutom kan du öppna R-konsolen på gränsnoden genom att skriva **R** Linux Kommandotolken när du har anslutit via SSH eller PuTTY. När gränssnittet för konsolen, är det praktiskt att köra en textredigerare för utveckling av R-skript i ett annat fönster och klipp ut och klistra in delar av skriptet i R-konsolen efter behov.

## <a name="pricing"></a>Prissättning

De priser som är kopplade till en ML-tjänster HDInsight-kluster är strukturerade på samma sätt för priserna för andra typer av HDInsight-kluster. De är baserade på storlek för de underliggande virtuella datorerna mellan namn, data och edge-noder, med hjälp av en kärntimme höjning. Mer information finns i [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder ML-tjänster på HDInsight-kluster finns i följande avsnitt:

* [Kom igång med ML-Services-kluster i HDInsight](r-server-get-started.md)
* [Alternativ för beräkningskontexter för ML-tjänstkluster i HDInsight](r-server-compute-contexts.md)
* [Lagringsalternativ för ML-Services-kluster i HDInsight](r-server-storage.md)
