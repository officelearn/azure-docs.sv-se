---
title: Migrera ett HBase-kluster till en ny version - Azure HDInsight | Microsoft Docs
description: Hur du migrerar HBase-kluster till en ny version.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 2ab0bc792777b73f878bc1728c0d8310ecf41167
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="migrate-an-hbase-cluster-to-a-new-version"></a>Migrera ett HBase-kluster till en ny version

Jobb-baserade kluster som Spark och Hadoop, som är lätt att uppgradera - finns [uppgradera HDInsight-kluster till en nyare version](../hdinsight-upgrade-cluster.md):

1. Säkerhetskopiera tillfälligt (lokalt lagrade).
2. Ta bort det befintliga klustret.
3. Skapa ett nytt kluster i samma undernät i VNET.
4. Importera tillfälligt data.
5. Starta jobb och fortsätta bearbetningen på det nya klustret.

Om du vill uppgradera ett HBase-kluster vissa ytterligare åtgärder krävs, enligt beskrivningen i den här artikeln.

> [!NOTE]
> Driftstopp under uppgraderingen bör vara minimal terabyte minuter. Den här driftstörningen orsakas av stegen för att tömma alla InMemory-data och sedan tiden för att konfigurera och starta om tjänsterna på det nya klustret. Resultatet varierar beroende på antalet noder, mängden data och andra variabler.

## <a name="review-hbase-compatibility"></a>Granska HBase-kompatibilitet

Kontrollera före uppgradering HBase HBase-versioner på käll- och -kluster är kompatibla. Mer information finns i [Hadoop-komponenter och versioner som är tillgängliga med HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]
> Vi rekommenderar starkt att du läser igenom kompatibilitetsmatrix version i den [HBase book](https://hbase.apache.org/book.html#upgrading).

Här är ett exempel version kompatibilitetsmatrix, där Y anger kompatibilitet och N anger potentiella inkompatibilitet:

| Kompatibilitetstypen | Huvudversion| Lägre version | Korrigering |
| --- | --- | --- | --- |
| Klient / Server-överföring kompatibilitet | N | Y | Y |
| Kompatibilitet för servrar | N | Y | Y |
| Kompatibla filformat | N | Y | Y |
| Programmeringsgränssnitt mot klienten kompatibilitet | N | Y | Y |
| Klienten binära kompatibilitet | N | N | Y |
| **Serversidan begränsade API-kompatibilitet** |  |  |  |
| Stable | N | Y | Y |
| Under utveckling | N | N | Y |
| Instabilt | N | N | N |
| Beroende kompatibilitet | N | Y | Y |
| Använd kompatibilitet | N | N | Y |

> [!NOTE]
> Alla inkompatibiliteter bryta beskrivas i HBase version viktig information.

## <a name="upgrade-with-same-hbase-major-version"></a>Uppgradera med samma huvudversion för HBase

Följande scenario är för att uppgradera från HDInsight 3.4 till 3,6 (både kommer med Apache HBase 1.1.2) med samma huvudversion HBase. Andra versionsuppgraderingar är desamma, så länge det finns inga kompatibilitetsproblem mellan käll- och versioner.

1. Kontrollera att ditt program är kompatibla med den nya versionen som visas i HBase kompatibilitet matris och viktig information. Testa ditt program i ett kluster som kör Målversionen av HDInsight och HBase.

2. [Ställ in ett nytt mål HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md) använder samma lagringskonto, men med ett annat behållarnamn:

    ![Använd samma lagringskonto, men för att skapa en annan behållare](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Rensa din datakälla HBase-kluster. Detta är det klustret som du uppgraderar. HBase skriver inkommande data till en InMemory-store kallas en _memstore_. När memstore når en viss storlek, i memstore rensas till disk för långsiktig lagring i klustrets lagringskonto. När du tar bort det gamla klustret, är memstores återvinnas potentiellt förlust av data. Kör följande skript för att tömma memstore för varje tabell manuellt till disk. Den senaste versionen av det här skriptet finns på Azures [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh).

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1 or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```
    
4. Stoppa införandet för det gamla klustret HBase.
5. Kör skriptet tidigare igen för att säkerställa att inga nya data i memstore rensas.
6. Logga in på Ambari på det gamla klustret (https://OLDCLUSTERNAME.azurehdidnsight.net) och stoppa HBase-tjänster. När du uppmanas att bekräfta att du vill stoppa tjänsterna, markera kryssrutan Aktivera Underhållsläge för HBase. Läs mer på ansluter till och med Ambari [hantera HDInsight-kluster med Ambari-Webbgränssnittet](../hdinsight-hadoop-manage-ambari.md).

    ![I Ambari, klicka på fliken tjänster sedan HBase på den vänstra menyn och sedan stoppa under tjänståtgärder](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Kontrollera aktivera på Underhållsläge för HBase kryssrutan och sedan bekräfta](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Logga in på Ambari på det nya HDInsight-klustret. Ändra den `fs.defaultFS` HDFS-inställningen för att peka på behållarens namn används av det ursprungliga klustret. Den här inställningen är **HDFS > konfigurationerna > Avancerat > avancerade core-plats**.

    ![Klicka på fliken tjänster sedan HDFS på den vänstra menyn, sedan fliken konfigurationerna, sedan fliken Avancerat under i Ambari,](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Ambari, ändra behållarens namn](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. Spara ändringarna.
9. Starta om alla nödvändiga tjänster som anges med Ambari.
10. Peka ditt program till det nya klustret.

    > [!NOTE]
    > Statiskhet DNS för ditt program ändras när du uppgraderar. I stället för att hårdkoda detta DNS, kan du konfigurera en CNAME-post i ditt domännamn DNS-inställningar som pekar på klustrets namn. Ett annat alternativ är att använda en konfigurationsfil för ditt program som du kan uppdatera utan att omdistribuera.

11. Starta införandet för att se om allt fungerar som förväntat.
12. Ta bort det ursprungliga klustret om du är nöjd med det nya klustret.

## <a name="next-steps"></a>Nästa steg

Mer information om HBase och uppgradering av HDInsight-kluster finns i följande artiklar:

* [Uppgradera ett HDInsight-kluster till en nyare version](../hdinsight-upgrade-cluster.md)
* [Övervaka och hantera Azure HDInsight med Ambari-Webbgränssnittet](../hdinsight-hadoop-manage-ambari.md)
* [Komponenterna i Hadoop och versioner](../hdinsight-component-versioning.md)
* [Optimera konfigurationer med Ambari](../hdinsight-changing-configs-via-ambari.md#hbase-optimization-with-the-ambari-web-ui)
