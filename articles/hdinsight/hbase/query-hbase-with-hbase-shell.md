---
title: 'Snabbstart: Fråga Apache HBase i Azure HDInsight - HBase-gränssnittet'
description: Lär dig använda Apache HBase Shell för att köra Apache HBase-frågor.
keywords: hdinsight,hadoop,HBase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: hrasheed
ms.openlocfilehash: 41b16e63522a02cc16eb4dac2cbcc8e6540aceaf
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65552088"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Snabbstart: Fråga Apache HBase i Azure HDInsight med HBase-gränssnittet

I den här snabbstarten får du lära dig hur du använder Apache HBase-gränssnittet för att skapa en HBase-tabell, infoga data och sedan fråga tabellen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett Apache HBase-kluster. Se [Skapa kluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) att skapa ett HDInsight-kluster.  Kontrollera att du väljer den **HBase** typ av kluster.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Skapa en tabell och manipulera data

För de flesta visas data i tabellformat:

![HDInsight HBase-tabelldata](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

I HBase (en implementering av [molnet BigTable](https://cloud.google.com/bigtable/)), samma data ut:

![HDInsight HBase BigTable-data](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

Du kan använda SSH för att ansluta till HBase-kluster och sedan använda Apache HBase Shell för att skapa HBase-tabeller, infoga data och fråga efter data.

1. Använd `ssh` kommando för att ansluta till HBase-kluster. Redigera kommandot nedan genom att ersätta `CLUSTERNAME` med namnet på klustret, och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Använd `hbase shell` kommando för att starta interaktiv HBase-gränssnittet. Ange följande kommando i din SSH-anslutning:

    ```bash
    hbase shell
    ```

3. Använd `create` kommando för att skapa en HBase-tabell med två kolumnserier. Ange följande kommando:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Använd `list` kommando för att lista alla tabeller i HBase. Ange följande kommando:

    ```hbase
    list
    ```

5. Använd `put` kommando för att infoga värden i en angiven kolumn i en angiven rad i en viss tabell. Ange följande kommando:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Använd `scan` kommando för att skanna och returnera den `Contacts` tabelldata. Ange följande kommando:

    ```hbase
    scan 'Contacts'
    ```

7. Använd `get` kommando för att hämta innehållet i en rad. Ange följande kommando:

    ```hbase
    get 'Contacts', '1000'
    ```

    Du ser liknande resultat som med hjälp av den `scan` kommandot eftersom det finns bara en rad.

8. Använd `delete` kommando för att ta bort en cellvärden i en tabell. Ange följande kommando:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Använd `disable` kommando för att inaktivera tabellen. Ange följande kommando:

    ```hbase
    disable 'Contacts'
    ```

10. Använd `drop` kommando för att ta bort en tabell från HBase. Ange följande kommando:

    ```hbase
    drop 'Contacts'
    ```

11. Använd `exit` kommando för att stoppa interaktiva HBase-gränssnittet. Ange följande kommando:

    ```hbase
    exit
    ```

Mer information om HBase-tabellschemat finns i [introduktion till Design för Apache HBase-Schema](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Fler HBase-kommandon i [referensguiden för Apache HBase](https://hbase.apache.org/book.html#quickstart).

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kan du ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Om du vill ta bort ett kluster, se [ta bort ett HDInsight-kluster med din webbläsare, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du använder Apache HBase-gränssnittet för att skapa en HBase-tabell, infoga data och fråga efter tabellen. Mer information om data som lagras i HBase visas nästa artikel hur du kör frågor med Apache Spark.

> [!div class="nextstepaction"]
> [Använda Apache Spark för att läsa och skriva Apache HBase-data](../hdinsight-using-spark-query-hbase.md)