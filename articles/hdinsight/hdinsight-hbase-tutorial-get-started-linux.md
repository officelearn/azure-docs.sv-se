---
title: "Komma igång med HBase i Azure HDInsight | Microsoft Docs"
description: "Följ denna HBase-självstudie för att komma igång med Apache HBase med Hadoop i HDInsight. Skapa tabeller från HBase-gränssnittet och ställ frågor för dem med Hive."
keywords: "apache hbase, hbase, hbase shell, självstudier för hbase"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 4d6a2658-6b19-4268-95ee-822890f5a33a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 21d8dff230e045607b70013f4eabf1bfe8ec3993
ms.lasthandoff: 03/25/2017


---
# <a name="hbase-tutorial-get-started-using-apache-hbase-in-hdinsight"></a>HBase-självstudiekurs: Komma igång med Apache HBase i HDInsight

Lär dig skapa ett HBase-kluster i HDInsight, skapa HBase-tabeller och frågetabeller med Hive. Allmän HBase-information finns i [HDInsight HBase-översikt][hdinsight-hbase-overview].

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Krav
Innan du påbörjar den här HBase-självstudien måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Secure Shell(SSH)](hdinsight-hadoop-linux-use-ssh-unix.md). 
* [curl](http://curl.haxx.se/download.html).

### <a name="access-control-requirements"></a>Åtkomstkontrollkrav
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Skapa HBase-kluster
Följande procedur använder en Azure Resource Manager-mall för att skapa ett version 3.4 Linux-baserat HBase-kluster och det beroende standardkontot för Azure Storage. Mer information om de parametrar som används i proceduren och andra metoder för att skapa kluster finns i [Skapa Linux-baserade Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Klicka på följande bild för att öppna mallen i Azure Portal. Mallen finns i en offentlig blob-behållare. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Från bladet **Anpassad distribution**, anger du följande:
   
   * **Prenumeration**: Välj den Azure-prenumeration som kommer användas för att skapa klustret.
   * **Resursgrupp**: Skapa en ny Azure Resource Management-grupp eller använd en befintlig.
   * **Plats**: Ange platsen för resursgruppen. 
   * **Klusternamn**: Ange ett namn för det HBase-kluster som du vill skapa.
   * **Klustrets inloggningsnamn och lösenord**: Inloggningsnamnet är som standard **admin**.
   * **SSH-användarnamn och lösenord**: Standardanvändarnamnet är **sshuser**.  Du kan byta namn.
     
     Andra parametrar är valfria.  
     
     Varje kluster är beroende av ett Azure Storage-konto. När du tar bort ett kluster stannar aktuella data kvar på lagringskontot. Klustrets lagringskonto av standardtyp har det klusternamn som omfattar tillägget ”store”. Det är hårdkodat i avsnittet för mallvariabler.
3. Välj **Jag godkänner villkoren som anges ovan** och klicka sedan på **Köp**. Det tar cirka 20 minuter att skapa ett kluster.

> [!NOTE]
> När ett HBase-kluster har tagits bort kan du skapa ett annat HBase-kluster med hjälp av samma standard-blob-behållare. Det nya klustret hämtar de HBase-tabeller som du skapade i det ursprungliga klustret. Om du vill undvika inkonsekvenser rekommenderar vi att du inaktiverar HBase-tabellerna innan du tar bort klustret.
> 
> 

## <a name="create-tables-and-insert-data"></a>Skapa tabeller och infoga data
Du kan använda SSH för att ansluta till HBase-kluster och sedan använda HBase Shell för att skapa HBase-tabeller, infoga data och ställa frågor till data. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

För de flesta visas data i tabellformat:

![HDInsight HBase-tabelldata][img-hbase-sample-data-tabular]

I HBase, som är en implementering av BigTable, ser samma data ut så här:

![HDInsight HBase BigTable-data][img-hbase-sample-data-bigtable]

Det kommer att klarna mer efter att du har avslutat nästa procedur.  

**Använd HBase Shell**

1. Från SSH kör du följande kommando:
   
        hbase shell
2. Skapa en HBase med två kolumnserier:
   
        create 'Contacts', 'Personal', 'Office'
        list
3. Infoga data:
   
        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'
   
    ![HDInsight Hadoop HBase shell][img-hbase-shell]
4. Hämta en rad
   
        get 'Contacts', '1000'
   
    Samma resultat visas som med skanningskommandot eftersom det bara finns en rad.
   
    Mer information om HBase-tabellschemat finns i [Introduktion till design av HBase-schema][hbase-schema]. Fler HBase-kommandon finns i [referensguiden för Apache HBase][hbase-quick-start].
5. Lämna gränssnittet
   
        exit

**För att läsa in data i bulk i HBase-tabellen kontakter**

HBase innehåller flera metoder för att läsa in data i tabeller.  Mer information finns i [Massinläsning](http://hbase.apache.org/book.html#arch.bulk.load).

En exempeldatafil har överförts till en offentlig blobbehållaren *wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  Innehållet i datafilen är:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Du kan skapa en textfil och överföra filen till ditt eget lagringskonto om du vill. Anvisningarna finns i [Överföra data för Hadoop-jobb i HDInsight][hdinsight-upload-data].

> [!NOTE]
> Den här proceduren använder den HBase-tabell för kontakter som du skapade i föregående procedur.
> 
> 

1. Kör följande kommando från SSH för att omvandla datafilen till StoreFiles och lagra vid en relativ sökväg som anges av Dimporttsv.bulk.output:.  Om du är i HBase Shell använder du avslutningskommandot för att avsluta.
   
        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
2. Kör följande kommando för att överföra data från /example/data/storeDataFileOutput till  HBase-tabellen:
   
        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
3. Du kan öppna HBase-gränssnittet och använda skanningskommandot för att lista tabellinnehållet.

## <a name="use-hive-to-query-hbase"></a>Använd Hive för att ställa frågor till HBase
Du kan fråga efter data i HBase-tabeller med hjälp av Hive. I det här avsnittet skapas en Hive-tabell som mappar till HBase-tabellen och använder den för att fråga efter data i din HBase-tabell.

> [!NOTE]
> Om Hive och HBase är på olika kluster i samma VNet måste du passera zookeeperkvorum samtidigt som du anropar Hive-gränssnittet:
>
>       hive --hiveconf hbase.zookeeper.quorum=zk0-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net,zk1-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net,zk2-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net --hiveconf zookeeper.znode.parent=/hbase-unsecure  
>
>

1. Öppna **PuTTY** och anslut till klustret.  Se anvisningarna i föregående procedur.
2. Öppna Hive-gränssnittet.
   
       hive
       
3. Kör följande HiveQL-skript för att skapa en Hive-tabell som mappar till HBase-tabellen. Kontrollera att du har skapat den exempeltabell som vi hänvisade till tidigare i den här självstudien med hjälp av HBase-gränssnittet innan du kör den här instruktionen.
   
        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');
4. Kör följande HiveQL-skript för att fråga data i HBase-tabellen:
   
         SELECT count(*) FROM hbasecontacts;

## <a name="use-hbase-rest-apis-using-curl"></a>Använd HBase REST API:er med Curl
> [!NOTE]
> När du använder Curl eller annan REST-kommunikation med WebHCat, måste du autentisera begärandena genom att ange användarnamn och lösenord för HDInsight-klustrets administratör. Du måste också använda klustrets namn som en del av den URI (Uniform Resource Identifier) som används för att skicka begäranden till servern.
> 
> För kommandona i det här avsnittet, ersätter du **USERNAME** med användaren för att autentisera till klustret och ersätter **PASSWORD** med lösenordet för användarkontot. Ersätt **CLUSTERNAME** med namnet på klustret.
> 
> REST API skyddas via [grundläggande autentisering](http://en.wikipedia.org/wiki/Basic_access_authentication). Du bör alltid göra begäranden genom att använda säker HTTP (HTTPS) för att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern.
> 
> 

1. Använd följande kommando från en kommandorad för att verifiera att du kan ansluta till ditt HDInsight-kluster:
   
        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
   
    Du bör få ett svar som liknar följande:
   
        {"status":"ok","version":"v1"}
   
    De parametrar som används i det här kommandot är följande:
   
   * **-u** – Det användarnamn och lösenord som används för att autentisera begäran.
   * **-G** – Anger att detta är en GET-begäran.
2. Använd följande kommando för att lista de befintliga HBase-tabellerna:
   
        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/hbaserest/
3. Använd följande kommando för att skapa en ny HBase-tabell med två kolumnserier:
   
        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
        -v
   
    Schemat tillhandahålls i JSon-format.
4. Använd följande kommando för att infoga vissa data:
   
        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
        -v
   
    Du måste base64-koda de värden som anges i switchen -d.  I exemplet:
   
   * MTAwMA ==: 1000
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
   * Sm9obiBEb2xl: John Dole
     
     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) gör att du kan infoga flera (gruppbaserade) värden.
5. Använd följande kommando för att få en rad:
   
        curl -u <UserName>:<Password> \
        -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
        -H "Accept: application/json" \
        -v

Mer information om HBase Rest finns i [Referensguiden för Apache HBase](https://hbase.apache.org/book.html#_rest).

>
> [!NOTE]
> Thrift stöds inte av HBase i HDInsight.
>

## <a name="check-cluster-status"></a>Kontrollera klusterstatus
HBase i HDInsight levereras med ett webbgränssnitt för övervakning av kluster. Du kan använda webbgränssnittet för att begära statistik eller information om regioner.

**Så här får du åtkomst till HBase Master UI**

1. Öppna Ambari-webbgränssnittet på https://&lt;Clustername>.azurehdinsight.net.
2. Klicka på **HBase** på den vänstra menyn.
3. Klicka på **Snabblänkar** överst på sidan, peka på den aktiva Zookeeper-nodlänken och klicka sedan på **HBase Master UI**.  Gränssnittet har öppnats i en annan webbläsarflik:

  ![HDInsight HBase HMaster UI](./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

  HBase Master UI innehåller följande avsnitt:

  - regionservrar
  - huvudservrar för säkerhetskopiering
  - tabeller
  - uppgifter
  - attribut för programvara

## <a name="delete-the-cluster"></a>Ta bort klustret
Om du vill undvika inkonsekvenser rekommenderar vi att du inaktiverar HBase-tabellerna innan du tar bort klustret.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Nästa steg
I den här HBase-självstudien för HDInsight har du fått lära dig att skapa ett HBase-kluster och hur du skapar tabeller och visar data i dessa tabeller från HBase-gränssnittet. Du har också fått lära dig hur man använder en Hive-fråga på data i HBase-tabeller och hur man använder HBase C# REST-API:er för att skapa en HBase-tabell och hämta data från tabellen.

Du kan läsa mer här:

* [HDInsight HBase-översikt][hdinsight-hbase-overview]: HBase är en NoSQL-databas av Apachetyp med öppen källkod som bygger på Hadoop och som ger direktåtkomst och stark konsekvens för stora mängder ostrukturerade och semistrukturerade data.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png

