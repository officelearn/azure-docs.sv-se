---
title: Skapa rekommendationer med hjälp av Mahout och HDInsight (SSH) - Azure | Microsoft Docs
description: Lär dig hur du använder Apache Mahout-machine learning-biblioteket för att generera filmrekommendationer med HDInsight (Hadoop).
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c78ec37c-9a8c-4bb6-9e38-0bdb9e89fbd7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: larryfr
ms.openlocfilehash: 357df033c4d3fd0da84ea595d68802546aee3e9a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight-ssh"></a>Generera filmrekommendationer med hjälp av Apache Mahout med Linux-baserade Hadoop i HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Lär dig hur du använder den [Apache Mahout](http://mahout.apache.org) machine learning-biblioteket med Azure HDInsight för att generera filmrekommendationer.

Mahout är en [maskininlärning] [ ml] -biblioteket för Apache Hadoop. Mahout innehåller algoritmer för bearbetning av data, till exempel filtrering, klassificering, och klustring. I den här artikeln använder du en rekommendation motor för att generera filmrekommendationer som baseras på dina vänner har sett filmer.

## <a name="prerequisites"></a>Förutsättningar

* Ett Linux-baserat HDInsight-kluster. Information om hur du skapar en finns [komma igång med Linux-baserade Hadoop i HDInsight][getstarted].

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* En SSH-klient. Mer information finns i dokumentet [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="mahout-versioning"></a>Mahout versionshantering

Mer information om versionen av Mahout i HDInsight finns [HDInsight versioner och Hadoop-komponenter](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Förstå rekommendationer

En av de funktioner som tillhandahålls av Mahout är en rekommendation motor. Den här motorn accepterar data i formatet `userID`, `itemId`, och `prefValue` (inställningar för artikeln). Mahout kan sedan utföra samtidigt förekomsten analysen för att avgöra: *användare som har en inställning för ett objekt har också en inställning för dessa andra objekt*. Mahout bestämmer användare med liknande objekt inställningar som kan användas för att ge rekommendationer.

Följande arbetsflöde är en förenklad exempel som använder filmdata:

* **Samtidigt förekomsten**: Joe Alice och Bob alla tyckte *Star krig*, *i Empire strejker tillbaka*, och *tillbaka Jedi*. Mahout anger att användare också som en av dessa filmer som de andra två.

* **Samtidigt förekomsten**: Bob och Alice också tyckte *i Phantom hot*, *Attack av klonerna*, och *Revenge av Sith*. Mahout anger att användare som även gillade föregående tre filmer som dessa tre filmer.

* **Likhet rekommendation**: Joe eftersom tyckte om de första tre filmerna, Mahout tittar på filmer som andra med liknande inställningar tyckte om, men Johan har inte bevakade (tyckte/klassificerad). I det här fallet Mahout rekommenderar *i Phantom hot*, *Attack av klonerna*, och *Revenge av Sith*.

### <a name="understanding-the-data"></a>Förstå data

Enkelt, [GroupLens Research] [ movielens] innehåller klassificering av data för filmer i ett format som är kompatibel med Mahout. Dessa data är tillgängliga på ditt kluster standardlagring på `/HdiSamples/HdiSamples/MahoutMovieData`.

Det finns två filer, `moviedb.txt` och `user-ratings.txt`. Den `user-ratings.txt` filen som ska användas vid analys. Den `moviedb.txt` används för att ange användarvänliga textinformation när du visar resultaten.

Data i ratings.txt användare har en struktur med `userID`, `movieID`, `userRating`, och `timestamp`, vilket anger hur mycket en film klassificerats som varje användare. Här är ett exempel på data:

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Analys körs

Från en SSH-anslutning till klustret använder du följande kommando för att köra jobbet rekommendation:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]
> Jobbet kan ta flera minuter att slutföra, och kan köra flera MapReduce-jobb.

## <a name="view-the-output"></a>Visa utdata

1. När jobbet har slutförts använder du följande kommando visa genererade utdata:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    Utdata visas på följande sätt:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    Den första kolumnen är den `userID`. Värdena i ' [' och ']' är `movieId`:`recommendationScore`.

2. Du kan använda utdata, tillsammans med moviedb.txt, för att ge mer information om rekommendationer. Kopierar du filerna lokalt med hjälp av följande kommandon:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Det här kommandot kopierar utdata till en fil med namnet **recommendations.txt** i den aktuella katalogen, tillsammans med film datafiler.

3. Använder du följande kommando för att skapa en Python-skriptet som slår upp film namn för data i rekommendationer:

    ```bash
    nano show_recommendations.py
    ```

    När det öppnas redigeraren, Använd följande text som innehållet i filen:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Tryck på **Ctrl + X**, **Y**, och slutligen **RETUR** att spara data.

4. Kör skriptet Python. Följande kommando förutsätter att du är i katalogen där alla filer som hämtades:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Detta kommando kontrollerar de rekommendationer som genererats för användaren ID 4.

    * Den **användare ratings.txt** används för att hämta filmer som har bedömts.

    * Den **moviedb.txt** används för att hämta namnen på filmer.

    * Den **recommendations.txt** används för att hämta filmrekommendationer för den här användaren.

     Utdata från kommandot liknar följande:

        Sju år i Tibet (1997), poängsätta = 5.0 Indiana brev och den senaste Crusade (1989) poängsätta = 5.0 Jaws (1975) poäng = 5.0 mening och känseln (1995) poäng = 5.0 oberoende dag (ID4) (1996), poäng = 5.0 min bästa vän Wedding (1997) poängsätta = 5.0 Jerry Maguire (1996) poäng = 5.0 Scream 2 (1997), poäng = 5.0 tid att Kill, (1996), poängsätta = 5.0

## <a name="delete-temporary-data"></a>Ta bort temporära data

Mahout jobb ta inte bort tillfälliga data som har skapats under bearbetning av jobbet. Den `--tempDir` parameter har angetts i exempel jobbet att isolera de temporära filerna i en specifik sökväg för enkelt borttagning. Om du vill ta bort temporära filer, använder du följande kommando:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]
> Om du vill köra kommandot igen måste du också ta bort den angivna katalogen. Använd följande för att ta bort den här katalogen:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Mahout, identifiera andra sätt att arbeta med data i HDInsight:

* [Hive med HDInsight](hdinsight-use-hive.md)
* [Pig med HDInsight](hdinsight-use-pig.md)
* [MapReduce med HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
