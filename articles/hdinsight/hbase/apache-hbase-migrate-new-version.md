---
title: Migrera ett HBase-kluster till en ny version - Azure HDInsight
description: Hur du migrerar HBase-kluster till en ny version.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: ac7984c50e6adec888c112cc260cf2e6af02fc97
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114469"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrera ett Apache HBase-kluster till en ny version

Jobb-baserade kluster som [Apache Spark](https://spark.apache.org/) och [Apache Hadoop](https://hadoop.apache.org/), är enkelt att uppgradera – Se [uppgradera HDInsight-kluster till en nyare version](../hdinsight-upgrade-cluster.md):

1. Säkerhetskopiera data för tillfälliga (lokalt lagrad).
2. Ta bort det befintliga klustret.
3. Skapa ett nytt kluster i samma undernät för virtuellt nätverk.
4. Importera tillfälliga data.
5. Starta jobb och fortsätta bearbetningen på det nya klustret.

Så här uppgraderar du en [Apache HBase](https://hbase.apache.org/) kluster några ytterligare steg krävs, enligt beskrivningen i den här artikeln.

> [!NOTE]  
> Driftstopp under uppgraderingen bör vara minimal storleksordningen minuter. Det här driftstoppet orsakas av stegen för att tömma alla minnesinterna data och sedan tiden för att konfigurera och starta om tjänsterna på det nya klustret. Resultatet varierar beroende på antalet noder, mängden data och andra variabler.

## <a name="review-apache-hbase-compatibility"></a>Granska Apache HBase-kompatibilitet

Kontrollera innan du uppgraderar Apache HBase, HBase-versioner på käll- och -kluster är kompatibla. Mer information finns i [Apache Hadoop-komponenter och versioner som är tillgängliga med HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> Vi rekommenderar starkt att du läser igenom kompatibilitetsöversikten version i den [HBase boken](https://hbase.apache.org/book.html#upgrading).

Här är ett exempel version kompatibilitetsöversikten, där Y anger kompatibilitet och N anger en potentiella kompatibilitetsproblem:

| Kompatibilitetstyp | Huvudversion| Podverze | Korrigering |
| --- | --- | --- | --- |
| Klient-Server under överföring kompatibilitet | N | Y | Y |
| Kompatibilitet för servrar | N | Y | Y |
| Kompatibla filformat | N | Y | Y |
| Klient-API-kompatibilitet | N | Y | Y |
| Klienten binär kompatibilitet | N | N | Y |
| **Serversidan begränsad API-kompatibilitet** |  |  |  |
| Stable | N | Y | Y |
| Utvecklas | N | N | Y |
| Instabilt | N | N | N |
| Beroende kompatibilitet | N | Y | Y |
| Operativa kompatibilitet | N | N | Y |

> [!NOTE]  
> Alla de senaste inkompatibiliteter beskrivas i HBase version viktig information.

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Uppgradera med samma huvudversion för Apache HBase

Följande scenario är för att uppgradera från HDInsight 3.4 till 3.6 (både levereras med Apache HBase 1.1.2) med samma huvudversion för HBase. Andra versionsuppgraderingar liknar varandra, så länge det finns inga kompatibilitetsproblem mellan käll- och versioner.

1. Se till att ditt program är kompatibla med den nya versionen, som visas i HBase kompatibilitet matris och viktig information. Testa ditt program i ett kluster som kör Målversionen av HDInsight och HBase.

2. [Konfigurera ett nytt mål HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md) använder samma lagringskonto, men med ett annat behållarnamn:

    ![Använd samma lagringskonto, men skapa en annan behållare](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Rensa din källa HBase-kluster. Det här är det kluster som du uppgraderar. HBase skriver inkommande data till en minnesintern lagring kallas en _memstores_. När memstores når en viss storlek, memstores har rensats till disk för långsiktig lagring på klustrets lagringskonto. När du tar bort det gamla klustret, har memstores återvunnits, potentiellt att förlora data. Att manuellt tömma memstores för varje tabell på disk, kör följande skript. Den senaste versionen av det här skriptet finns på Azures [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh).

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
    
4. Stoppa inmatning för det gamla HBase-klustret.
5. För att säkerställa att alla de senaste data i memstores har rensats, kör du föregående skript igen.
6. Logga in på [Apache Ambari](https://ambari.apache.org/) på det gamla klustret (https://OLDCLUSTERNAME.azurehdidnsight.net) och stoppa HBase-tjänsterna. När du uppmanas att bekräfta att du vill stoppa tjänsterna markerar du kryssrutan Aktivera underhållsläget för HBase. Läs mer om att ansluta till och använda Ambari [hantera HDInsight-kluster med hjälp av Ambari-Webbgränssnittet](../hdinsight-hadoop-manage-ambari.md).

    ![I Ambari, klicka på fliken tjänster, sedan HBase på den vänstra menyn och sedan stoppa under tjänståtgärder](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Kontrollera aktivera på Underhållsläge för HBase kryssrutan och bekräfta sedan](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Logga in på Ambari på det nya HDInsight-klustret. Ändra den `fs.defaultFS` HDFS-inställningen så att den pekar till behållarens namn som används av det ursprungliga klustret. Den här inställningen är **HDFS > Peeringkonfigurationer > Avancerat > avancerade core-site**.

    ![I Ambari, klickar du på fliken tjänster sedan HDFS på den vänstra menyn och sedan fliken konfigurationer, sedan fliken Avancerat under](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![I Ambari, ändrar du behållarens namn](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. **Om du inte använder HBase-kluster med funktionen Förbättrad skriver, hoppar du över det här steget. Det krävs endast för HBase-kluster med förbättrad skriver funktionen.**
   
   Ändra hbase.rootdir sökvägen så att den pekar till en behållare för det ursprungliga klustret.

    ![I Ambari, ändrar du behållarens namn för hbase rootdir](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)
    
9. Spara ändringarna.
10. Starta om alla nödvändiga tjänster som anges av Ambari.
11. Peka ditt program till det nya klustret.

    > [!NOTE]  
    > Statisk DNS för ditt program ändras när du uppgraderar. I stället för att hårdkoda detta DNS, kan du konfigurera en CNAME-post i DNS-inställningarna för ditt domännamn som pekar på klustrets namn. Ett annat alternativ är att använda en konfigurationsfil för ditt program som du kan uppdatera utan att omdistribuera.

12. Starta inmatningen för att se om allt fungerar som förväntat.
13. Ta bort det ursprungliga klustret om du är nöjd med det nya klustret.

## <a name="next-steps"></a>Nästa steg

Mer information om [Apache HBase](https://hbase.apache.org/) och uppgradera HDInsight-kluster finns i följande artiklar:

* [Uppgradera ett HDInsight-kluster till en nyare version](../hdinsight-upgrade-cluster.md)
* [Övervaka och hantera Azure HDInsight med hjälp av Apache Ambari-Webbgränssnittet](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop-komponenter och versioner](../hdinsight-component-versioning.md)
* [Optimera konfigurationer med Apache Ambari](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
