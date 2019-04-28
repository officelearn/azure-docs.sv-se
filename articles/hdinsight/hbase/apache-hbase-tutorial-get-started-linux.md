---
title: Kom igång med HBase-exempel på HDInsight - Azure
description: Följ stegen i det här exemplet om Apache HBase om du vill börja använda hadoop med HDInsight. Skapa tabeller från HBase-gränssnittet och ställ frågor för dem med Hive.
keywords: hbasecommand,hbase example
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: c1c582c60a7c91de40983daa07bdec1e8d748f8a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123135"
---
# <a name="get-started-with-an-apache-hbase-example-in-hdinsight"></a>Kom igång med ett Apache HBase-exempel i HDInsight

Lär dig hur du skapar en [Apache HBase](https://hbase.apache.org/) kluster i HDInsight, skapa HBase-tabeller och frågetabeller med [Apache Hive](https://hive.apache.org/).  Allmän HBase-information finns i [HDInsight HBase-översikt][hdinsight-hbase-overview].

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du testar det här HBase-exemplet måste du ha följande objekt:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Secure Shell(SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md). 
* [curl](https://curl.haxx.se/download.html).

## <a name="create-apache-hbase-cluster"></a>Skapa Apache HBase-kluster
Följande procedur använder en Azure Resource Manager-mall för att skapa ett HBase-kluster och det beroende standardkontot för Azure Storage. Mer information om de parametrar som används i proceduren och andra metoder för att skapa kluster finns i [Skapa Linux-baserade Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Mer information om hur du kan använda Data Lake Storage Gen2 finns i [Snabbstart: Konfigurera kluster i HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

1. Klicka på följande bild för att öppna mallen i Azure Portal. Mallen finns i [Azures snabbstartsmallar](https://azure.microsoft.com/resources/templates/).
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Från bladet **Anpassad distribution** anger du följande värden:
   
   * **Prenumeration**: Välj din Azure-prenumeration som används för att skapa klustret.
   * **Resursgrupp**: Skapa en Azure Resource management-grupp eller Använd en befintlig.
   * **Plats**: Ange platsen för resursgruppen. 
   * **Klusternamn**: Ange ett namn för HBase-kluster.
   * **Klustrets inloggningsnamn och lösenord**: Standardinloggningsnamnet är **admin**.
   * **SSH-användarnamn och lösenord**: Standardanvändarnamnet är **sshuser**.  Du kan byta namn.
     
     Andra parametrar är valfria.  
     
     Varje kluster är beroende av ett Azure Storage-konto. När du tar bort ett kluster stannar aktuella data kvar på lagringskontot. Klustrets lagringskonto av standardtyp har det klusternamn som omfattar tillägget ”store”. Det är hårdkodat i avsnittet för mallvariabler.
3. Välj **Jag godkänner villkoren som anges ovan** och klicka sedan på **Köp**. Det tar cirka 20 minuter att skapa ett kluster.

> [!NOTE]  
> När ett HBase-kluster har tagits bort kan du skapa ett annat HBase-kluster med hjälp av samma standard-blob-container. Det nya klustret hämtar de HBase-tabeller som du skapade i det ursprungliga klustret. Om du vill undvika inkonsekvenser rekommenderar vi att du inaktiverar HBase-tabellerna innan du tar bort klustret.
> 
> 

## <a name="create-tables-and-insert-data"></a>Skapa tabeller och infoga data
Du kan använda SSH för att ansluta till HBase-kluster och sedan använda [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) för att skapa HBase-tabeller, infoga data och köra frågor mot data. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

För de flesta visas data i tabellformat:

![HDInsight HBase-tabelldata][img-hbase-sample-data-tabular]

I HBase (en implementering av [molnet BigTable](https://cloud.google.com/bigtable/)), samma data ut:

![HDInsight HBase BigTable-data][img-hbase-sample-data-bigtable]


**Använd HBase Shell**

1. Från SSH kör du följande HBase-kommando:
   
    ```bash
    hbase shell
    ```

2. Skapa en HBase med två kolumnserier:

    ```hbaseshell   
    create 'Contacts', 'Personal', 'Office'
    list
    ```
3. Infoga data:
    
    ```hbaseshell   
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    scan 'Contacts'
    ```
   
    ![HDInsight Hadoop HBase shell][img-hbase-shell]
4. Hämta en rad
   
    ```hbaseshell
    get 'Contacts', '1000'
    ```
   
    Samma resultat visas som med skanningskommandot eftersom det bara finns en rad.
   
    Mer information om HBase-tabellschemat finns i [introduktion till Design för Apache HBase-Schema][hbase-schema]. Fler HBase-kommandon finns i [referensguiden för Apache HBase][hbase-quick-start].
5. Lämna gränssnittet
   
    ```hbaseshell
    exit
    ```

**För att läsa in data i bulk i HBase-tabellen kontakter**

HBase innehåller flera metoder för att läsa in data i tabeller.  Mer information finns i [Massinläsning](https://hbase.apache.org/book.html#arch.bulk.load).

En exempeldatafil finns i en offentlig blobbehållare *wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt*.  Innehållet i datafilen är:

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

Du kan även skapa en textfil och överföra filen till ditt eget lagringskonto. Anvisningar finns i [överföra data för Apache Hadoop-jobb i HDInsight][hdinsight-upload-data].

> [!NOTE]  
> Den här proceduren använder den HBase-tabell för kontakter som du skapade i föregående procedur.

1. Kör följande kommando från SSH för att omvandla datafilen till StoreFiles och lagra vid en relativ sökväg som anges av Dimporttsv.bulk.output.  Om du är i HBase Shell använder du avslutningskommandot för att avsluta.

    ```bash   
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Kör följande kommando för att överföra data från /example/data/storeDataFileOutput till  HBase-tabellen:
   
    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Du kan öppna HBase-gränssnittet och använda skanningskommandot för att lista tabellinnehållet.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Använda Apache Hive för att ställa frågor till Apache HBase

Du kan fråga efter data i HBase-tabeller med hjälp av [Apache Hive](https://hive.apache.org/). I det här avsnittet skapar du en Hive-tabell som mappar till HBase-tabellen och använder den för att fråga efter data i din HBase-tabell.

1. Öppna **PuTTY** och anslut till klustret.  Se anvisningarna i föregående procedur.
2. Från SSH-sessionen använder du följande kommando för att starta Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Mer information om Beeline finns i [Use Hive with Hadoop in HDInsight with Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) (Använda Hive med Hadoop i HDInsight med Beeline).
       
3. Kör följande [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) skript för att skapa en Hive-tabell som mappar till HBase-tabellen. Kontrollera att du har skapat den exempeltabell som vi hänvisade till tidigare i den här självstudien med hjälp av HBase-gränssnittet innan du kör den här instruktionen.

    ```hiveql   
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

4. Kör följande HiveQL-skript för att fråga data i HBase-tabellen:

    ```hiveql   
    SELECT count(rowkey) FROM hbasecontacts;
    ```

## <a name="use-hbase-rest-apis-using-curl"></a>Använd HBase REST API:er med Curl

REST API skyddas via [grundläggande autentisering](https://en.wikipedia.org/wiki/Basic_access_authentication). Du bör alltid göra begäranden genom att använda säker HTTP (HTTPS) för att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern.

1. Använd följande kommando för att lista de befintliga HBase-tabellerna:

    ```bash
    curl -u <UserName>:<Password> \
    -G https://<ClusterName>.azurehdinsight.net/hbaserest/
    ```

1. Använd följande kommando för att skapa en ny HBase-tabell med två kolumnserier:

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    Schemat tillhandahålls i JSon-format.
1. Använd följande kommando för att infoga vissa data:

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```
   
    Du måste base64-koda de värden som anges i switchen -d. I exemplet:
   
   * MTAwMA ==: 1000
   * UGVyc29uYWw6TmFtZQ==: Personlig: namn
   * Sm9obiBEb2xl: John Dole
     
     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) gör att du kan infoga flera (gruppbaserade) värden.
1. Använd följande kommando för att få en rad:
   
    ```bash 
    curl -u <UserName>:<Password> \
    -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Mer information om HBase Rest finns i [Referensguiden för Apache HBase](https://hbase.apache.org/book.html#_rest).

> [!NOTE]  
> Thrift stöds inte av HBase i HDInsight.
>
> När du använder Curl eller annan REST-kommunikation med WebHCat, måste du autentisera begärandena genom att ange användarnamn och lösenord för HDInsight-klustrets administratör. Du måste också använda klustrets namn som en del av den URI (Uniform Resource Identifier) som används för att skicka begäranden till servern.
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    Du bör få ett svar som liknar följande svar:
>   
>        {"status":"ok","version":"v1"}
   


## <a name="check-cluster-status"></a>Kontrollera klusterstatus
HBase i HDInsight levereras med ett webbgränssnitt för övervakning av kluster. Du kan använda webbgränssnittet för att begära statistik eller information om regioner.

**Så här får du åtkomst till HBase Master UI**

1. Logga in på Ambari-webbgränssnittet på https://&lt;klusternamn >. azurehdinsight.net.
2. Klicka på **HBase** på den vänstra menyn.
3. Klicka på **Snabblänkar** överst på sidan, peka på den aktiva Zookeeper-nodlänken och klicka sedan på **HBase Master UI**.  Gränssnittet har öppnats i en annan webbläsarflik:

   ![HDInsight HBase HMaster UI](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   HBase Master UI innehåller följande avsnitt:

   - regionservrar
   - huvudservrar för säkerhetskopiering
   - tabeller
   - uppgifter
   - attribut för programvara

## <a name="delete-the-cluster"></a>Ta bort klustret
Om du vill undvika inkonsekvenser rekommenderar vi att du inaktiverar HBase-tabellerna innan du tar bort klustret.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du skapar ett Apache HBase-kluster och hur du skapar tabeller och visar data i dessa tabeller från HBase-gränssnittet. Du har också fått lära dig hur man använder en Hive-fråga på data i HBase-tabeller och hur man använder HBase C# REST-API:er för att skapa en HBase-tabell och hämta data från tabellen.

Du kan läsa mer här:

* [Översikt över HDInsight HBase][hdinsight-hbase-overview]: Apache HBase är en Apache, öppen källkod, NoSQL-databas som bygger på Apache Hadoop och ger direktåtkomst och stark konsekvens för stora mängder Ostrukturerade och semistrukturerade data.

[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hbase-reference]: https://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: https://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]:apache-hbase-overview.md
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: https://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hbase-shell]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png
