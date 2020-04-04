---
title: Introduktion till ML Services på Azure HDInsight
description: Lär dig hur du använder ML Services på HDInsight för att skapa program för stordataanalys.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 04/03/2020
ms.openlocfilehash: 5bf405840de54c4e2399ee73e723201acca9e6bc
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657037"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Vad är ML-tjänster i Azure HDInsight

Microsoft Machine Learning Server är tillgängligt som ett distributionsalternativ när du skapar HDInsight-kluster i Azure. Klustertypen som tillhandahåller det här alternativet kallas **ML Services**. Den här funktionen ger åtkomst på begäran till anpassningsbara, distribuerade analysmetoder på HDInsight.

ML Services på HDInsight ger de senaste funktionerna för R-baserad analys av datauppsättningar av praktiskt taget alla storlekar. Datauppsättningarna kan läsas in till antingen Azure Blob eller Data Lake-lagring. Dina R-baserade program kan använda 8000+ R-paket med öppen källkod. Rutinerna i ScaleR, Microsofts stordataanalyspaket finns också tillgängliga.

Kantnoden är en bekväm plats att ansluta till klustret och köra dina R-skript. Kantnoden gör det möjligt att köra ScaleR-parallelliserade distribuerade funktioner över serverns kärnor. Du kan också köra dem över noderna i klustret med hjälp av ScaleR:s Hadoop-karta minska. Du kan också använda Apache Spark-beräkningskontexter.

De modeller eller förutsägelser som resultat av analys kan laddas ner för lokal användning. De kan också användas på andra ställen i Azure. I synnerhet via [Azure Machine Learning Studio (klassisk)](https://studio.azureml.net)och [webbtjänst](../../machine-learning/studio/deploy-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Komma igång med ML Services på HDInsight

Om du vill skapa ett ML Services-kluster i HDInsight väljer du **klustertypen ML Services.** Ml Services-klustertypen innehåller ML Server på datanoderna och kantnoden. Kantnoden fungerar som en landningszon för ML Services-baserad analys. Se [Skapa Apache Hadoop-kluster med Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md) för en genomgång om hur du skapar klustret.

## <a name="why-choose-ml-services-in-hdinsight"></a>Varför välja ML Services i HDInsight?

ML Services i HDInsight ger följande fördelar:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>AI-innovation från Microsoft och öppen källkod

  ML Services innehåller mycket anpassningsbar, distribuerad uppsättning algoritmer som [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)och [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package). Dessa algoritmer kan fungera på datastorlekar som är större än storleken på det fysiska minnet. De körs också på en mängd olika plattformar på ett distribuerat sätt. Läs mer om samlingen av Microsofts anpassade [R-paket](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) och [Python-paket](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) som ingår i produkten.
  
  ML Services överbryggar dessa Microsoft-innovationer och bidrag från verktygslådorna med öppen källkod (R-, Python- och AI-verktygslådor). Allt utöver en plattform i företagsklass. Alla R- eller Python-maskininlärningspaket med öppen källkod kan fungera sida vid sida med all egenutvecklad innovation från Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Enkel, säker och storskalig operationalisering och administration

  Företag som förlitar sig på traditionella paradigm och miljöer investerar mycket tid och ansträngning för operationalisering. Den här åtgärden resulterar i uppblåsta kostnader och förseningar, inklusive översättningstiden för: modeller, iterationer för att hålla dem giltiga och aktuella, myndighetsgodkännande och hantering av behörigheter.

  ML Services erbjuder [operationalisering i](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)företagsklass . När en maskininlärningsmodell är klar tar det bara några klick för att generera API:er för webbtjänster. Dessa [webbtjänster](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) finns på ett servernät i molnet och kan integreras med affärsprogram. Möjligheten att distribuera till ett elastiskt rutnät gör att du kan skala sömlöst med ditt företags behov, både för batch- och realtidsbedömning. Instruktioner finns i [Operationalize ML Services på HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> Ml Services-klustertypen på HDInsight stöds endast på HDInsight 3.6. HDInsight 3.6 är planerad att gå i pension den 31 december 2020.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Viktiga funktioner i ML Services på HDInsight

Följande funktioner ingår i ML Services på HDInsight.

| Funktionskategori | Beskrivning |
|------------------|-------------|
| R-aktiverad | [R-paket](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) för lösningar skrivna i R, med en distribution med öppen källkod av R och körningsinfrastruktur för skriptkörning. |
| Python-aktiverad | [Python-moduler](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) för lösningar skrivna i Python, med en distribution med öppen källkod av Python och körningsinfrastruktur för skriptkörning.
| [Förtränade modeller](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | För visuell analys och text sentimentanalys, redo att poäng data som du anger. |
| [Distribuera och förbruka](r-server-operationalize.md) | Operationalisera servern och distribuera lösningar som en webbtjänst. |
| [Fjärrkörning](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Starta fjärrsessioner i ML Services-klustret i nätverket från klientarbetsstationen. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Datalagringsalternativ för ML-tjänster på HDInsight

Standardlagring för HDFS-filsystemet kan vara ett Azure Storage-konto eller Azure Data Lake Storage. Överförda data till klusterlagring under analys görs beständig. Data är tillgängliga även efter att klustret har tagits bort. Olika verktyg kan hantera dataöverföringen till lagring. Verktygen inkluderar den portalbaserade uppladdningsanläggningen för lagringskontot och [AzCopy-verktyget.](../../storage/common/storage-use-azcopy.md)

Du kan aktivera åtkomst till ytterligare Blob- och Datasjölager när klustret skapas. Du begränsas inte av det primära lagringsalternativet som används.  Mer information om hur du använder flera lagringskonton finns i [Azure Storage-alternativ för ML-tjänster i hdinsight-artikeln.](./r-server-storage.md)

Du kan också använda [Azure-filer](../../storage/files/storage-how-to-use-files-linux.md) som ett lagringsalternativ för användning på kantnoden. Azure Files aktiverar filresurser som skapats i Azure Storage till Linux-filsystemet. Mer information finns i [Azure Storage-alternativ för ML-tjänster på HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Få tillgång till ML Services kantnod

Du kan ansluta till Microsoft ML Server på kantnoden med en webbläsare eller SSH/PuTTY. R-konsolen installeras som standard när klustret skapas.  

## <a name="develop-and-run-r-scripts"></a>Utveckla och köra R-skript

Dina R-skript kan använda något av de 8000+ R-paketen med öppen källkod. Du kan också använda parallelliserade och distribuerade rutiner från ScaleR-biblioteket. Skript körs på kantnoden körs inom R-tolken på den noden. Förutom steg som anropar ScaleR-funktioner med en beräkningskontext (Kartarminka) (RxHadoopMR) eller Spark (RxSpark). Funktionerna körs på ett distribuerat sätt över de datanoder som är associerade med data. Mer information om kontextalternativ finns i [Beräkningskontextalternativ för ML-tjänster på HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operationalisera en modell

När datamodelleringen är klar kan du operationalisera modellen för att göra förutsägelser för nya data antingen från Azure eller lokalt. Den här processen kallas poängsättning. Poängsättning kan göras i HDInsight, Azure Machine Learning eller lokalt.

### <a name="score-in-hdinsight"></a>Poäng i HDInsight

För att göra mål i HDInsight, skriv en R-funktion. Funktionen anropar din modell för att göra förutsägelser för en ny datafil som du har läst in på ditt lagringskonto. Spara sedan förutsägelserna tillbaka till lagringskontot. Du kan köra den här rutinen på begäran på kantnoden i klustret eller med hjälp av ett schemalagt jobb.

### <a name="score-in-azure-machine-learning-aml"></a>Poäng i Azure Machine Learning (AML)

Om du vill göra mål med Azure Machine Learning använder du Azure Machine Learning R-paketet med öppen källkod som kallas [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) för att publicera din modell som en Azure-webbtjänst. För enkelhetens skull är det här paketet förinstallerat på kantnoden. Använd sedan anläggningarna i Azure Machine Learning för att skapa ett användargränssnitt för webbtjänsten och anropa sedan webbtjänsten efter behov för bedömning. Konvertera sedan ScaleR-modellobjekt till likvärdiga modellobjekt med öppen källkod för användning med webbtjänsten. Använd ScaleR tvångfunktioner, `as.randomForest()` till exempel för ensemble-baserade modeller, för den här konverteringen.

### <a name="score-on-premises"></a>Betyg lokalt

Att göra mål lokalt när du har skapat din modell: serialisera modellen i R, ladda ner den, av serialisera den och använd den sedan för att göra nya data. Du kan få nya data att göra mål med hjälp av den metod som beskrivs tidigare i [Poäng i HDInsight](#score-in-hdinsight) eller genom att använda [webbtjänster](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Underhåll klustret

### <a name="install-and-maintain-r-packages"></a>Installera och underhålla R-paket

De flesta R-paket som du använder krävs på kantnoden eftersom de flesta stegen i dina R-skript körs där. Om du vill installera ytterligare R-paket på `install.packages()` kantnoden kan du använda metoden i R.

Om du bara använder ScaleR-biblioteksrutiner behöver du vanligtvis inte ytterligare R-paket. Du kan behöva ytterligare paket för **rxExec-** eller **RxDataStep-körningen** på datanoderna.

Ytterligare paket kan installeras med en skriptåtgärd när du har skapat klustret. Mer information finns i [Hantera ML-tjänster i HDInsight-klustret](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Ändra minnesinställningar för Apache Hadoop MapReduce

Tillgängligt minne till ML Services kan ändras när det kör ett MapReduce-jobb. Om du vill ändra ett kluster använder du Apache Ambari-användargränssnittet för klustret. Instruktioner för Ambari UI finns i [Hantera HDInsight-kluster med hjälp av Ambari Web UI](../hdinsight-hadoop-manage-ambari.md).

Tillgängligt minne till ML Services kan ändras med hjälp av Hadoop-switchar i anropet till **RxHadoopMR:**

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Skala klustret

Ett befintligt ML Services-kluster på HDInsight kan skalas upp eller ned via portalen. Genom att skala upp får du ytterligare kapacitet för större bearbetningsuppgifter. Du kan skala tillbaka ett kluster när det är inaktivt. Instruktioner om hur du skalar ett kluster finns i [Hantera HDInsight-kluster](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Underhåll systemet

OS-underhåll görs på de underliggande virtuella Linux-datorerna i ett HDInsight-kluster under off-hours. Normalt utförs underhåll klockan 03:30 (VM:s lokala tid) varje måndag och torsdag. Uppdateringar påverkar inte mer än en fjärdedel av klustret åt gången.

Jobb som körs kan sakta ner under underhåll. De bör dock fortfarande köras till färdigställande. All anpassad programvara eller lokala data som du har bevarats över dessa underhållshändelser om inte ett oåterkalleligt fel inträffar som kräver en klusterrena.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-alternativ för ML-tjänster på HDInsight

Linux-edgenoden för ett HDInsight-kluster är landningszonen för R-baserad analys. De senaste versionerna av HDInsight ger en webbläsarbaserad IDE för RStudio Server på kantnoden. RStudio Server är mer produktiv än R-konsolen för utveckling och körning.

En stationär IDE kan komma åt klustret via en fjärrkarteduce eller Spark-beräkningskontext. Alternativen inkluderar: Microsofts [R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS), RStudio och Walware's Eclipse-baserade [StatET](http://www.walware.de/goto/statet).

Öppna R-konsolen på kantnoden genom att skriva **R** i kommandotolken. När du använder konsolgränssnittet är det praktiskt att utveckla R-skript i en textredigerare. Klipp sedan ut och klistra in delar av skriptet i R-konsolen efter behov.

## <a name="pricing"></a>Prissättning

Priserna som är associerade med ett ML Services HDInsight-kluster är strukturerade på samma sätt som andra HDInsight-klustertyper. De baseras på storleken på de underliggande virtuella datorerna över namn- och kantnoderna. Core-timme upplyftningar också. Mer information finns i [HDInsight-priser](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder ML-tjänster i HDInsight-kluster finns i följande artiklar:

* [Köra ett R-skript i ett ML Services-kluster i Azure HDInsight med RStudio Server](machine-learning-services-quickstart-job-rstudio.md)
* [Alternativ för beräkningskontexter för ML-tjänstkluster i HDInsight](r-server-compute-contexts.md)
* [Lagringsalternativ för ML Services-kluster på HDInsight](r-server-storage.md)
