---
title: Migrera ett HBase-kluster till en ny version - Azure HDInsight
description: Migrera Apache HBase-kluster till en nyare version i Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 30cda7a83feddaeb41385252a61d1dc68a881a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646514"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrera ett Apache HBase-kluster till en ny version

I den hÃ¤r artikeln beskrivs de steg som krävs fÃ¶r att uppdatera Apache HBase-klustret fÃ¶r Azure HDInsight till en nyare version.

Driftstoppet vid uppgradering bör vara minimal, i ordningen för minuter. Den här driftstopp orsakas av stegen för att tömma alla minnesdata, sedan tid att konfigurera och starta om tjänsterna i det nya klustret. Resultaten varierar beroende på antalet noder, mängden data och andra variabler.

## <a name="review-apache-hbase-compatibility"></a>Granska Apache HBase-kompatibilitet

Innan du uppgraderar Apache HBase ska du se till att HBase-versionerna på käll- och målkluster är kompatibla. Mer information finns i [Apache Hadoop-komponenter och versioner som är tillgängliga med HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> Vi rekommenderar starkt att du granskar versionskompatibilitetsmatrisen i [HBase-boken](https://hbase.apache.org/book.html#upgrading). Eventuella brytande inkompatibiliteter bör beskrivas i HBase-versionens viktig information.

Här är ett exempel version kompatibilitet matris. Y anger kompatibilitet och N indikerar en potentiell inkompatibilitet:

| Typ av kompatibilitet | Större version| Delversion | Patch |
| --- | --- | --- | --- |
| Kompatibilitet mellan klient och server | N | Y | Y |
| Server-serverkompatibilitet | N | Y | Y |
| Kompatibilitet för filformat | N | Y | Y |
| API-kompatibilitet för klient | N | Y | Y |
| Binär kompatibilitet för klienten | N | N | Y |
| **Begränsad API-kompatibilitet på serversidan** |  |  |  |
| Stable | N | Y | Y |
| Utvecklas | N | N | Y |
| Instabila | N | N | N |
| Beroendekompatibilitet | N | Y | Y |
| Driftkompatibilitet | N | N | Y |

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Uppgradera med samma Apache HBase huvudversion

Så här uppgraderar du Apache HBase-klustret på Azure HDInsight:

1. Kontrollera att ditt program är kompatibelt med den nya versionen, som visas i HBase-kompatibilitetsmatrisen och viktig information. Testa ditt program i ett kluster som kör målversionen av HDInsight och HBase.

1. [Konfigurera ett nytt HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md) för mål med samma lagringskonto, men med ett annat behållarnamn:

    ![Använd samma lagringskonto, men skapa en annan behållare](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

1. Rensa din källa HBase kluster, som är det kluster du uppgraderar. HBase skriver inkommande data till ett minneslager, som kallas _en memstore_. När memstore når en viss storlek, HBase spolar den till disk för långsiktig lagring i klustrets lagringskonto. När du tar bort det gamla klustret återvinns memstores, vilket kan förlora data. Om du vill tömma memstore manuellt för varje tabell till disk kör du följande skript. Den senaste versionen av det här skriptet finns på Azures [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh).

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
    
            Provide hostname as a parameter (i.e. hn0, hn1, hn2.. or wn2 etc.).
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

1. Stoppa inmatning till det gamla HBase-klustret.

1. Om du vill vara säkra på att alla nya data i memstore rensas kör du det föregående skriptet igen.

1. Logga in på [Apache Ambari](https://ambari.apache.org/) `https://OLDCLUSTERNAME.azurehdidnsight.net`i det gamla klustret ( ) och stoppa HBase-tjänsterna. När du uppmanas att bekräfta att du vill stoppa tjänsterna markerar du kryssrutan för att aktivera underhållsläge för HBase. Mer information om hur du ansluter till och använder Ambari finns i [Hantera HDInsight-kluster med hjälp av Ambari Web UI](../hdinsight-hadoop-manage-ambari.md).

    ![I Ambari klickar du på Tjänster > HBase > stopp under Service-åtgärder](./media/apache-hbase-migrate-new-version/stop-hbase-services1.png)

    ![Kontrollera kryssrutan Aktivera underhållsläge för HBase och bekräfta sedan](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

1. Logga in på Ambari i det nya HDInsight-klustret. Ändra `fs.defaultFS` HDFS-inställningen så att den pekar på det behållarnamn som används av det ursprungliga klustret. Den här inställningen är under **HDFS > Configs > Advanced > Advanced core-site**.

    ![I Ambari klickar du på Tjänster > HDFS > Configs > Advanced](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Ändra behållarnamnet i Ambari](./media/apache-hbase-migrate-new-version/change-container-name.png)

1. Om du inte använder HBase-kluster med funktionen Förbättrade skrivningar hoppar du över det här steget. Det behövs bara för HBase kluster med förbättrad skrivfunktionen.

   Ändra `hbase.rootdir` sökvägen så att den pekar på behållaren för det ursprungliga klustret.

    ![I Ambari ändrar du behållarnamnet för HBase rootdir](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)

1. Om du uppgraderar HDInsight 3.6 till 4.0 följer du stegen nedan och går annars till steg 10:
    1. Starta om alla nödvändiga tjänster i Ambari genom att välja **Tjänster** > **Starta om alla nödvändiga**.
    1. Stoppa HBase-tjänsten.
    1. SSH till zookeeper noden och kör [zkCli](https://github.com/go-zkcli/zkcli) kommandot `rmr /hbase-unsecure` för att ta bort HBase root znode från Zookeeper.
    1. Starta om HBase.

1. Om du uppgraderar till någon annan HDInsight-version förutom 4.0 gör du så här:
    1. Spara ändringarna.
    1. Starta om alla nödvändiga tjänster enligt Ambari.

1. Peka programmet på det nya klustret.

    > [!NOTE]  
    > Den statiska DNS för ditt program ändras vid uppgradering. I stället för att hårdkoda den här DNS:n kan du konfigurera ett CNAME i domännamnets DNS-inställningar som pekar på klustrets namn. Ett annat alternativ är att använda en konfigurationsfil för ditt program som du kan uppdatera utan omdistribution.

1. Starta inmatningen för att se om allt fungerar som förväntat.

1. Om det nya klustret är tillfredsställande tar du bort det ursprungliga klustret.

## <a name="next-steps"></a>Nästa steg

Mer information om [Apache HBase](https://hbase.apache.org/) och uppgradera HDInsight-kluster finns i följande artiklar:

* [Uppgradera ett HDInsight-kluster till en nyare version](../hdinsight-upgrade-cluster.md)
* [Övervaka och hantera Azure HDInsight med hjälp av Apache Ambari Webbgränssnitt](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop-komponenter och -versioner](../hdinsight-component-versioning.md)
* [Optimera konfigurationer med Apache Ambari](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
