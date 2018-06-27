---
title: Analysera Twitter-data med Apache Hive - Azure HDInsight | Microsoft Docs
description: Lär dig använda Hive och Hadoop i HDInsight för att omvandla TWitter rådata till en sökbar Hive-tabell.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1e249ed-5f57-40d6-b3bc-a1b4d9a871d3
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: f48aa68838ff8cac0119f66f168fce6008d388bc
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36959030"
---
# <a name="analyze-twitter-data-using-hive-and-hadoop-on-hdinsight"></a>Analysera Twitter-data med Hive och Hadoop på HDInsight

Lär dig hur du använder Apache Hive att processen Twitter-data. Resultatet är en lista över Twitter-användare som skickade de flesta tweets som innehåller ett visst ord.

> [!IMPORTANT]
> Stegen i det här dokumentet har testats på HDInsight 3,6.
>
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="get-the-data"></a>Hämta data

Twitter kan du hämta data för varje tweet som JavaScript Object Notation (JSON) dokument via ett REST-API. [OAuth](http://oauth.net) krävs för autentisering-API: et.

### <a name="create-a-twitter-application"></a>Skapa ett Twitter-program

1. Logga in från en webbläsare på [ https://apps.twitter.com/ ](https://apps.twitter.com/). Klicka på den **registrering nu** länk om du inte har ett Twitter-konto.

2. Klicka på **Skapa ny App**.

3. Ange **namn**, **beskrivning**, **webbplats**. Du kan göra upp en URL för den **webbplats** fältet. I följande tabell visas några exempelvärden som ska användas:

   | Fält | Värde |
   |:--- |:--- |
   | Namn |MyHDInsightApp |
   | Beskrivning |MyHDInsightApp |
   | Webbplats |http://www.myhdinsightapp.com |

4. Kontrollera **Ja, jag godkänner**, och klicka sedan på **skapa programmet Twitter**.

5. Klicka på den **behörigheter** fliken. Standardbehörigheten är **skrivskyddad**.

6. Klicka på den **nycklar och åtkomst-token** fliken.

7. Klicka på **skapa åtkomst-token**.

8. Klicka på **Test OAuth** i det övre högra hörnet på sidan.

9. Skriv ned **konsumenten nyckeln**, **konsumenthemlighet**, **åtkomsttoken**, och **åtkomst-token hemlighet**.

### <a name="download-tweets"></a>Hämta tweets

Följande Python-kod hämtar 10 000 tweets från Twitter och spara dem till en fil med namnet **tweets.txt**.

> [!NOTE]
> Följande steg utförs på HDInsight-klustret eftersom Python redan är installerad.

1. Anslut till HDInsight-klustret med hjälp av SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

3. Använd följande kommandon för att installera [Tweepy](http://www.tweepy.org/), [Progressbar](https://pypi.python.org/pypi/progressbar/2.2), och andra nödvändiga paket:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

4. Använd följande kommando för att skapa en fil med namnet **gettweets.py**:

   ```bash
   nano gettweets.py
   ```

5. Använd följande text som innehållet i den **gettweets.py** fil:

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=10000

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!IMPORTANT]
    > Ersätt platshållaren för följande objekt med information från ditt twitter-program:
    >
    > * `consumer_secret`
    > * `consumer_key`
    > * `access_token`
    > * `access_token_secret`

    > [!TIP]
    > Justera filtret avsnitt på den sista raden att spåra populära nyckelord. Med hjälp av nyckelord populära när du kör skriptet ger snabbare insamlingen av data.

6. Använd **Ctrl + X**, sedan **Y** att spara filen.

7. Använd följande kommando för att köra filen och hämta tweets:

    ```bash
    python gettweets.py
    ```

    En förloppsindikator. Räknar upp till 100% som tweets hämtas.

   > [!NOTE]
   > Om det tar lång tid för förloppsindikatorn att avancera, bör du ändra filtret för att spåra trender avsnitt. När det finns många tweets om ämnet i filtret, kan du snabbt få 10000 tweets behövs.

### <a name="upload-the-data"></a>Överföra data

Använd följande kommandon för att överföra data till HDInsight lagring:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Dessa kommandon lagra data på en plats som har åtkomst till alla noder i klustret.

## <a name="run-the-hiveql-job"></a>Kör jobb för HiveQL

1. Använd följande kommando för att skapa en fil som innehåller HiveQL-instruktioner:

   ```bash
   nano twitter.hql
   ```

    Använd följande text som innehållet i filen:

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
           when "Jan" then "01"
           when "Feb" then "02"
           when "Mar" then "03"
           when "Apr" then "04"
           when "May" then "05"
           when "Jun" then "06"
           when "Jul" then "07"
           when "Aug" then "08"
           when "Sep" then "09"
           when "Oct" then "10"
           when "Nov" then "11"
           when "Dec" then "12" end,
       substr (get_json_object(json_response, '$.created_at'),9,2),
       substr (get_json_object(json_response, '$.created_at'),12,8),
       get_json_object(json_response, '$.in_reply_to_user_id_str'),
       get_json_object(json_response, '$.text'),
       get_json_object(json_response, '$.contributors'),
       get_json_object(json_response, '$.retweeted'),
       get_json_object(json_response, '$.truncated'),
       get_json_object(json_response, '$.coordinates'),
       get_json_object(json_response, '$.source'),
       cast (get_json_object(json_response, '$.retweet_count') as INT),
       get_json_object(json_response, '$.entities.display_url'),
       array(
           trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
       array(
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
       trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
       trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
       get_json_object(json_response, '$.user.screen_name'),
       get_json_object(json_response, '$.user.name'),
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

2. Tryck på **Ctrl + X**, tryck på **Y** att spara filen.
3. Använd följande kommando för att köra HiveQL i filen:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Det här kommandot Kör den den **twitter.hql** fil. När frågan har slutförts kan du se en `jdbc:hive2//localhost:10001/>` prompt.

4. Använd följande fråga för att verifiera att data importerades från beeline-prompten:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Den här frågan returnerar högst 10 tweets som innehåller ordet **Azure** i meddelandetexten.

    > [!NOTE]
    > Om du har ändrat filter i den `gettweets.py` skript genom att ersätta **Azure** med ett av filtren som du använde.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du omvandlar en ostrukturerad datauppsättning JSON till en strukturerad Hive-tabell. Mer information om Hive i HDInsight finns i följande dokument:

* [Komma igång med HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Analysera svarta fördröjning data med HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
