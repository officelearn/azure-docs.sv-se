---
title: Skapa rekommendationer med Apache Mahout i Azure HDInsight
description: Lär dig hur du använder Apache Mahout Machine Learning-biblioteket för att skapa film rekommendationer med HDInsight (Hadoop).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 3923abd10fc3a64773d561b1f375f9e2f00a7e56
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044567"
---
# <a name="generate-movie-recommendations-using-apache-mahout-with-apache-hadoop-in-hdinsight-ssh"></a>Skapa film rekommendationer med Apache Mahout med Apache Hadoop i HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Lär dig hur du använder [Apache Mahout](https://mahout.apache.org) Machine Learning-biblioteket med Azure HDInsight för att skapa film rekommendationer.

Mahout är ett [maskin inlärnings](https://en.wikipedia.org/wiki/Machine_learning) bibliotek för Apache Hadoop. Mahout innehåller algoritmer för bearbetning av data, till exempel filtrering, klassificering och klustring. I den här artikeln använder du en rekommendations motor för att skapa film rekommendationer som baseras på filmer som dina vänner har sett.

## <a name="prerequisites"></a>Krav

* Ett Apache Hadoop kluster i HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="apache-mahout-versioning"></a>Apache Mahout-versioner

Mer information om versionen av Mahout i HDInsight finns i HDInsight- [versioner och Apache Hadoop-komponenter](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Förstå rekommendationer

En av de funktioner som tillhandahålls av Mahout är en rekommendations motor. Den här motorn accepterar data i formatet `userID`, `itemId`och `prefValue` (inställningen för objektet). Mahout kan sedan utföra analys av samförekomster för att fastställa: *användare som har en inställning för ett objekt har även prioritet för dessa andra objekt*. Mahout avgör sedan användare med liknande objekt inställningar som kan användas för att göra rekommendationer.

Följande arbets flöde är ett förenklat exempel som använder film data:

* **Co-förekomst**: Johan, Alice och Bob all gillade *krig*, Empire går *tillbaka*och *returnerar Jedi*. Mahout avgör att användare som gillar någon av dessa filmer också är de andra två.

* **Co-förekomst**: Bob och Alice gillade även *den fiktiva Menace*, *angrepp av kloner*och *Revenge för Sith*. Mahout avgör att användare som gillade de föregående tre filmerna även liknar dessa tre filmer.

* **Rekommendation för likhet**: eftersom Johan gillade de tre första filmerna, tittar Mahout på filmer som andra med liknande inställningar gillade, men Joe har inte tittat på (gilla/klassificerat). I det här fallet rekommenderar Mahout *den fiktiva Menace*, *angrepp av kloner*och *Revenge i Sith*.

### <a name="understanding-the-data"></a>Förstå data

[GroupLens Research](https://grouplens.org/datasets/movielens/) ger enkelt klassificerings data för filmer i ett format som är kompatibelt med Mahout. Dessa data är tillgängliga på klustrets standard lagring på `/HdiSamples/HdiSamples/MahoutMovieData`.

Det finns två filer, `moviedb.txt` och `user-ratings.txt`. `user-ratings.txt`-filen används under analysen. `moviedb.txt` används för att tillhandahålla användarvänlig textinformation när resultatet visas.

De data som finns i User-Ratings. txt har en struktur med `userID`, `movieID`, `userRating`och `timestamp`, vilket anger hur mycket varje användare betygsatte en film. Här är ett exempel på data:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Kör analysen

Använd följande kommando för att köra rekommendations jobbet från en SSH-anslutning till klustret:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]  
> Jobbet kan ta flera minuter att slutföra och kan köra flera MapReduce-jobb.

## <a name="view-the-output"></a>Visa utdata

1. När jobbet har slutförts använder du följande kommando för att visa genererade utdata:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    Utdata visas på följande sätt:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    Den första kolumnen är `userID`. Värdena i [och] är `movieId`:`recommendationScore`.

2. Du kan använda utdata tillsammans med MovieDB. txt för att få mer information om rekommendationerna. Kopiera först filerna lokalt med följande kommandon:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Detta kommando kopierar utdata till en fil med namnet **rekommendationer. txt** i den aktuella katalogen, tillsammans med film-datafilerna.

3. Använd följande kommando för att skapa ett Python-skript som söker efter film namn för data i resultatet av rekommendationerna:

    ```bash
    nano show_recommendations.py
    ```

    När redigeraren öppnas använder du följande text som filens innehåll:

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

    Tryck på **CTRL-X**, **Y**och slutligen **RETUR** för att spara data.

4. Kör python-skriptet. Följande kommando förutsätter att du är i katalogen där alla filer hämtades:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Det här kommandot kontrollerar rekommendationerna som genereras för användar-ID 4.

   * Filen **User-Ratings. txt** används för att hämta filmer som har klassificerats.

   * Filen **MovieDB. txt** används för att hämta namnen på filmerna.

   * **Rekommendationerna. txt** används för att hämta film rekommendationer för den här användaren.

     Utdata från det här kommandot liknar följande text:

       Sju år i Tibet (1997), score = 5.0 Indiana Jones och senaste Crusade (1989), score = 5.0 JAWS (1975), score = 5.0 Sense och sensibility (1995), score = 5,0 självständighets dag (ID4) (1996), score = 5.0 min bästa vän: s bröllop (1997), score = 5.0 Jerry Maguire (1996). Score = 5.0 Scream 2 (1997), poängen = 5.0 Time to Kill, A (1996), score = 5.0

## <a name="delete-temporary-data"></a>Ta bort temporära data

Mahout-jobb tar inte bort temporära data som skapas när jobbet bearbetas. Parametern `--tempDir` anges i exempel jobbet för att isolera de temporära filerna till en specifik sökväg för enkel borttagning. Använd följande kommando för att ta bort temporära filer:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Om du vill köra kommandot igen måste du också ta bort utdata-katalogen. Använd följande för att ta bort den här katalogen:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Mahout kan du upptäcka andra sätt att arbeta med data i HDInsight:

* [Apache Hive med HDInsight](hdinsight-use-hive.md)
* [Apache-gris med HDInsight](hdinsight-use-pig.md)
* [MapReduce med HDInsight](hdinsight-use-mapreduce.md)