---
title: Introduktion till ML Services på Azure HDInsight
description: Lär dig hur du använder ML Services på HDInsight för att skapa program för stordataanalys.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 02/10/2020
ms.openlocfilehash: a77771880da962298f6e80782e5f3e251f5f4641
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77122369"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Vad är ML-tjänster i Azure HDInsight

Microsoft Machine Learning Server är tillgängligt som ett distributionsalternativ när du skapar HDInsight-kluster i Azure. Klustertypen som tillhandahåller det här alternativet kallas **ML Services**. Den här funktionen ger datavetare, statistiker och R-programmerare åtkomst på begäran till skalbara, distribuerade analysmetoder på HDInsight.

ML Services på HDInsight innehåller de senaste funktionerna för R-baserad analys av datauppsättningar av praktiskt taget alla storlekar, inlästa till antingen Azure Blob eller Data Lake-lagring. Eftersom ML Services-klustret är byggt på R med öppen källkod kan de R-baserade program som du skapar utnyttja något av de 8000+ R-paket med öppen källkod. Rutinerna i ScaleR, Microsofts stordataanalyspaket finns också tillgängliga.

Kantnoden i ett kluster är en praktisk plats för att ansluta till klustret och köra dina R-skript. Med en kantnod har du möjlighet att köra de parallelliserade distribuerade funktionerna i ScaleR över kärnorna på kantnodservern. Du kan också köra dem över noderna i klustret med hjälp av ScaleR:s Hadoop-karta minska eller Apache Spark-beräkningskontexter.

De modeller eller förutsägelser som resultat av analys kan laddas ner för lokal användning. De kan också användas på andra ställen i Azure, särskilt via [Azure Machine Learning Studio (klassisk)](https://studio.azureml.net) [webbtjänst](../../machine-learning/studio/deploy-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Komma igång med ML Services på HDInsight

Om du vill skapa ett ML Services-kluster i Azure HDInsight väljer du **klustertypen ML Services** när du skapar ett HDInsight-kluster med Azure-portalen. Ml Services-klustertypen innehåller ML Server på datanoderna i klustret och på en kantnod, som fungerar som en landningszon för ML Services-baserad analys. Se [Skapa Apache Hadoop-kluster med Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md) för en genomgång om hur du skapar klustret.

## <a name="why-choose-ml-services-in-hdinsight"></a>Varför välja ML Services i HDInsight?

ML Services i HDInsight ger följande fördelar:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>AI-innovation från Microsoft och öppen källkod

  ML Services innehåller mycket skalbara, distribuerade uppsättning algoritmer som [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)och [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) som kan arbeta med datastorlekar som är större än storleken på det fysiska minnet och köras på en mängd olika plattformar på ett distribuerat sätt. Läs mer om samlingen av Microsofts anpassade [R-paket](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) och [Python-paket](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) som ingår i produkten.
  
  ML Services överbryggar dessa Microsoft-innovationer och bidrag från verktygslådor med öppen källkod (R,Python och AI-verktygslådor) ovanpå en enda plattform i företagsklass. Alla R- eller Python-maskininlärningspaket med öppen källkod kan fungera sida vid sida med all egenutvecklad innovation från Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Enkel, säker och storskalig operationalisering och administration

  Företag som förlitar sig på traditionella paradigm och miljöer investerar mycket tid och ansträngning för operationalisering. Detta resulterar i uppblåsta kostnader och förseningar, inklusive översättningstiden för modeller, iterationer för att hålla dem giltiga och aktuella, myndighetsgodkännande och hantera behörigheter genom operationalisering.

  ML Services erbjuder [operationalisering](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)i företagsklass , eftersom det, efter att en maskininlärningsmodell har slutförts, tar det bara några klick för att generera API:er för webbtjänster. Dessa [webbtjänster](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) finns på ett servernät i molnet och kan integreras med affärsprogram. Möjligheten att distribuera till ett elastiskt rutnät gör att du kan skala sömlöst med ditt företags behov, både för batch- och realtidsbedömning. Instruktioner finns i [Operationalize ML Services på HDInsight](r-server-operationalize.md).

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

Standardlagring för HDFS-filsystemet i HDInsight-kluster kan associeras med antingen ett Azure Storage-konto eller ett Azure Data Lake Storage. Den här associeringen säkerställer att oavsett data överförs till klusterlagringen under analysen görs beständig och att data är tillgängliga även efter att klustret har tagits bort. Det finns olika verktyg för att hantera dataöverföringen till lagringsalternativet som du väljer, inklusive den portalbaserade uppladdningsanläggningen för lagringskontot och [AzCopy-verktyget.](../../storage/common/storage-use-azcopy.md)

Du kan välja att aktivera åtkomst till ytterligare Blob- och Datasjölager under klusteretableringsprocessen oavsett vilket primärt lagringsalternativ som används.  Mer information om hur du använder flera lagringskonton finns i [Azure Storage-alternativ för ML-tjänster i hdinsight-artikeln.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage)

Du kan också använda [Azure-filer](../../storage/files/storage-how-to-use-files-linux.md) som ett lagringsalternativ för användning på kantnoden. Med Azure Files kan du montera en filresurs som har skapats i Azure Storage till Linux-filsystemet. Mer information om dessa datalagringsalternativ för ML-tjänster i HDInsight-klustret finns i [Azure Storage-alternativ för ML-tjänster på HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Få tillgång till ML Services kantnod

Du kan ansluta till Microsoft ML Server på kantnoden med hjälp av en webbläsare. Den installeras som standard när klustret skapas.  Du kan också ansluta till klusterkantnoden från kommandoraden med hjälp av SSH/PuTTY för att komma åt R-konsolen.

## <a name="develop-and-run-r-scripts"></a>Utveckla och köra R-skript

De R-skript som du skapar och kör kan använda något av de 8000+ R-paket med öppen källkod utöver de parallelliserade och distribuerade rutiner som finns i ScaleR-biblioteket. I allmänhet körs ett skript som körs med ML Services på kantnoden inom R-tolken på den noden. Undantagen är de steg som måste anropa en ScaleR-funktion med en beräkningskontext som är inställd på Hadoop Map Reduce (RxHadoopMR) eller Spark (RxSpark). I det här fallet körs funktionen på ett distribuerat sätt över de data-noder (aktivitet) i klustret som är associerade med de data som refereras. Mer information om de olika beräkningskontextalternativen finns i [Beräkningskontextalternativ för ML-tjänster på HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operationalisera en modell

När datamodelleringen är klar kan du operationalisera modellen för att göra förutsägelser för nya data antingen från Azure eller lokalt. Den här processen kallas poängsättning. Poängsättning kan göras i HDInsight, Azure Machine Learning eller lokalt.

### <a name="score-in-hdinsight"></a>Poäng i HDInsight

Om du vill göra mål i HDInsight skriver du en R-funktion som anropar din modell för att göra förutsägelser för en ny datafil som du har läst in på ditt lagringskonto. Spara sedan förutsägelserna tillbaka till lagringskontot. Du kan köra den här rutinen på begäran på kantnoden i klustret eller med hjälp av ett schemalagt jobb.

### <a name="score-in-azure-machine-learning-aml"></a>Poäng i Azure Machine Learning (AML)

Om du vill göra mål med Azure Machine Learning använder du Azure Machine Learning R-paketet med öppen källkod som kallas [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) för att publicera din modell som en Azure-webbtjänst. För enkelhetens skull är det här paketet förinstallerat på kantnoden. Använd sedan anläggningarna i Azure Machine Learning för att skapa ett användargränssnitt för webbtjänsten och anropa sedan webbtjänsten efter behov för bedömning.

Om du väljer det här alternativet måste du konvertera alla ScaleR-modellobjekt till likvärdiga modellobjekt med öppen källkod som ska användas med webbtjänsten. Använd ScaleR tvångfunktioner, `as.randomForest()` till exempel för ensemble-baserade modeller, för den här konverteringen.

### <a name="score-on-premises"></a>Betyg lokalt

Om du vill göra mål lokalt när du har skapat modellen kan du serialisera modellen i R, hämta den, av serialisera den och sedan använda den för att göra nya data. Du kan få nya data att göra mål med hjälp av den metod som beskrivs tidigare i [Poäng i HDInsight](#score-in-hdinsight) eller genom att använda [webbtjänster](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Underhåll klustret

### <a name="install-and-maintain-r-packages"></a>Installera och underhålla R-paket

De flesta R-paket som du använder krävs på kantnoden eftersom de flesta stegen i dina R-skript körs där. Om du vill installera ytterligare R-paket på `install.packages()` kantnoden kan du använda metoden i R.

Om du bara använder rutiner från ScaleR-biblioteket i klustret behöver du vanligtvis inte installera ytterligare R-paket på datanoderna. Du kan dock behöva ytterligare paket för att stödja användningen av **rxExec-** eller **RxDataStep-körningen** på datanoderna.

I sådana fall kan ytterligare paket installeras med en skriptåtgärd när du har skapat klustret. Mer information finns i [Hantera ML-tjänster i HDInsight-klustret](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Ändra minnesinställningar för Apache Hadoop MapReduce

Ett kluster kan ändras för att ändra mängden minne som är tillgängligt för ML Services när det körs ett MapReduce-jobb. Om du vill ändra ett kluster använder du apacheambari-användargränssnittet som är tillgängligt via Azure-portalbladet för klustret. Instruktioner om hur du kommer åt Ambari-användargränssnittet för klustret finns i [Hantera HDInsight-kluster med hjälp av Ambari Web UI](../hdinsight-hadoop-manage-ambari.md).

Det är också möjligt att ändra mängden minne som är tillgängligt för ML Services genom att använda Hadoop-switchar i anropet till **RxHadoopMR** enligt följande:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Skala klustret

Ett befintligt ML Services-kluster på HDInsight kan skalas upp eller ned via portalen. Genom att skala upp kan du få den ytterligare kapacitet som du kan behöva för större bearbetningsuppgifter, eller så kan du skala tillbaka ett kluster när det är inaktivt. Instruktioner om hur du skalar ett kluster finns i [Hantera HDInsight-kluster](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Underhåll systemet

Underhåll för att tillämpa OS-korrigeringar och andra uppdateringar utförs på de underliggande virtuella Linux-datorerna i ett HDInsight-kluster under off-hours. Vanligtvis sker underhåll klockan 03:30 (baserat på den lokala tiden för den virtuella datorn) varje måndag och torsdag. Uppdateringar utförs på ett sådant sätt att de inte påverkar mer än en fjärdedel av klustret åt gången.

Eftersom huvudnoderna är överflödiga och inte alla datanoder påverkas kan alla jobb som körs under den här tiden sakta ner. De bör dock fortfarande köras till färdigställande. All anpassad programvara eller lokala data som du har bevaras över dessa underhållshändelser om inte ett oåterkalleligt fel inträffar som kräver en klusterreversion.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-alternativ för ML-tjänster på HDInsight

Linux-edgenoden för ett HDInsight-kluster är landningszonen för R-baserad analys. De senaste versionerna av HDInsight ger en standardinstallation av RStudio Server på kantnoden som en webbläsarbaserad IDE. Användning av RStudio Server som IDE för utveckling och körning av R-skript kan vara betydligt mer produktiv än att bara använda R-konsolen.

Dessutom kan du installera en stationär IDE och använda den för att komma åt klustret med hjälp av en fjärrkarteduce eller Spark-beräkningskontext. Alternativen inkluderar Microsofts [R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS), RStudio och Walware's Eclipse-baserade [StatET](http://www.walware.de/goto/statet).

Dessutom kan du komma åt R-konsolen på kantnoden genom att skriva **R** i Linux-kommandotolken efter anslutning via SSH eller PuTTY. När du använder konsolgränssnittet är det praktiskt att köra en textredigerare för R-skriptutveckling i ett annat fönster och klippa ut och klistra in delar av skriptet i R-konsolen efter behov.

## <a name="pricing"></a>Prissättning

De priser som är associerade med ett ML Services HDInsight-kluster är strukturerade på samma sätt som priserna för andra HDInsight-klustertyper. De baseras på storleken på de underliggande virtuella datorerna över namn- och kantnoderna, med tillägg av en upplyftning i kärntimmen. Mer information finns i [HDInsight-priser](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder ML-tjänster i HDInsight-kluster finns i följande avsnitt:

* [Köra ett R-skript i ett ML Services-kluster i Azure HDInsight med RStudio Server](machine-learning-services-quickstart-job-rstudio.md)
* [Alternativ för beräkningskontexter för ML-tjänstkluster i HDInsight](r-server-compute-contexts.md)
* [Lagringsalternativ för ML Services-kluster på HDInsight](r-server-storage.md)
