---
title: "Skapa rekommendationer med hjälp av Mahout HDInsight från PowerShell - Azure | Microsoft Docs"
description: "Lär dig hur du använder Apache Mahout-machine learning-biblioteket för att generera filmrekommendationer med HDInsight (Hadoop) från ett PowerShell-skript som körs på klienten."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: larryfr
ms.openlocfilehash: a2477b0a7c9c76f8ce4a183f4d699ddf4c291023
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2018
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>Generera filmrekommendationer med hjälp av Apache Mahout med Hadoop i HDInsight (PowerShell)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Lär dig hur du använder den [Apache Mahout](http://mahout.apache.org) machine learning-biblioteket med Azure HDInsight för att generera filmrekommendationer. Exemplet i det här dokumentet använder Azure PowerShell för att köra Mahout jobb.

## <a name="prerequisites"></a>Förutsättningar

* Ett Linux-baserat HDInsight-kluster. Information om hur du skapar en finns [komma igång med Linux-baserade Hadoop i HDInsight][getstarted].

    > [!IMPORTANT]
    > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Azure PowerShell](/powershell/azure/overview)

## <a name="recommendations"></a>Skapa rekommendationer med hjälp av Azure PowerShell

> [!WARNING]
> Jobbet i det här avsnittet fungerar med hjälp av Azure PowerShell. Många av de klasser som medföljer Mahout fungerar inte för närvarande med Azure PowerShell. En lista med klasser som inte fungerar med Azure PowerShell finns i [felsökning](#troubleshooting) avsnitt.
>
> Ett exempel på hur du använder SSH för att ansluta till HDInsight och kör Mahout exempel direkt på klustret finns [generera filmrekommendationer med hjälp av Mahout och HDInsight (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md).

En av de funktioner som tillhandahålls av Mahout är en rekommendation motor. Den här motorn accepterar data i formatet `userID`, `itemId`, och `prefValue` (användare preferens för artikeln). Mahout använder informationen för att avgöra användare med liknande objekt inställningar som kan användas för att ge rekommendationer.

Följande exempel är en förenklad genomgång av hur rekommendationen processen fungerar:

* **samtidigt förekomsten**: Joe Alice och Bob alla tyckte *Star krig*, *i Empire strejker tillbaka*, och *tillbaka Jedi*. Mahout anger att användare också som en av dessa filmer som de andra två.

* **samtidigt förekomsten**: Bob och Alice också tyckte *i Phantom hot*, *Attack av klonerna*, och *Revenge av Sith*. Mahout anger att användare som även gillade föregående tre filmer som dessa filmer.

* **Likhet rekommendation**: Joe eftersom tyckte om de första tre filmerna, Mahout tittar på filmer som andra med liknande inställningar tyckte om, men Johan har inte bevakade (tyckte/klassificerad). I det här fallet Mahout rekommenderar *i Phantom hot*, *Attack av klonerna*, och *Revenge av Sith*.

### <a name="understanding-the-data"></a>Förstå data

[GroupLens Research] [ movielens] innehåller klassificering av data för filmer i ett format som är kompatibel med Mahout. Informationen är tillgänglig i standard lagringsutrymme för klustret på `/HdiSamples/HdiSamples/MahoutMovieData`.

Det finns två filer, `moviedb.txt` (information om filmer) och `user-ratings.txt`. Den `user-ratings.txt` filen som ska användas vid analys. Den `moviedb.txt` filen används för att tillhandahålla användarvänliga text när du visar resultatet av analysen.

Data i ratings.txt användare har en struktur med `userID`, `movieID`, `userRating`, och `timestamp`, som anger hur mycket en film klassificerats som varje användare. Här är ett exempel på data:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>Kör jobbet

Använd följande Windows PowerShell-skript för att köra ett jobb som använder Mahout rekommendation motor med filmdata:

> [!NOTE]
> Den här filen efterfrågas information som används för att ansluta till ditt HDInsight-kluster och köra jobb. Det kan ta flera minuter att slutföra och hämta filen output.txt-jobb.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

> [!NOTE]
> Mahout jobb ta inte bort tillfälliga data som har skapats under bearbetning av jobbet. Den `--tempDir` parameter har angetts i exempel jobbet att isolera de temporära filerna i en specifik katalog.

Jobbet Mahout returnerar inga utdata STDOUT. I stället lagras den i den angivna målkatalogen som **del-r-00000**. Skriptet laddar ned filen till **output.txt** i den aktuella katalogen på din arbetsstation.

Följande är ett exempel på innehållet i den här filen:

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

Den första kolumnen är den `userID`. Värdena i ' [' och ']' är `movieId`:`recommendationScore`.

Skriptet också hämtar den `moviedb.txt` och `user-ratings.txt` filer som behövs för att formatera utdata för att lättare att läsa.

### <a name="view-the-output"></a>Visa utdata

Även om genererade utdata kan vara OK för användning i ett program, är det inte användarvänliga. Den `moviedb.txt` från servern kan användas för att matcha den `movieId` till ett film namn. Använd följande PowerShell-skript för att visa rekommendationer med film namn:

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

Använd följande kommando för att visa rekommendationerna i ett användarvänligt format: 

```powershell
.\show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt
```

De utdata som genereras liknar följande text:

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237

## <a name="troubleshooting"></a>Felsökning

### <a name="cannot-overwrite-files"></a>Det går inte att skriva över filer

Mahout jobb vill inte ta bort temporära filer som skapades under bearbetning. Dessutom Skriv jobben inte över befintliga utdatafilen.

Om du vill undvika fel när Mahout jobb som körs, att ta bort temporära och utgående filer mellan körs. Använd följande PowerShell-skript för att ta bort de filer som skapats av tidigare skript i det här dokumentet:

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard,
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="nopowershell"></a>Klasser som inte fungerar med Azure PowerShell

Mahout jobb som använder följande klasser returnera olika felmeddelanden när används från Windows PowerShell:

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

För att köra jobb som använder dessa klasser, ansluta till HDInsight-klustret med SSH och köra jobb från kommandoraden. Ett exempel för att använda SSH för att köra Mahout jobb finns [generera filmrekommendationer med hjälp av Mahout och HDInsight (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Mahout, identifiera andra sätt att arbeta med data i HDInsight:

* [Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Pig med HDInsight](hadoop/hdinsight-use-pig.md)
* [MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
