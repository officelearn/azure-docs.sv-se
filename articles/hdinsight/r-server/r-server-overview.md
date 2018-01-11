---
title: "Introduktion till R Server på Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du använder R Server på HDInsight för att skapa program för stordataanalys."
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 860099b2634765f3088199c89341f0b48b20b801
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2018
---
#<a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>Introduktion till R Server och öppen källkod R-funktioner på HDInsight

Microsoft R Server är tillgänglig som ett distributionsalternativ när du skapar HDInsight-kluster i Azure. Den här nya funktionen ger dataanalytiker, statistiker och R-programmerare på begäran åtkomst till skalbara, distribuerade metoder för analyser på HDInsight.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Kluster kan storlek på lämpligt sätt till projekt och uppgifter till hands och datakanalen när de inte längre behövs. Eftersom de är en del av Azure HDInsight medföljer dessa kluster på företagsnivå 24/7 stöd och ett SLA för 99,9% drifttid kan integrera med andra komponenter i Azure-ekosystemet.

R Server på HDInsight ger de senaste funktionerna för analys av R-baserade på datauppsättningar av valfri storlek som har lästs in till Azure Blob eller Data Lake lagringsutrymme. Eftersom R Server bygger på öppen källkod R kan R-baserade program som du skapar utnyttja öppen källkod R-paket 8000 +. Det finns också rutiner i ScaleR, Microsofts big analytics datapaketet medföljer R Server.

Edge-nod i ett kluster ger en lämplig plats att ansluta till klustret och köra R-skript. Med en kantnod har möjlighet att köra parallelized distribuerade funktioner ScaleR över kärnor på noden gränsservern. Du kan också köra dem mellan noder i klustret med hjälp av Scaler's Hadoop kartan minska eller compute kontexter för Spark.

Använda lokala modeller eller förutsägelser som kan hämta resultat från analyser för. De kan också vara operationalized någon annanstans i Azure, i synnerhet via [Azure Machine Learning Studio](http://studio.azureml.net) [webbtjänsten](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-r-on-hdinsight"></a>Kom igång med R i HDInsight
Om du vill inkludera R Server i ett HDInsight-kluster, måste du välja R Server klustret typ när du skapar ett HDInsight-kluster med Azure-portalen. Typ av R Server-kluster innehåller R Server på datanoder i klustret och på en kantnod som fungerar som en stannplan för R Server-baserade analytics. Se [komma igång med R Server på HDInsight](r-server-get-started.md) stegvisa anvisningar för hur du skapar klustret.

## <a name="learn-about-data-storage-options"></a>Lär dig mer om alternativen för datalagring
Standardlagring för HDFS filsystemet på HDInsight-kluster kan associeras med ett Azure Storage-konto eller ett Azure Data Lake store. Den här associationen säkerställer att data överförs till klustret lagring under analysen görs permanent. Det finns flera olika verktyg för att hantera dataöverföring till lagringsalternativ du väljer, inklusive portal-baserade överför användandet av lagringskontot och [AzCopy](../../storage/common/storage-use-azcopy.md) verktyget.

Du har möjlighet att lägga till åtkomst till ytterligare Blob och Data lake lagrar under klustret etableringsprocessen oavsett primära lagringsalternativ används. Se [komma igång med R Server på HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) information om att lägga till åtkomst till ytterligare konton. Finns det ytterligare [Azure lagringsalternativ för R Server på HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) artikeln om du vill veta mer om hur du använder flera lagringskonton.

Du kan också använda [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) som ett lagringsalternativ för användning på kantnoden. Azure Files kan du montera en filresurs som skapats i Azure Storage till Linux-filsystem. Mer information om de här alternativen för datalagring för R Server på HDInsight-kluster finns [Azure Storage-alternativen för R Server på HDInsight-kluster](r-server-storage.md).

## <a name="access-r-server-on-the-cluster"></a>Åtkomst R Server i klustret
Du kan ansluta till R Server på kantnod med en webbläsare. Den installeras som standard när klustret skapas. Mer information finns i [komma igång med R Server på HDInsight](r-server-get-started.md).

Du kan också ansluta till R-Server från kommandoraden med hjälp av SSH/PuTTY åtkomst till R-konsolen. 

## <a name="develop-and-run-r-scripts"></a>Utveckla och köra R-skript
R-skript som du skapar och kör kan använda 8000 + öppen källkod R-paket förutom paralleliserad och distribuerade-rutiner finns i ScaleR-biblioteket. I allmänhet körs ett skript som körs med R Server på noden edge inom R-tolken på noden. Undantagen är de steg som måste anropa en ScaleR funktion med en kontext för beräkning som har angetts till Hadoop kartan minska (RxHadoopMR) eller Spark (RxSpark). I det här fallet körs funktionen i ett distribuerat sätt över dessa (aktivitet) datanoder i klustret som är associerade med de data som refererar till. Mer information om alternativ för olika beräkning kontexten finns [Compute-kontexten alternativ för R Server på HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operationalisera en modell
När dina datamodellering är klar, kan du operationalisera modellen för att göra förutsägelser för nya data från Azure och lokalt. Den här processen kallas bedömningen. Bedömningen kan göras i HDInsight, Azure Machine Learning eller lokalt.

### <a name="score-in-hdinsight"></a>Poäng i HDInsight
Skriva ett R-funktion som anropar din modell för att göra förutsägelser för en ny datafil som du har läst in till ditt lagringskonto för att poängsätta i HDInsight. Spara förutsägelser tillbaka till lagringskontot. Du kan köra den rutinunderhåll på begäran på kantnod på klustret eller med hjälp av ett schemalagt jobb.  

### <a name="score-in-azure-machine-learning-aml"></a>Poäng i Azure Maskininlärning (AML)
För att poängsätta med hjälp av en AML-webbtjänsten använder öppen källkod Azure Machine Learning R paketet kallas [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) att publicera din modell som Azure-webbtjänst. Det här paketet har förinstallerats kantnoden för enkelhetens skull. Sedan använda funktionerna i Machine Learning för att skapa ett användargränssnitt för webbtjänsten och sedan anropa webbtjänsten som krävs för resultatfunktioner.

Om du väljer det här alternativet måste du konvertera ScaleR modellobjekt till motsvarande öppen källkod modellobjekt för användning med webbtjänsten. Använda ScaleR tvång funktioner, till exempel `as.randomForest()` för ensemble-baserade modeller för den här konverteringen.

### <a name="score-on-premises"></a>Poäng på lokalt
För att poängsätta lokalt när du har skapat din modell du serialisera modellen i R, ladda ned det, deserialisera den och sedan använda datorn för resultatfunktioner nya data. Du kan samla in nya data med hjälp av den metod som beskrivs tidigare i [Resultatfunktioner i HDInsight](#scoring-in-hdinsight) eller genom att använda [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Underhåll klustret
### <a name="install-and-maintain-r-packages"></a>Installera och underhålla R-paket
De flesta av de R-paket som du använder krävs på noden edge eftersom de flesta stegen för din R-skript körs det. Om du vill installera ytterligare R-paket på edge-nod, och du kan använda den vanliga `install.packages()` metod i R.

Om du bara använder rutiner från biblioteket ScaleR hela klustret kan behöver du vanligtvis inte installera ytterligare R-paket på datanoder. Du kan dock behöva ytterligare paket för att ge stöd åt **rxExec** eller **RxDataStep** körning på datanoder.

I dessa fall kan ytterligare paket installeras med en skriptåtgärd när du har skapat klustret. Mer information finns i [skapar ett HDInsight-kluster med R Server](r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Ändra inställningarna för Hadoop kartan minska minne
Ett kluster kan ändras om du vill ändra mängden minne som är tillgängligt för R Server när den körs en karta minska jobb. Om du vill ändra ett kluster, använda Apache Ambari-användargränssnitt som är tillgängliga via Azure portalbladet för klustret. Instruktioner om hur du kommer åt Ambari UI för klustret, se [hantera HDInsight-kluster med Ambari-Webbgränssnittet](../hdinsight-hadoop-manage-ambari.md).

Det är också möjligt att ändra mängden minne som är tillgängligt för R Server med hjälp av Hadoop-växlar i anropet till **RxHadoopMR** på följande sätt:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Skala ditt kluster
Ett befintligt kluster kan skalas upp eller ned via portalen. Genom att skala, får du ytterligare kapacitet som kan behövas för större bearbetningsuppgifter eller du kan skala tillbaka ett kluster när den är inaktiv. Instruktioner om hur du skalar ett kluster, se [hantera HDInsight-kluster](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Underhåll av systemet
Underhåll tillämpa operativsystemskorrigeringar och andra uppdateringar utförs på den underliggande virtuella Linux-datorer i ett HDInsight-kluster kontorstid. Normalt görs Underhåll på 3:30 AM (baserat på den lokala tiden för den virtuella datorn) varje måndag och torsdag. Uppdateringar utförs så att de inte påverkar mer än ett kvartal i klustret samtidigt.  

Eftersom huvudnoderna är redundant och påverkas inte alla datanoder, kan alla jobb som körs under den här tiden långsammare. De bör fortfarande köra att slutföras, men. Anpassade programvara eller lokala data som du har bevaras över händelserna underhåll om ett oåterkalleligt fel inträffar som kräver att klustret.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>Lär dig mer om IDE-alternativ för R Server på ett HDInsight-kluster
Linux-kantnod för ett HDInsight-kluster är stannplan för R-baserade analys. Nya versioner av HDInsight ger en standardinstallation av RStudio Server på kantnoden som en webbläsarbaserad IDE. Användning av RStudio Server som IDE-miljö för utveckling och körning av R-skript kan vara betydligt mer produktiv än att bara använda R-konsolen.

Ett annat alternativ för fullständig IDE är att installera en stationär IDE och använda den för att få åtkomst till klustret med hjälp av en remote kartan minska eller Spark beräknings-kontext. Alternativen är Microsofts [R Tools för Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS) RStudio och Walware datorns Eclipse-baserade [StatET](http://www.walware.de/goto/statet).

Till sist ska du har åtkomst till konsolen på noden edge R Server genom att skriva **R** Linux Kommandotolken efter anslutning via SSH eller PuTY. När gränssnittet i konsolen, är det praktiskt att köra en textredigerare för utveckling av R-skript i ett annat fönster och klippa och klistra in avsnitt i ditt skript i konsolen R efter behov.

## <a name="learn-about-pricing"></a>Läs mer om prissättningen
Avgifter som är associerade med ett HDInsight-kluster med R Server är strukturerade på samma sätt att avgifter för standard HDInsight-kluster. De är baserade på storleksändring av de underliggande virtuella datorerna över namn, data och edge noder med att lägga till en timme core-höjning. Läs mer om prissättningen för HDInsight [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du använder R Server med HDInsight-kluster finns i följande avsnitt:

* [Komma igång med R Server på HDInsight](r-server-get-started.md)
* [Alternativ för beräkningskontexter för R Server på HDInsight](r-server-compute-contexts.md)
* [Alternativ för Azure Storage för R Server på HDInsight](r-server-storage.md)
