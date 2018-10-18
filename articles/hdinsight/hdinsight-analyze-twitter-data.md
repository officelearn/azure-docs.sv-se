---
title: Analysera Twitter-data med Hadoop i HDInsight - Azure
description: Lär dig hur du använder Hive för att analysera Twitter-data med Hadoop i HDInsight för att hitta användningsfrekvenser för ett visst ord.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 294353cfcfba617ab19e703f11f35402bcf7ea82
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49387589"
---
# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analysera Twitter-data med Hive i HDInsight
Sociala webbplatser är en av större Drivande faktorer för big data-införande. Offentliga API: er som tillhandahålls av webbplatser som Twitter är en bra källa till data för att analysera och förstå populära trender.
I de här självstudierna kommer du få tweets med ett Twitter API för strömning och sedan använda Apache Hive på Azure HDInsight till att hämta en lista över Twitter-användare som skickade de flesta tweets som innehåller ett visst ord.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett Windows-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Anvisningar om specifika till ett Linux-baserade kluster finns i [analysera Twitter-data med Hive i HDInsight (Linux)](hdinsight-analyze-twitter-data-linux.md).

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar de här självstudierna måste du ha:

* **En arbetsstation** med Azure PowerShell installerad och konfigurerad.

    För att köra Windows PowerShell-skript, måste du köra Azure PowerShell som administratör och ange körningsprincipen till *RemoteSigned*. Se [kör Windows PowerShell-skript][powershell-script].

    Innan du kör Windows PowerShell-skript måste du kontrollera att du är ansluten till din Azure-prenumeration med hjälp av följande cmdlet:

    ```powershell
    Connect-AzureRmAccount
    ```

    Om du har flera Azure-prenumerationer, kan du använda följande cmdlet för att ställa in den aktuella prenumerationen:

    ```powershell
    Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>
    ```

    > [!IMPORTANT]
    > Azure PowerShell-stöd för hantering av HDInsight-resurser med hjälp av Azure Service Manager **är föråldrat** och togs bort den 1 januari 2017. I stegen i det här dokumentet används de nya HDInsight-cmdletarna som fungerar med Azure Resource Manager.
    >
    > Följ stegen i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs) för att installera den senaste versionen av Azure PowerShell. Om du har skript som behöver ändras för att använda de nya cmdletarna som fungerar med Azure Resource Manager, hittar du mer information i [Migrera till Azure Resource Manager-baserade utvecklingsverktyg för HDInsight-kluster](hdinsight-hadoop-development-using-azure-resource-manager.md).

* **Ett Azure HDInsight-kluster**. Anvisningar för att etablera klustret finns i [komma igång med HDInsight] [ hdinsight-get-started] eller [etablera HDInsight-kluster][hdinsight-provision]. Klusternamnet måste senare under kursen.

I följande tabell visas de filer som används i den här självstudien:

| Filer | Beskrivning |
| --- | --- |
| /tutorials/Twitter/data/tweets.txt |Hive-jobb i källdata. |
| /tutorials/Twitter/Output |Utdatamappen för Hive-jobb. Hive-jobb utdata Standardfilnamnet är **000000_0**. |
| tutorials/Twitter/Twitter.hql |Skriptfilen HiveQL. |
| /tutorials/Twitter/JobStatus |Hadoop jobbets status. |

## <a name="get-twitter-feed"></a>Hämta Twitter-flöde
I den här självstudien använder du den [Twitter-API: er för direktuppspelning][twitter-streaming-api]. Specifika Twitter strömmande API som du använder är [statusar/filter][twitter-statuses-filter].

> [!NOTE]
> En fil som innehåller 10 000 tweets och Hive-skriptfilen (beskrivs i nästa avsnitt) har laddats upp i en offentlig blobbehållare. Du kan hoppa över det här avsnittet om du vill använda de uppladdade filerna.

Tweets data lagras i JavaScript Object Notation (JSON)-format som innehåller en komplex kapslade struktur. Istället för att skriva många rader med kod med hjälp av ett vanliga programmeringsspråk, kan du omvandla den här kapslade strukturen i en Hive-tabell, så att den kan ta emot av en SQL Structured Query Language ()-likt språk som kallas HiveQL.

Twitter använder OAuth för att tillhandahålla auktoriserad åtkomst till dess API. OAuth är ett autentiseringsprotokoll som tillåter användare att godkänna program så att den fungerar för deras räkning utan att dela sina lösenord. Mer information finns på [oauth.net](http://oauth.net/) eller i den utmärkt [Nybörjarguide till OAuth](http://hueniverse.com/oauth/) från Hueniverse.

Det första steget att använda OAuth är att skapa ett nytt program i Twitter-utvecklarwebbplatsen.

**Skapa ett Twitter-program**

1. Logga in på [https://apps.twitter.com/](https://apps.twitter.com/). Klicka på den **registrera dig nu** länkar om du inte har ett Twitter-konto.
2. Klicka på **Skapa ny App**.
3. Ange **namn**, **beskrivning**, **webbplats**. Du kan göra upp en URL för den **webbplats** fält. I följande tabell visas några exempelvärden som ska användas:

   | Fält | Värde |
   | --- | --- |
   |  Namn |MyHDInsightApp |
   |  Beskrivning |MyHDInsightApp |
   |  Webbplats |http://www.myhdinsightapp.com |
4. Kontrollera **Ja, jag godkänner**, och klicka sedan på **skapa ditt Twitter-program**.
5. Klicka på den **behörigheter** fliken. Standardbehörigheten är **skrivskyddad**. Det här är tillräcklig för den här självstudiekursen.
6. Klicka på den **nycklar och åtkomsttoken** fliken.
7. Klicka på **Skapa min åtkomsttoken**.
8. Klicka på **Test OAuth** i det övre högra hörnet på sidan.
9. Anteckna **använda nyckeln**, **konsumenthemligheten**, **åtkomsttoken**, och **åtkomsttokenhemligheten**. Du behöver värdena senare under kursen.

I den här självstudien använder du Windows PowerShell för att göra-webbtjänstanrop. Det andra populära verktyget för att göra de tjänstanrop för web är [ *Curl*][curl]. CURL kan laddas ned från [här][curl-download].

> [!NOTE]
> När du använder curl-kommando i Windows, Använd dubbla citattecken i stället för enkla citattecken för värden för alternativ.

**Att hämta tweets**

1. Öppna Windows-PowerShell Integrated Scripting Environment (ISE). (På startskärmen i Windows 8, skriver **PowerShell_ISE** och klicka sedan på **Windows PowerShell ISE**. Se [starta Windows PowerShell i Windows 8 och Windows](https://docs.microsoft.com/powershell/scripting/setup/starting-windows-powershell?view=powershell-6)
2. Kopiera följande skript i skriptfönstret:

    ```powershell
    #region - variables and constants
    $clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

    # Enter the OAuth information for your Twitter application
    $oauth_consumer_key = "<TwitterAppConsumerKey>";
    $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
    $oauth_token = "<TwitterAppAccessToken>";
    $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

    $destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

    $trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
    $track = [System.Uri]::EscapeDataString($trackString);
    $lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
    #endregion

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    Connect-AzureRmAccount
    #endregion

    #region - Create a block blob object for writing tweets into Blob storage
    Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $containerName = $myCluster.DefaultStorageContainer
    Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

    Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
    Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

    Write-Host "Create block blob object ..." -ForegroundColor Green
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($containerName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
    #end region

    # region - Format OAuth strings
    Write-Host "Format oauth strings ..." -ForegroundColor Green
    $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
    $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
    $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

    $signature = "POST&";
    $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
    $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
    $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
    $signature += [System.Uri]::EscapeDataString("track=" + $track);

    $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

    $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
    $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
    $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

    $oauth_authorization = 'OAuth ';
    $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
    $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
    $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
    $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
    $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
    $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
    $oauth_authorization += 'oauth_version="1.0"';

    $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
    #endregion

    #region - Read tweets
    Write-Host "Create HTTP web request ..." -ForegroundColor Green
    [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
    $request.Method = "POST";
    $request.Headers.Add("Authorization", $oauth_authorization);
    $request.ContentType = "application/x-www-form-urlencoded";
    $body = $request.GetRequestStream();

    $body.write($post_body, 0, $post_body.length);
    $body.flush();
    $body.close();
    $response = $request.GetResponse() ;

    Write-Host "Start stream reading ..." -ForegroundColor Green

    Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream

    $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

    $inrec = $sReader.ReadLine()
    $count = 0
    while (($inrec -ne $null) -and ($count -le $lineMax))
    {
        if ($inrec -ne "")
        {
            Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

            $writeStream.WriteLine($inrec)
            $count ++
        }

        $inrec=$sReader.ReadLine()
    }
    #endregion

    #region - Write tweets to Blob storage
    Write-Host "Write to the destination blob ..." -ForegroundColor Green
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)

    $sReader.close()
    #endregion

    Write-Host "Completed!" -ForegroundColor Green
    ```

3. Ange första fem till åtta variabler i skriptet:

    Variabel|Beskrivning
    ---|---
    $clusterName|Det här är namnet på HDInsight-klustret där du vill köra programmet.
    $oauth_consumer_key|Detta är ett Twitter-program **använda nyckeln** du skrev ned tidigare när du skapade Twitter-programmet.
    $oauth_consumer_secret|Detta är ett Twitter-program **konsumenthemligheten** du skrev ned tidigare.
    $oauth_token|Detta är ett Twitter-program **åtkomsttoken** du skrev ned tidigare.
    $oauth_token_secret|Detta är ett Twitter-program **åtkomsttokenhemligheten** du skrev ned tidigare.
    $destBlobName|Det här är utdata-blob-namnet. Standardvärdet är **tutorials/twitter/data/tweets.txt**. Om du ändrar standardvärdet, behöver du uppdatera Windows PowerShell-skript på samma sätt.
    $trackString|Webbtjänsten returnerar tweets som rör dessa nyckelord. Standardvärdet är **Azure, molnet, HDInsight**. Om du ändrar standardvärdet uppdateras Windows PowerShell-skript på lämpligt sätt.
    $lineMax|Värdet anger hur många tweets som skriptet läser. Det tar cirka tre minuter för att läsa 100 tweets. Du kan ange fler, men det tar längre tid att hämta.

1. Tryck **F5** för att köra skriptet. Om du stöter på problem, som en lösning kan du markera alla rader och tryck sedan på **F8**.
2. Du bör se ”Slutför”! i slutet av utdata. Eventuella felmeddelanden visas i rött.

Du kan kontrollera utdatafilen, som en valideringsproceduren **/tutorials/twitter/data/tweets.txt**, på Azure Blob storage med hjälp av en Azure storage explorer eller Azure PowerShell. En Windows PowerShell-exempelskript för att lista filer, se [använda Blob storage med HDInsight][hdinsight-storage-powershell].

## <a name="create-hiveql-script"></a>Skapa HiveQL-skript
Med Azure PowerShell kan du köra flera HiveQL-instruktioner en i taget eller paketera HiveQL-instruktionen i en skriptfil. I de här självstudierna skapar du en HiveQL-skript. Skriptfilen måste överföras till Azure Blob storage. I nästa avsnitt ska du köra skriptfilen med hjälp av Azure PowerShell.

> [!NOTE]
> Hive-skriptfilen och en fil som innehåller 10 000 tweets har laddats upp i en offentlig blobbehållare. Du kan hoppa över det här avsnittet om du vill använda de uppladdade filerna.

HiveQL-skript kommer att utföra följande:

1. **Ta bort tabellen tweets_raw** om tabellen redan finns.
2. **Skapa Hive-tabell tweets_raw**. Den här tillfälliga strukturerade Hive-tabellen innehåller data för ytterligare extrahering, transformering och inläsning (ETL) bearbetning. Information om partitioner finns [Hive självstudien][apache-hive-tutorial].
3. **Läsa in data** från källmappen /tutorials/twitter/data. Stora tweets datauppsättningen i kapslad JSON-format har nu tagits omvandlas till en tillfällig Hive-tabellstruktur.
4. **Ta bort tabellen tweets** om tabellen redan finns.
5. **Skapa tabellen tweets**. Innan du kan fråga mot datauppsättningen tweets med hjälp av Hive, måste du köra en annan ETL-processen. Den här ETL-processen definierar en mer detaljerad tabellschemat för de data som du har lagrat i tabellen ”twitter_raw”.
6. **Infoga överskrivning tabell**. Det här komplexa Hive-skriptet startar en uppsättning lång MapReduce-jobb med Hadoop-kluster. Detta kan ta cirka 10 minuter beroende på din datauppsättning och storleken på ditt kluster.
7. **Infoga skriva över katalogen**. Kör en fråga och utdatauppsättning till en fil. Den här frågan returnerar en lista över Twitter-användare som skickade de flesta tweets som innehåller ordet ”Azure”.

**Skapa en Hive-skript och överför det till Azure**

1. Öppna Windows PowerShell ISE.
2. Kopiera följande skript i skriptfönstret:

    ```powershell
    #region - variables and constants
    $clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
    $subscriptionID = "<Azure Subscription ID>"

    $sourceDataPath = "/tutorials/twitter/data"
    $outputPath = "/tutorials/twitter/output"
    $hqlScriptFile = "tutorials/twitter/twitter.hql"

    $hqlStatements = @"
    set hive.exec.dynamic.partition = true;
    set hive.exec.dynamic.partition.mode = nonstrict;

    DROP TABLE tweets_raw;
    CREATE EXTERNAL TABLE tweets_raw (
        json_response STRING
    )
    STORED AS TEXTFILE LOCATION '$sourceDataPath';

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

    FROM tweets_raw
    INSERT OVERWRITE TABLE tweets
    SELECT
        cast(get_json_object(json_response, '$.id_str') as BIGINT),
        get_json_object(json_response, '$.created_at'),
        concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
        substr (get_json_object(json_response, '$.created_at'),27,4)),
        substr (get_json_object(json_response, '$.created_at'),27,4),
        case substr (get_json_object(json_response, '$.created_at'),5,3)
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

    INSERT OVERWRITE DIRECTORY '$outputPath'
    SELECT name, screen_name, count(1) as cc
        FROM tweets
        WHERE text like "%Azure%"
        GROUP BY name,screen_name
        ORDER BY cc DESC LIMIT 10;
    "@
    #endregion

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green

    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Connect-AzureRmAccount
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    #endregion

    #region - Create a block blob object for writing the Hive script file
    Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow

    Write-Host "Define the connection string ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

    Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
    $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)

    Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream
    $writeStream.Writeline($hqlStatements)
    #endregion

    #region - Write the Hive script file to Blob storage
    Write-Host "Write to the destination blob ... " -ForegroundColor Green
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $hqlScriptBlob.UploadFromStream($memStream)
    #endregion

    Write-Host "Completed!" -ForegroundColor Green
    ```

3. Ange de första två variablerna i skriptet:

   | Variabel | Beskrivning |
   | --- | --- |
   |  $clusterName |Ange HDInsight-klusternamnet där du vill köra programmet. |
   |  $subscriptionID |Ange ditt Azure-prenumeration-ID. |
   |  $sourceDataPath |Azure Blob storage plats där Hive-frågor kommer att läsa data från. Du behöver inte ändra den här variabeln. |
   |  $outputPath |Azure Blob storage plats där Hive-frågor kommer matar ut resultaten. Du behöver inte ändra den här variabeln. |
   |  $hqlScriptFile |Sökvägen och filnamnet för filen HiveQL-skript. Du behöver inte ändra den här variabeln. |
4. Tryck **F5** för att köra skriptet. Om du stöter på problem, som en lösning kan du markera alla rader och tryck sedan på **F8**.
5. Du bör se ”Slutför”! i slutet av utdata. Eventuella felmeddelanden visas i rött.

Du kan kontrollera utdatafilen, som en valideringsproceduren **/tutorials/twitter/twitter.hql**, på Azure Blob storage med hjälp av en Azure storage explorer eller Azure PowerShell. En Windows PowerShell-exempelskript för att lista filer, se [använda Blob storage med HDInsight][hdinsight-storage-powershell].

## <a name="process-twitter-data-by-using-hive"></a>Bearbeta Twitter-data med hjälp av Hive
Du är klar med alla förberedelse. Nu har du anropar Hive-skriptet och kontrollera resultaten.

### <a name="submit-a-hive-job"></a>Skicka ett Hive-jobb
Du kan använda följande Windows PowerShell-skript för att köra Hive-skriptet. Du behöver du ange den första variabeln.

> [!NOTE]
> Om du vill använda tweets och HiveQL-skript som du laddade upp i de sista två avsnitten, inställd $hqlScriptFile ”/ tutorials/twitter/twitter.hql”. Om du vill använda de som har överförts till en offentlig blob du inställd $hqlScriptFile ”wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql”.

```powershell
#region variables and constants
$clusterName = "<Existing Azure HDInsight Cluster Name>"
$httpUserName = "admin"
$httpUserPassword = "<HDInsight Cluster HTTP User Password>"

#use one of the following
$hqlScriptFile = "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
$hqlScriptFile = "/tutorials/twitter/twitter.hql"

$statusFolder = "/tutorials/twitter/jobstatus"
#endregion

$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $myCluster.ResourceGroup
$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value

$defaultBlobContainerName = $myCluster.DefaultStorageContainer

#region - Invoke Hive
Write-Host "Invoke Hive ... " -ForegroundColor Green

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential
$response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable

Write-Host "Display the standard error log ... " -ForegroundColor Green
$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace �\s*$� -replace �.*\s�
Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
#endregion
```

### <a name="check-the-results"></a>Kontrollera resultaten
Du kan använda följande Windows PowerShell-skript för att kontrollera utdata för Hive-jobb. Du måste ange de första två variablerna.

```powershell
#region variables and constants
$clusterName = "<Existing Azure HDInsight Cluster Name>"

$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
#endregion

#region - Create an Azure storage context object
Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $myCluster.ResourceGroup
$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
$defaultBlobContainerName = $myCluster.DefaultStorageContainer

Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow

Write-Host "Create a context object ... " -ForegroundColor Green
$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
#endregion

#region - Download blob and display blob
Write-Host "Download the blob ..." -ForegroundColor Green
cd $HOME
Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force

Write-Host "Display the output ..." -ForegroundColor Green
Write-Host "==================================" -ForegroundColor Green
cat "./$blob"
Write-Host "==================================" -ForegroundColor Green
#end region
```

> [!NOTE]
> Hive-tabell använder \001 som fältavgränsaren. Avgränsaren visas inte i utdata.

När analysresultaten har placerats i Azure Blob storage, kan du exportera data till en Azure SQL database/SQL server, exportera data till Excel med Power Query eller Anslut ditt program till data med hjälp av Hive ODBC-drivrutinen. Mer information finns i [Använd Sqoop med HDInsight][hdinsight-use-sqoop], [analysera flygförseningsdata med HDInsight][hdinsight-analyze-flight-delay-data], [ Ansluta Excel till HDInsight med Power Query][hdinsight-power-query], och [ansluta Excel till HDInsight med Microsoft Hive ODBC-drivrutin][hdinsight-hive-odbc].

## <a name="next-steps"></a>Nästa steg
I den här självstudien har vi sett hur du kan omvandla en Ostrukturerade JSON-datauppsättning till en strukturerade Hive-tabell för att fråga, utforska och analysera data från Twitter med hjälp av HDInsight på Azure. Du kan läsa mer här:

* [Kom igång med HDInsight][hdinsight-get-started]
* [Analysera flygförseningsdata med HDInsight][hdinsight-analyze-flight-delay-data]
* [Ansluta Excel till HDInsight med Power Query][hdinsight-power-query]
* [Ansluta Excel till HDInsight med Microsoft Hive ODBC-drivrutin][hdinsight-hive-odbc]
* [Använda Sqoop med HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://developer.twitter.com/en/docs/api-reference-index
[twitter-statuses-filter]: https://developer.twitter.com/en/docs/tweets/filter-realtime/api-reference/post-statuses-filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]:hdinsight-hadoop-use-blob-storage.md#access-blobs-using-azure-powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-power-query]:hadoop/apache-hadoop-connect-excel-power-query.md
[hdinsight-hive-odbc]:hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md
