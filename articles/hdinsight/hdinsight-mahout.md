---
title: Skapa rekommendationer med Mahout HDInsight från PowerShell - Azure
description: Lär dig hur du använder Apache Mahout-machine learning-biblioteket för att skapa filmrekommendationer med HDInsight (Hadoop) från ett PowerShell-skript som körs på klienten.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 06181eaf4a44a00ddeeedcd9c40edeae9157abd9
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438556"
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-apache-hadoop-in-hdinsight-powershell"></a>Skapa filmrekommendationer med hjälp av Apache Mahout med Apache Hadoop i HDInsight (PowerShell)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Lär dig hur du använder den [Apache Mahout](http://mahout.apache.org) machine learning-biblioteket med Azure HDInsight för att generera filmrekommendationer. I exemplet i det här dokumentet används Azure PowerShell för att köra Mahout jobb.

## <a name="prerequisites"></a>Förutsättningar

* Ett Linux-baserade HDInsight-kluster. Information om hur du skapar en finns i [komma igång med Linux-baserat Hadoop i HDInsight][getstarted].

    > [!IMPORTANT]  
    > Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Azure PowerShell](/powershell/azure/overview)

## <a name="recommendations"></a>Skapa rekommendationer med hjälp av Azure PowerShell

> [!WARNING]  
> Jobbet i det här avsnittet fungerar med hjälp av Azure PowerShell. Många av de klasser som medföljer Mahout fungerar inte för närvarande med Azure PowerShell. En lista över klasser som inte fungerar med Azure PowerShell finns i den [felsökning](#troubleshooting) avsnittet.
>
> Ett exempel på hur du använder SSH för att ansluta till HDInsight och kör Mahout exempel direkt i klustret finns i [skapa filmrekommendationer med Apache Mahout och HDInsight (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md).

En av de funktioner som tillhandahålls av Mahout är en rekommendationsmotor. Den här motorn accepterar data i formatet `userID`, `itemId`, och `prefValue` (användare preferens för objektet). Mahout använder informationen för att fastställa användare med liknande objekt inställningar som kan användas för att göra rekommendationer.

I följande exempel är en förenklad genomgång av hur rekommendationen processen fungerar:

* **delad förekomsten**: Joe Alice och Bob alla gillade *Star Wars*, *The Empire under katastrofsituationer tillbaka*, och *avkastningen på Jedi*. Mahout anger att användare som gillar något av dessa filmer också som de andra två.

* **delad förekomsten**: Bob och Alice också tyckte *The Phantom hot*, *Attack av klonerna*, och *Revenge av Sith*. Mahout anger att användare som gillade föregående tre filmer också som dessa filmer.

* **Likhet rekommendation**: Eftersom Joe gillade tre första filmer, Mahout tittar på filmer den andra med liknande inställningar som gillade, men Josef har inte sett (gillade/klassificerad). I det här fallet Mahout rekommenderar *The Phantom hot*, *Attack av klonerna*, och *Revenge av Sith*.

### <a name="understanding-the-data"></a>Förstå data

[GroupLens Research] [ movielens] tillhandahåller data för klassificering för filmer i ett format som är kompatibel med Mahout. Informationen är tillgänglig på standardlagringen för klustret på `/HdiSamples/HdiSamples/MahoutMovieData`.

Det finns två filer, `moviedb.txt` (information om filmer) och `user-ratings.txt`. Den `user-ratings.txt` filen ska användas vid analys. Den `moviedb.txt` filen används för att tillhandahålla användarvänliga text när du visar resultatet av analysen.

Data i användaren ratings.txt har en struktur för `userID`, `movieID`, `userRating`, och `timestamp`, som anger hur varje användare väl en film. Här är ett exempel på data:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>Kör jobbet

Använd följande Windows PowerShell-skript för att köra ett jobb som använder Mahout-rekommendationsmotor med Filminformationen:

> [!NOTE]  
> Den här filen efterfrågas information som används för att ansluta till ditt HDInsight-kluster och köra jobb. Det kan ta flera minuter att slutföra och ladda ned filen output.txt-jobb.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

> [!NOTE]  
> Mahout jobb ta inte bort tillfälliga data som skapas vid bearbetning av jobbet. Den `--tempDir` parameter har angetts i exempel-jobbet för att isolera de temporära filerna till en viss katalog.

Mahout jobbet returnerar inte utdata till STDOUT. I stället den lagrar den i den angivna katalogen som **del-r-00000**. Skriptet hämtar filen till **output.txt** i den aktuella katalogen på din arbetsstation.

Följande text är ett exempel på innehållet i den här filen:

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

Den första kolumnen är den `userID`. De värden som finns i ' [' och ']' är `movieId`:`recommendationScore`.

Skriptet kan även laddas ned den `moviedb.txt` och `user-ratings.txt` filer som behövs för att formatera utdata att vara mer lättlästa.

### <a name="view-the-output"></a>Visa utdata

Även om den genererade utdatan kan vara OK för användning i ett program, är det inte användarvänliga. Den `moviedb.txt` från servern kan användas för att lösa den `movieId` till ett film-namn. Använd följande PowerShell-skript för att visa rekommendationer med film namn:

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

Använd följande kommando för att visa rekommendationer i ett användarvänligt format: 

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

Mahout jobb du inte rensa för tillfälliga filer som skapades under bearbetning. Dessutom jobben inte över befintliga utdatafilen.

Om du vill undvika fel vid Mahout jobb som körs, att ta bort tillfälliga och utgående filer mellan körningar. Använd följande PowerShell-skript för att ta bort filer som skapas av de tidigare skript i det här dokumentet:

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzureRmAccount
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

Mahout-jobb som använder följande klasser returnera olika felmeddelanden när de används från Windows PowerShell:

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

Om du vill köra jobb som använder de här klasserna, ansluta till HDInsight-klustret med SSH och kör jobb från kommandoraden. Ett exempel på hur du använder SSH för att köra Mahout jobb finns i [skapa filmrekommendationer med Apache Mahout och HDInsight (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Apache Mahout kan upptäcka andra sätt att arbeta med data i HDInsight:

* [Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig med HDInsight](hadoop/hdinsight-use-pig.md)
* [MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: https://en.wikipedia.org/wiki/Machine_learning
[forest]: https://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
