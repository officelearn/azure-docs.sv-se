---
title: Introduktion till ML-tjänster på Azure HDInsight
description: Lär dig hur du använder ML-tjänster i HDInsight för att skapa program för stor data analys.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/12/2019
ms.openlocfilehash: 5108424c4e39c1c47710c0e25e4e22c4474e68ad
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68941698"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Vad är ML-tjänster i Azure HDInsight

Microsoft Machine Learning Server är tillgängligt som ett distributions alternativ när du skapar HDInsight-kluster i Azure. Kluster typen som innehåller det här alternativet kallas **ml-tjänster**. Den här funktionen ger data vetenskaps-, statistiker-och R-programmerare med åtkomst på begäran till skalbara, distribuerade analys metoder på HDInsight.

Med ML tjänster i HDInsight får du de senaste funktionerna för R-baserade analyser på data uppsättningar i praktiskt taget vilken storlek som helst, som läses in på antingen Azure Blob eller Data Lake-lagring. Eftersom ML Services-kluster bygger på R med öppen källkod kan de R-baserade programmen som du skapar utnyttja alla R-paket med öppen källkod. Rutinerna i scaler, Microsofts Big data Analytics-paket är också tillgängliga.

Edge-noden i ett kluster är en praktisk plats för att ansluta till klustret och köra R-skript. Med en Edge-nod kan du välja att köra de parallellt distribuerade funktionerna i skalan över gränserna för Edge-nodens Server. Du kan också köra dem över noderna i klustret med hjälp av skalnings tjänstens Hadoop-karta minska eller Apache Spark Compute-kontexter.

De modeller eller förutsägelser som orsakas av analyser kan laddas ned för lokal användning. De kan också användas på andra platser i Azure, särskilt via [Azure Machine Learning Studio](https://studio.azureml.net) [-webbtjänst](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Kom igång med ML-tjänster i HDInsight

Om du vill skapa ett ML Services-kluster i Azure HDInsight väljer du kluster typen **ml Services** när du skapar ett HDInsight-kluster med hjälp av Azure Portal. Kluster typen ML-tjänster innehåller ML Server på datanoderna i klustret och på en Edge-nod, som fungerar som en landnings zon för ML service-baserad analys. Se [skapa Apache Hadoop kluster med hjälp av Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) för en genom gång av hur du skapar klustret.

## <a name="why-choose-ml-services-in-hdinsight"></a>Varför bör man välja ML-tjänster i HDInsight?

ML-tjänster i HDInsight ger följande fördelar:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>AI-innovation från Microsoft och öppen källkod

  ML-tjänster innehåller hög skalbar, distribuerad uppsättning algoritmer som [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)och [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) som kan arbeta med data storlekar som är större än storleken på det fysiska minnet och körs på en mängd olika plattformar i en fördelat sätt. Läs mer om samlingen av Microsofts anpassade R- [paket](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) och [python-paket](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) som ingår i produkten.
  
  ML-tjänster förenar dessa Microsoft-innovationer och bidrag från communityn för öppen källkod (R, python och AI-verktyg) allt ovanpå en enda plattform i företags klass. Ett Machine Learning-paket med R eller python kan fungera sida vid sida med en egen innovation från Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Enkel, säker och driftsättning och administration med hög skala

  Företag som förlitar sig på traditionella paradigm och miljöer investerar mycket tid och ansträngning mot driftsättning. Detta resulterar i fasta kostnader och fördröjningar, inklusive översättnings tiden för modeller, iterationer för att hålla dem giltiga och aktuella, reglerade godkännande och hantera behörigheter via driftsättning.

  ML-tjänster erbjuder [driftsättning](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)i företags klass, i det fall när en maskin inlärnings modell har slutförts tar det bara några klick att generera webb tjänst-API: er. Dessa [webb tjänster](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) finns i ett Server rutnät i molnet och kan integreras med branschspecifika program. Möjligheten att distribuera till ett elastiskt rutnät gör att du kan skala sömlöst med företagets behov, både för batch-och real tids resultat. Instruktioner finns i [OPERATIONALISERA ml-tjänster i HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>Viktiga funktioner i ML-tjänster i HDInsight

Följande funktioner ingår i ML-tjänster i HDInsight.

| Funktions kategori | Beskrivning |
|------------------|-------------|
| R-aktiverad | [R-paket](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) för lösningar som skrivits i r, med en distribution med öppen källkod av R och körnings infrastruktur för skript körning. |
| Python-aktiverat | [Python-moduler](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) för lösningar som skrivits i python, med en distribution med öppen källkod av python och körnings infrastruktur för skript körning.
| [Förtränade modeller](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | För analys av visuella analyser och text sentiment är det dags att visa data som du anger. |
| [Distribuera och förbruka](r-server-operationalize.md) | Operationalisera servern och distribuera lösningar som en webb tjänst. |
| [Fjärrkörning](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Starta fjärrsessioner på ML Services-kluster i nätverket från din klient dator. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Alternativ för data lagring för ML-tjänster i HDInsight

Standard lagring för HDFS-filsystemet i HDInsight-kluster kan associeras med antingen ett Azure Storage konto eller ett Azure Data Lake Storage. Den här kopplingen ser till att alla data som överförs till kluster lagringen under analysen görs beständiga och att data är tillgängliga även efter att klustret har tagits bort. Det finns olika verktyg för att hantera data överföringen till det lagrings alternativ som du väljer, inklusive Portal-baserad uppladdnings funktion för lagrings kontot och [AzCopy](../../storage/common/storage-use-azcopy.md) -verktyget.

Du kan välja att aktivera åtkomst till ytterligare blob-och data Lake Store under kluster etablerings processen oavsett vilket primärt lagrings alternativ som används.  Se [Azure Storage alternativ för ml-tjänster i HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) -artikeln för att lära dig mer om hur du använder flera lagrings konton.

Du kan också använda [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) som ett lagrings alternativ för att använda på Edge-noden. Med Azure Files kan du montera en fil resurs som har skapats i Azure Storage till Linux-filsystemet. Mer information om dessa alternativ för data lagring för ML-tjänster i HDInsight-kluster finns [Azure Storage alternativ för ml-tjänster i HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Edge-noden Access ML Services

Du kan ansluta till Microsoft ML Server på Edge-noden med hjälp av en webbläsare. Den installeras som standard när klustret skapas.  Du kan också ansluta till klustret Edge-noden från kommando raden med hjälp av SSH/SparaTillFil för att få åtkomst till R-konsolen.

## <a name="develop-and-run-r-scripts"></a>Utveckla och köra R-skript

De R-skript som du skapar och kör kan använda alla R-paket med öppen källkod utöver de parallella och distribuerade rutinerna som finns i skalnings biblioteket. I allmänhet körs ett skript som körs med ML-tjänster på Edge-noden i R-tolken på den noden. Undantag är de steg som krävs för att anropa en scaler-funktion med en beräknings kontext som är inställd på Hadoop Map reduce (RxHadoopMR) eller Spark (Rxspark beräkningskontexten). I det här fallet körs funktionen i ett distribuerat läge över dessa data (aktivitet) noder i klustret som är associerade med data som refereras till. Mer information om olika alternativ för beräknings kontexter finns i [alternativ för beräknings kontext för ml-tjänster i HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operationalisera en modell

När data modelleringen är klar kan du operationalisera modellen för att göra förutsägelser för nya data antingen från Azure eller lokalt. Den här processen kallas för poängsättning. Du kan göra en beräkning i HDInsight, Azure Machine Learning eller lokalt.

### <a name="score-in-hdinsight"></a>Poäng i HDInsight

Skriv en R-funktion som anropar din modell för att göra förutsägelser för en ny datafil som du har läst in till ditt lagrings konto för att få poäng i HDInsight. Spara sedan förutsägelserna på lagrings kontot. Du kan köra den här rutinen på begäran på noden Edge i klustret eller genom att använda ett schemalagt jobb.

### <a name="score-in-azure-machine-learning-aml"></a>Poäng i Azure Machine Learning (AML)

Om du vill använda Azure Machine Learning använder du det Azure Machine Learning R-paketet med öppen källkod som heter [azureml](https://cran.r-project.org/src/contrib/Archive/AzureML/) för att publicera din modell som en Azure-webbtjänst. För enkelhetens skull är det här paketet förinstallerat på Edge-noden. Använd sedan funktionerna i Azure Machine Learning för att skapa ett användar gränssnitt för webb tjänsten och anropa sedan webb tjänsten efter behov för att gå vidare.

Om du väljer det här alternativet måste du konvertera alla skalnings modell objekt till motsvarande modell objekt med öppen källkod för användning med webb tjänsten. Använd funktioner för skalnings tvång, till exempel `as.randomForest()` för Ensemble-baserade modeller, för den här konverteringen.

### <a name="score-on-premises"></a>Poängen lokalt

Om du vill att poängen ska vara lokalt när du har skapat din modell kan du serialisera modellen i R, ladda ned den, deserialisera den och sedan använda den för att bedöma nya data. Du kan räkna med nya data med hjälp av metoden som beskrivits tidigare i [Poäng i HDInsight](#score-in-hdinsight) eller med hjälp av [webb tjänster](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Underhålla klustret

### <a name="install-and-maintain-r-packages"></a>Installera och underhålla R-paket

De flesta R-paket som du använder krävs på Edge-noden eftersom de flesta stegen i dina R-skript körs där. Om du vill installera ytterligare R-paket på Edge-noden kan du `install.packages()` använda-metoden i R.

Om du bara använder rutiner från skalnings biblioteket i klustret, behöver du vanligt vis inte installera ytterligare R-paket på datanoderna. Du kan dock behöva ytterligare paket för att stödja användningen av **rxExec** -eller **RxDataStep** -körning på datanoderna.

I sådana fall kan du installera ytterligare paket med en skript åtgärd när du har skapat klustret. Mer information finns i [Hantera ml-tjänster i HDInsight-kluster](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Ändra Apache Hadoop MapReduce minnes inställningar

Ett kluster kan ändras för att ändra mängden minne som är tillgängligt för ML-tjänster när ett MapReduce-jobb körs. Om du vill ändra ett kluster använder du Apache Ambari-ANVÄNDARGRÄNSSNITTET som är tillgängligt via bladet Azure Portal för klustret. Anvisningar om hur du kommer åt Ambari-ANVÄNDARGRÄNSSNITTET för klustret finns i [Hantera HDInsight-kluster med hjälp av Ambari-](../hdinsight-hadoop-manage-ambari.md)webbgränssnittet.

Det är också möjligt att ändra mängden minne som är tillgängligt för ML-tjänster med hjälp av Hadoop-växlar i anropet till **RxHadoopMR** på följande sätt:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Skala ditt kluster

Ett befintligt ML-kluster i HDInsight kan skalas upp eller ned via portalen. Genom att skala upp kan du få den ytterligare kapacitet som du kan behöva för större bearbetnings uppgifter eller så kan du skala upp ett kluster när det är inaktivt. Instruktioner för hur du skalar ett kluster finns i [Hantera HDInsight-kluster](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Underhålla systemet

Underhåll för att tillämpa OS-korrigeringsfiler och andra uppdateringar utförs på de underliggande virtuella Linux-datorerna i ett HDInsight-kluster under andra tider. Normalt görs underhåll på 3:30 AM (baserat på den lokala tiden för den virtuella datorn) varje måndag och torsdag. Uppdateringar utförs på ett sådant sätt att de inte påverkar mer än en fjärdedel av klustret i taget.

Eftersom huvudnoderna är redundanta och inte alla datanoder påverkas, kan alla jobb som körs under den här tiden sakta ned. De bör dock fortfarande köras till slut för ande. All anpassad program vara eller lokala data som du har bevaras över dessa underhålls händelser om inte ett oåterkalleligt fel uppstår som kräver en kluster återuppbyggnad.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-alternativ för ML-tjänster i HDInsight

Linux Edge-noden i ett HDInsight-kluster är landnings zon för R-baserad analys. De senaste versionerna av HDInsight tillhandahåller en standard installation av RStudio-servern på Edge-noden som en webbläsarbaserad IDE. Användning av RStudio-servern som en IDE för utveckling och körning av R-skript kan vara betydligt mer produktiv än att bara använda R-konsolen.

Dessutom kan du installera en Desktop IDE och använda den för att få åtkomst till klustret genom att använda en fjärran sluten MapReduce eller Spark Compute context. Alternativen omfattar Microsofts [R-verktyg för Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS), RStudio och Walwares Sol förmörkelse-baserade [tillstånd](http://www.walware.de/goto/statet).

Dessutom kan du komma åt R-konsolen på Edge-noden genom att skriva **R** i kommando tolken i Linux när du har anslutit via SSH eller SparaTillFil. När du använder-konsol gränssnittet är det praktiskt att köra en text redigerare för R-skript utveckling i ett annat fönster och klippa ut och klistra in avsnitt i skriptet i R-konsolen efter behov.

## <a name="pricing"></a>Prissättning

Priserna som är associerade med ett ML-kluster i ML-tjänster struktureras på samma sätt som priserna för andra typer av HDInsight-kluster. De baseras på storleken på de underliggande virtuella datorerna över namn-, data-och Edge-noderna, med tillägg av en prishöjning på en timme. Mer information finns i avsnittet om [priser för HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder ML-tjänster i HDInsight-kluster finns i följande avsnitt:

* [Köra ett R-skript i ett ML Services-kluster i Azure HDInsight med RStudio-Server](machine-learning-services-quickstart-job-rstudio.md)
* [Alternativ för beräkningskontexter för ML-tjänstkluster i HDInsight](r-server-compute-contexts.md)
* [Lagrings alternativ för ML Services-kluster i HDInsight](r-server-storage.md)
