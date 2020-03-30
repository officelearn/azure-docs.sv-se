---
title: Generera rekommendationer med Apache Mahout i Azure HDInsight
description: Lär dig hur du använder Apache Mahout maskininlärningsbibliotek för att generera filmrekommendationer med HDInsight (Hadoop).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/03/2020
ms.openlocfilehash: 33110e9f1d45fcd11e5f4cad1b589ab929a9472d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767644"
---
# <a name="generate-movie-recommendations-using-apache-mahout-with-apache-hadoop-in-hdinsight-ssh"></a>Generera filmrekommendationer med Apache Mahout med Apache Hadoop i HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Lär dig hur du använder Apache Mahout-maskininlärningsbiblioteket med Azure HDInsight för att generera filmrekommendationer. [Apache Mahout](https://mahout.apache.org)

Mahout är ett [maskininlärningsbibliotek](https://en.wikipedia.org/wiki/Machine_learning) för Apache Hadoop. Mahout innehåller algoritmer för bearbetning av data, till exempel filtrering, klassificering och klustring. I den här artikeln använder du en rekommendationsmotor för att generera filmrekommendationer som baseras på filmer som dina vänner har sett.

## <a name="prerequisites"></a>Krav

En Apache Hadoop kluster på HDInsight. Se [Komma igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="apache-mahout-versioning"></a>Apache Mahout-versionshantering

Mer information om versionen av Mahout i HDInsight finns i [HDInsight-versioner och Apache Hadoop-komponenter](../hdinsight-component-versioning.md).

## <a name="understanding-recommendations"></a>Förstå rekommendationer

En av de funktioner som tillhandahålls av Mahout är en rekommendation motor. Den här motorn accepterar `userID`data `itemId`i `prefValue` formatet , och (inställningen för objektet). Mahout kan sedan utföra analys av samtidig förekomst för att avgöra: *användare som har en preferens för ett objekt har också en preferens för dessa andra objekt*. Mahout bestämmer sedan användare med inställningar för liknande objekt, som kan användas för att ge rekommendationer.

Följande arbetsflöde är ett förenklat exempel som använder filmdata:

* **Co-händelse:** Joe, Alice och Bob alla gillade *Star Wars*, The Empire *Strikes Back*, och Return of *the Jedi*. Mahout bestämmer att användare som gillar någon av dessa filmer också gillar de andra två.

* **Co-occurrence**: Bob och Alice gillade också *The Phantom Menace*, Attack of the *Clones*, och *Revenge of the Sith*. Mahout bestämmer att användare som gillade de tre föregående filmerna också gillar dessa tre filmer.

* **Likhet rekommendation:** Eftersom Joe gillade de tre första filmerna, mahout tittar på filmer som andra med liknande preferenser gillade, men Joe har inte sett (gillade / betygsatt). I det här fallet rekommenderar Mahout *The Phantom Menace*, Attack of the *Clones*och *Revenge of the Sith*.

### <a name="understanding-the-data"></a>Förstå data

[GroupLens Research](https://grouplens.org/datasets/movielens/) tillhandahåller klassificeringsdata för filmer i ett format som är kompatibelt med Mahout. Dessa data är tillgängliga i klustrets standardlagring på `/HdiSamples/HdiSamples/MahoutMovieData`.

Det finns två `moviedb.txt` `user-ratings.txt`filer, och . Filen `user-ratings.txt` används under analysen. Den `moviedb.txt` används för att ge användarvänlig textinformation när du visar resultaten.

De data som `user-ratings.txt` finns i `userID` `movieID`har `userRating`en `timestamp`struktur med , , och , som anger hur högt varje användare betygsatt en film. Här är ett exempel på data:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Kör analysen

1. Använd kommandot ssh för att ansluta till [klustret.](../hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Använd följande kommando för att köra rekommendationsjobbet:

    ```bash
    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
    ```

> [!NOTE]  
> Jobbet kan ta flera minuter att slutföra och kan köra flera MapReduce-jobb.

## <a name="view-the-output"></a>Visa utdata

1. När jobbet är klart använder du följande kommando för att visa den genererade utdata:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    Utdata visas på följande sätt:

    ```output
    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
    ```

    Den första kolumnen `userID`är . Värdena i "[" och ']' är `movieId`:`recommendationScore`.

2. Du kan använda utdata, tillsammans med moviedb.txt, för att ge mer information om rekommendationerna. Kopiera först filerna lokalt med följande kommandon:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Det här kommandot kopierar utdata till en fil med namnet **recommendations.txt** i den aktuella katalogen, tillsammans med filmdatafilerna.

3. Använd följande kommando för att skapa ett Python-skript som slår upp filmnamn för data i rekommendationerutdata:

    ```bash
    nano show_recommendations.py
    ```

    När redigeraren öppnas använder du följande text som innehållet i filen:

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

    Tryck på **Ctrl-X**, **Y**och **slutligen Retur** för att spara data.

4. Kör Python-skriptet. Följande kommando förutsätter att du är i katalogen där alla filer hämtades:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Det här kommandot tittar på de rekommendationer som genereras för användar-ID 4.

   * **Filen user-ratings.txt** används för att hämta filmer som har klassificerats.

   * Filen **moviedb.txt** används för att hämta namnen på filmerna.

   * **Recommendations.txt** används för att hämta filmrekommendationerna för den här användaren.

     Utdata från det här kommandot liknar följande text:

        ```output
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        ```

## <a name="delete-temporary-data"></a>Ta bort tillfälliga data

Mahout-jobb tar inte bort tillfälliga data som skapas när jobbet bearbetas. Parametern `--tempDir` anges i exempeljobbet för att isolera de temporära filerna till en viss sökväg för enkel borttagning. Om du vill ta bort temp-filerna använder du följande kommando:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Om du vill köra kommandot igen måste du också ta bort utdatakatalogen. Använd följande för att ta bort den här katalogen:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Mahout kan du upptäcka andra sätt att arbeta med data på HDInsight:

* [Apache Hive med HDInsight](hdinsight-use-hive.md)
* [MapReduce med HDInsight](hdinsight-use-mapreduce.md)
