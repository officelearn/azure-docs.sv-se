---
title: Självstudie – Använd Apache HBase i Azure HDInsight
description: Den här kursen Apache HBase om du vill börja använda hadoop på HDInsight. Skapa tabeller från HBase-gränssnittet och ställ frågor för dem med Hive.
keywords: hbasecommand,hbase example
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: tutorial
ms.date: 06/25/2019
ms.author: hrasheed
ms.openlocfilehash: 48b02a042b55af9ff65f57220f7a64c9cbde8848
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445549"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Självstudier: Använda Apache HBase i Azure HDInsight

Den här kursen visar hur du skapar ett Apache HBase-kluster i Azure HDInsight, skapa HBase-tabeller och frågetabeller med Apache Hive.  Allmän HBase-information finns i [HDInsight HBase-översikt](./apache-hbase-overview.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa Apache HBase-kluster
> * Skapa HBase-tabeller och infoga data
> * Använda Apache Hive för att ställa frågor till Apache HBase
> * Använd HBase REST API:er med Curl
> * Kontrollera klusterstatus

## <a name="prerequisites"></a>Förutsättningar

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. Exemplen i den här artikeln använder Bash-gränssnittet i Windows 10 för curl-kommandon. Se [Windows-undersystem for Linux Installation Guide för Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) för installationssteg.  Andra [Unix gränssnitt](https://www.gnu.org/software/bash/) fungerar också.  Curl-exempel, med några små ändringar kan arbeta i en kommandotolk i Windows.  Du kan också använda Windows PowerShell-cmdleten [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod).

## <a name="create-apache-hbase-cluster"></a>Skapa Apache HBase-kluster

Följande procedur använder en Azure Resource Manager-mall för att skapa ett HBase-kluster och det beroende standardkontot för Azure Storage. Mer information om de parametrar som används i proceduren och andra metoder för att skapa kluster finns i [Skapa Linux-baserade Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Välj följande bild för att öppna mallen i Azure-portalen. Mallen finns i [Azures snabbstartsmallar](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Från bladet **Anpassad distribution** anger du följande värden:

    |Egenskap |Beskrivning |
    |---|---|
    |Prenumeration|Välj din Azure-prenumeration som används för att skapa klustret.|
    |Resursgrupp|Skapa en Azure Resource management-grupp eller Använd en befintlig.|
    |Location|Ange platsen för resursgruppen. |
    |Klusternamn|Ange ett namn för HBase-kluster.|
    |Klustrets inloggningsnamn och lösenord|Standardinloggningsnamnet är **admin**.|
    |SSH-användarnamn och lösenord|Standardanvändarnamnet är **sshuser**.|

    Andra parametrar är valfria.  

    Varje kluster är beroende av ett Azure Storage-konto. När du tar bort ett kluster stannar aktuella data kvar på lagringskontot. Klustrets lagringskonto av standardtyp har det klusternamn som omfattar tillägget ”store”. Det är hårdkodat i avsnittet för mallvariabler.

3. Välj **Jag godkänner villkoren som anges ovan** och välj sedan **Köp**. Det tar cirka 20 minuter att skapa ett kluster.

När ett HBase-kluster har tagits bort kan du skapa ett annat HBase-kluster med hjälp av samma standard-blob-container. Det nya klustret hämtar de HBase-tabeller som du skapade i det ursprungliga klustret. Om du vill undvika inkonsekvenser rekommenderar vi att du inaktiverar HBase-tabellerna innan du tar bort klustret.

## <a name="create-tables-and-insert-data"></a>Skapa tabeller och infoga data

Du kan använda SSH för att ansluta till HBase-kluster och sedan använda [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) för att skapa HBase-tabeller, infoga data och köra frågor mot data.

För de flesta visas data i tabellformat:

![HDInsight HBase-tabelldata](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

I HBase (en implementering av [molnet BigTable](https://cloud.google.com/bigtable/)), samma data ut:

![HDInsight HBase BigTable-data](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**Använd HBase Shell**

1. Använd `ssh` kommando för att ansluta till HBase-kluster. Redigera kommandot nedan genom att ersätta `CLUSTERNAME` med namnet på klustret, och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Använd `hbase shell` kommando för att starta interaktiv HBase-gränssnittet. Ange följande kommando i din SSH-anslutning:

    ```bash
    hbase shell
    ```

1. Använd `create` kommando för att skapa en HBase-tabell med två kolumnserier. Tabell-och är skiftlägeskänsliga. Ange följande kommando:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Använd `list` kommando för att lista alla tabeller i HBase. Ange följande kommando:

    ```hbase
    list
    ```

1. Använd `put` kommando för att infoga värden i en angiven kolumn i en angiven rad i en viss tabell. Ange följande kommandon:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Använd `scan` kommando för att skanna och returnera den `Contacts` tabelldata. Ange följande kommando:

    ```hbase
    scan 'Contacts'
    ```

    ![HDInsight Hadoop HBase shell](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Använd `get` kommando för att hämta innehållet i en rad. Ange följande kommando:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Du ser liknande resultat som med hjälp av den `scan` kommandot eftersom det finns bara en rad.

    Mer information om HBase-tabellschemat finns i [introduktion till Design för Apache HBase-Schema](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Fler HBase-kommandon i [referensguiden för Apache HBase](https://hbase.apache.org/book.html#quickstart).

1. Använd `exit` kommando för att stoppa interaktiva HBase-gränssnittet. Ange följande kommando:

    ```hbaseshell
    exit
    ```

**För att läsa in data i bulk i HBase-tabellen kontakter**

HBase innehåller flera metoder för att läsa in data i tabeller.  Mer information finns i [Massinläsning](https://hbase.apache.org/book.html#arch.bulk.load).

En exempeldatafil finns i en offentlig blobbehållare `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  Innehållet i datafilen är:

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

Du kan även skapa en textfil och överföra filen till ditt eget lagringskonto. Anvisningar finns i [överföra data för Apache Hadoop-jobb i HDInsight](../hdinsight-upload-data.md).

Den här proceduren använder den `Contacts` HBase-tabell som du skapade i föregående procedur.

1. Från din öppna ssh-anslutning kör du följande kommando för att transformera data filen till StoreFiles och lagra vid en relativ sökväg som anges av `Dimporttsv.bulk.output`.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Kör följande kommando för att överföra data från `/example/data/storeDataFileOutput` till HBase-tabellen:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Du kan öppna HBase-gränssnittet och använda den `scan` kommando för att lista innehåll.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Använda Apache Hive för att ställa frågor till Apache HBase

Du kan fråga efter data i HBase-tabeller med hjälp av [Apache Hive](https://hive.apache.org/). I det här avsnittet skapar du en Hive-tabell som mappar till HBase-tabellen och använder den för att fråga efter data i din HBase-tabell.

1. Från din öppna ssh-anslutningen, använder du följande kommando för att starta Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Mer information om Beeline finns i [Use Hive with Hadoop in HDInsight with Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) (Använda Hive med Hadoop i HDInsight med Beeline).

1. Kör följande [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) skript för att skapa en Hive-tabell som mappar till HBase-tabellen. Kontrollera att du har skapat den exempeltabell som hänvisade till tidigare i den här artikeln med hjälp av HBase-gränssnittet innan du kör den här instruktionen.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. Kör följande HiveQL-skript för att fråga data i HBase-tabellen:

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. Om du vill avsluta Beeline använder `!exit`.

1. Avsluta din ssh-anslutning används `exit`.

## <a name="use-hbase-rest-apis-using-curl"></a>Använd HBase REST API:er med Curl

REST API skyddas via [grundläggande autentisering](https://en.wikipedia.org/wiki/Basic_access_authentication). Du bör alltid göra begäranden genom att använda säker HTTP (HTTPS) för att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern.

1. Initiera miljövariabeln för enkel användning. Redigera kommandona nedan genom att ersätta `MYPASSWORD` med inloggningslösenordet för klustret. Ersätt `MYCLUSTERNAME` med namnet på din HBase-kluster. Ange sedan kommandona.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Använd följande kommando för att lista de befintliga HBase-tabellerna:

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. Använd följande kommando för att skapa en ny HBase-tabell med två kolumnserier:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    Schemat tillhandahålls i JSon-format.
1. Använd följande kommando för att infoga vissa data:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
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
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
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

1. Logga in på Ambari-Webbgränssnittet på `https://CLUSTERNAME.azurehdinsight.net` där `CLUSTERNAME` är namnet på din HBase-kluster.

1. Välj **HBase** menyn till vänster.

1. Välj **snabblänkar** överst på sidan, peka på den aktiva Zookeeper-nodlänken och välj sedan **HBase Master UI**.  Gränssnittet har öppnats i en annan webbläsarflik:

   ![HDInsight HBase HMaster UI](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   HBase Master UI innehåller följande avsnitt:

   - regionservrar
   - huvudservrar för säkerhetskopiering
   - tabeller
   - uppgifter
   - attribut för programvara

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill undvika inkonsekvenser rekommenderar vi att du inaktiverar HBase-tabellerna innan du tar bort klustret. Du kan använda kommandot HBase `disable 'Contacts'`. Om du inte planerar att fortsätta använda det här programmet tar du bort det HBase-kluster som du skapade med följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. I rutan **Sök** längst upp skriver du **HDInsight**.
1. Välj **HDInsight-kluster** under **Tjänster**.
1. I listan över HDInsight-kluster som visas klickar du på **...** intill det kluster som du skapade för den här självstudien.
1. Klicka på **Ta bort**. Klicka på **Ja**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien beskrivs hur du skapar ett Apache HBase-kluster och hur du skapar tabeller och visar data i dessa tabeller från HBase-gränssnittet. Du har också fått lära dig hur man använder en Hive-fråga på data i HBase-tabeller och hur man använder HBase C# REST-API:er för att skapa en HBase-tabell och hämta data från tabellen. Du kan läsa mer här:

> [!div class="nextstepaction"]
> [HDInsight HBase-översikt](./apache-hbase-overview.md)