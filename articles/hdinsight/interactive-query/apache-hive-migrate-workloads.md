---
title: Migrera Azure HDInsight 3.6 Hive-arbetsbelastningar till HDInsight 4.0
description: Lär dig hur du migrerar Apache Hive-arbetsbelastningar på HDInsight 3.6 till HDInsight 4.0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: ec96189185a06c1fcbd95eed6216ade47f3089c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214650"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrera Azure HDInsight 3.6 Hive-arbetsbelastningar till HDInsight 4.0

Det här dokumentet visar hur du migrerar Apache Hive- och LLAP-arbetsbelastningar på HDInsight 3.6 till HDInsight 4.0. HDInsight 4.0 ger nyare Hive- och LLAP-funktioner som materialiserade vyer och frågeresultatcachelagring. När du migrerar dina arbetsbelastningar till HDInsight 4.0 kan du använda många nyare funktioner i Hive 3 som inte är tillgängliga på HDInsight 3.6.

Denna artikel behandlar följande ämnen:

* Flyttning av Hive-metadata till HDInsight 4.0
* Säker migration av ACID- och non-ACID-tabeller
* Bevarande av Hive-säkerhetsprinciper i HDInsight-versioner
* Frågekörning och felsökning från HDInsight 3.6 till HDInsight 4.0

En fördel med Hive är möjligheten att exportera metadata till en extern databas (kallad Hive Metastore). **Hive Metastore** ansvarar för att lagra tabellstatistik, inklusive tabelllagringsplats, kolumnnamn och tabellindexinformation. Metastore-databasschemat skiljer sig mellan Hive-versioner. Det rekommenderade sättet att uppgradera Hive-metabutiken på ett säkert sätt är att skapa en kopia och uppgradera kopian i stället för den aktuella produktionsmiljön.

## <a name="copy-metastore"></a>Kopiera metabutik

HDInsight 3.6 och HDInsight 4.0 kräver olika metavåningsscheman och kan inte dela en enda metabutik.

### <a name="external-metastore"></a>Extern metabutik

Skapa en ny kopia av din externa metabutik. Om du använder en extern metabutik är ett av de säkra och enkla sätten att göra en kopia av metabutiken att [återställa databasen](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) med ett annat namn med hjälp av sql Database-återställningsfunktionen.  Se [Använda externa metadataarklagringar i Azure HDInsight](../hdinsight-use-external-metadata-stores.md) om du vill veta mer om hur du kopplar en extern metabutik till ett HDInsight-kluster.

### <a name="internal-metastore"></a>Intern metabutik

Om du använder den interna metabutiken kan du använda frågor för att exportera objektdefinitioner i Hive-metabutiken och importera dem till en ny databas.

När skriptet är klart antas det att det gamla klustret inte längre kommer att användas för att komma åt någon av de tabeller eller databaser som anges i skriptet.

> [!NOTE]
> När det gäller ACID-tabeller skapas en ny kopia av data under tabellen.

1. Anslut till HDInsight-klustret med hjälp av en [SSH-klient (Secure Shell).](../hdinsight-hadoop-linux-use-ssh-unix.md)

1. Anslut till HiveServer2 med [Din Beeline-klient](../hadoop/apache-hadoop-use-hive-beeline.md) från din öppna SSH-session genom att ange följande kommando:

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; 
    do
        echo "Scanning Database: $d"
        echo "create database if not exists $d; use $d;" >> alltables.hql; 
        for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"`;
        do
            echo "Copying Table: $t"
            ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`;

            echo "$ddl;" >> alltables.hql;
            lowerddl=$(echo $ddl | awk '{print tolower($0)}')
            if [[ $lowerddl == *"'transactional'='true'"* ]]; then
                if [[ $lowerddl == *"partitioned by"* ]]; then
                    # partitioned
                    raw_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "CREATE TABLE .*" | cut -d"(" -f2- | cut -f1 -d")" | sed 's/`//g');
                    ptn_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "PARTITIONED BY .*" | cut -f1 -d")" | cut -d"(" -f2- | sed 's/`//g');
                    final_cols=$(echo "(" $raw_cols "," $ptn_cols ")")

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t $final_cols TBLPROPERTIES ('transactional'='false');";
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    parsed_ptn_cols=$(echo $ptn_cols| sed 's/ [a-z]*,/,/g' | sed '$s/\w*$//g');
                    echo "create table flattened_$t $final_cols;" >> alltables.hql;
                    echo "load data inpath '$dir' into table flattened_$t;" >> alltables.hql;
                    echo "insert into $t partition($parsed_ptn_cols) select * from flattened_$t;" >> alltables.hql;
                    echo "drop table flattened_$t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                else
                    # not partitioned
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t like $t TBLPROPERTIES ('transactional'='false');";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    echo "load data inpath '$dir' into table $t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                fi
            fi
            echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t;" >> alltables.hql;
        done;
    done
    ```

    Det här kommandot genererar en fil med namnet **alltables.hql**.

1. Avsluta SSH-sessionen. Ange sedan ett scp-kommando för att ladda ner **alltables.hql** lokalt.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Ladda upp **alltables.hql** till det *nya* HDInsight-klustret.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Använd sedan SSH för att ansluta till det *nya* HDInsight-klustret. Kör följande kod från SSH-sessionen:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.hql
    ```


## <a name="upgrade-metastore"></a>Uppgradera metabutik

När **metastore-kopian** är klar kör du ett schemauppgraderingsskript i [Skriptåtgärd](../hdinsight-hadoop-customize-cluster-linux.md) på det befintliga HDInsight 3.6-klustret för att uppgradera det nya metabutiken till Hive 3-schemat. Detta gör att databasen kan bifogas som HDInsight 4.0 metabutik.

Använd värdena i tabellen längre nedan. Ersätt `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` med lämpliga värden för den **kopierade** Hive-metabutiken, avgränsad med blanksteg. Ta inte med ".database.windows.net" när du anger SQL-servernamnet.

|Egenskap | Värde |
|---|---|
|Skripttyp|- Anpassad|
|Namn|Hive uppgradering|
|Bash skript URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Nodtyper|Head|
|Parametrar|ANVÄNDARNAMN FÖR SQLSERVERNAME-ANVÄNDARNAMN|

> [!Warning]  
> Uppgraderingen som konverterar METADATASCHEMAT FÖR HDInsight 3.6 till HDInsight 4.0-schemat kan inte återföras.

Du kan verifiera uppgraderingen genom att köra följande sql-fråga mot databasen:

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrera Hive-tabeller till HDInsight 4.0

När du har slutfört den tidigare uppsättningen steg för att migrera Hive Metastore till HDInsight 4.0, kommer tabellerna och databaserna som `show tables` registrerats i metabutiken att vara synliga inifrån HDInsight 4.0-klustret genom att köra eller `show databases` inifrån klustret. Se [Frågekörning i HDInsight-versioner](#query-execution-across-hdinsight-versions) för information om körning av frågor i HDInsight 4.0-kluster.

De faktiska data från tabellerna är dock inte tillgängliga förrän klustret har åtkomst till nödvändiga lagringskonton. Så här kontrollerar du att DITT HDInsight 4.0-kluster kan komma åt samma data som ditt gamla HDInsight 3.6-kluster:

1. Bestäm Azure-lagringskontot för din tabell eller databas.

1. Om ditt HDInsight 4.0-kluster redan körs ansluter du Azure-lagringskontot till klustret via Ambari. Om du ännu inte har skapat HDInsight 4.0-klustret kontrollerar du att Azure-lagringskontot anges som antingen det primära eller ett sekundärt klusterlagringskonto. Mer information om hur du lägger till lagringskonton i HDInsight-kluster finns i [Lägga till ytterligare lagringskonton i HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Distribuera nya HDInsight 4.0 och anslut till den nya metabutiken

När schemauppgraderingen är klar distribuerar du ett nytt HDInsight 4.0-kluster och ansluter den uppgraderade metabutiken. Om du redan har distribuerat 4.0 ställer du in den så att du kan ansluta till metabutiken från Ambari.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Köra schemamigreringsskript från HDInsight 4.0

Tabeller behandlas olika i HDInsight 3.6 och HDInsight 4.0. Därför kan du inte dela samma tabeller för kluster av olika versioner. Om du vill använda HDInsight 3.6 samtidigt som HDInsight 4.0 måste du ha separata kopior av data för varje version.

Din Hive-arbetsbelastning kan innehålla en blandning av ACID- och non-ACID-tabeller. En viktig skillnad mellan Hive på HDInsight 3.6 (Hive 2) och Hive på HDInsight 4.0 (Hive 3) är ACID-överensstämmelse för tabeller. I HDInsight 3.6 kräver aktivera hive acid-överensstämmelse ytterligare konfiguration, men i HDInsight 4.0-tabeller är ACID-kompatibla som standard. Den enda åtgärd som krävs före migreringen är att köra en större kompaktering mot ACID-tabellen på 3.6-klustret. Kör följande fråga från Hive-vyn eller från Beeline:

```sql
alter table myacidtable compact 'major';
```

Denna kompaktering krävs eftersom HDInsight 3.6 och HDInsight 4.0 ACID-tabeller förstår ACID-deltan på olika sätt. Komprimering upprätthåller en ren skiffer som garanterar konsekvens. Avsnitt 4 i [Hive-migreringsdokumentationen](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) innehåller vägledning för bulkkomprimering av HDInsight 3.6 ACID-tabeller.

När du har slutfört stegen för migrering och komprimering i metalager kan du migrera det faktiska lagret. När du har slutfört hive-lagermigreringen har HDInsight 4.0-lagret följande egenskaper:

|3.6 |4.0 |
|---|---|
|Externa tabeller|Externa tabeller|
|Icke-transaktionella hanterade tabeller|Externa tabeller|
|Transaktionshanterade tabeller|Hanterade tabeller|

Du kan behöva justera egenskaperna för distributionslagret innan du kör migreringen. Om du till exempel förväntar dig att en tabell kommer att nås av en tredje part (till exempel ett HDInsight 3.6-kluster) måste tabellen vara extern när migreringen är klar. I HDInsight 4.0 är alla hanterade tabeller transaktionella. Hanterade tabeller i HDInsight 4.0 bör därför endast nås av HDInsight 4.0-kluster.

När tabellegenskaperna har angetts korrekt kör du verktyget för migrering av Hive-distributionslager från en av klusterhuvudnoderna med SSH-skalet:

1. Anslut till klusterhuvudnoden med SSH. Instruktioner finns i [Ansluta till HDInsight med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Öppna ett inloggningsskal som Hive-användare genom att köra`sudo su - hive`
1. Bestäm dataplattformsstackens `ls /usr/hdp`version genom att köra . Då visas en versionssträng som du bör använda i nästa kommando.
1. Kör följande kommando från skalet. Ersätt `STACK_VERSION` med versionssträngen från föregående steg:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

När migreringsverktyget är klart är hive-lagret redo för HDInsight 4.0.

> [!Important]  
> Hanterade tabeller i HDInsight 4.0 (inklusive tabeller som migrerats från 3.6) bör inte nås av andra tjänster eller program, inklusive HDInsight 3.6-kluster.

## <a name="secure-hive-across-hdinsight-versions"></a>Säker Hive över HDInsight-versioner

Sedan HDInsight 3.6 integreras HDInsight med Azure Active Directory med HDInsight Enterprise Security Package (ESP). ESP använder Kerberos och Apache Ranger för att hantera behörigheterna för specifika resurser i klustret. Ranger-principer som distribueras mot Hive i HDInsight 3.6 kan migreras till HDInsight 4.0 med följande steg:

1. Navigera till panelen Ranger Service Manager i ditt HDInsight 3.6-kluster.
2. Navigera till principen **HIVE** och exportera principen till en json-fil.
3. Kontrollera att alla användare som avses i den exporterade principjisonen finns i det nya klustret. Om en användare refereras till i principjison men inte finns i det nya klustret, antingen lägga till användaren i det nya klustret eller ta bort referensen från principen.
4. Navigera till **panelen Ranger Service Manager** i ditt HDInsight 4.0-kluster.
5. Navigera till principen **HIVE** och importera rangerprincipen från steg 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Kontrollera kompatibilitet och ändra koder efter behov i testappen

När du migrerar arbetsbelastningar som befintliga program och frågor, kontrollera viktig information och dokumentation för ändringar och tillämpa ändringar efter behov. Om ditt HDInsight 3.6-kluster använder en delad Spark- och Hive-metabutik krävs [ytterligare konfiguration med Hive Warehouse Connector.](./apache-hive-warehouse-connector.md)

## <a name="deploy-new-app-for-production"></a>Distribuera ny app för produktion

Om du vill växla till det nya klustret kan du till exempel installera ett nytt klientprogram och använda det som en ny produktionsmiljö, eller så kan du uppgradera ditt befintliga klientprogram och växla till HDInsight 4.0.

## <a name="switch-hdinsight-40-to-the-production"></a>Växla HDInsight 4.0 till produktionen

Om skillnader skapades i metabutiken under testningen måste du uppdatera ändringarna precis innan du byter. I det här fallet kan du exportera & importera metabutiken och sedan uppgradera igen.

## <a name="remove-the-old-production"></a>Ta bort den gamla produktionen

När du har bekräftat att versionen är klar och fullt fungerande kan du ta bort version 3.6 och den tidigare metabutiken. Kontrollera att allt migreras innan du tar bort miljön.

## <a name="query-execution-across-hdinsight-versions"></a>Frågekörning i HDInsight-versioner

Det finns två sätt att köra och felsöka Hive/LLAP-frågor i ett HDInsight 3.6-kluster. HiveCLI ger en kommandoradsupplevelse och vyn Tez view/Hive ger ett GUI-baserat arbetsflöde.

I HDInsight 4.0 har HiveCLI ersatts med Beeline. HiveCLI är en sparsamhetsklient för Hiveserver 1 och Beeline är en JDBC-klient som ger åtkomst till Hiveserver 2. Beeline kan också användas för att ansluta till någon annan JDBC-kompatibel databasslutpunkt. Beeline finns tillgängligt out-of-box på HDInsight 4.0 utan någon installation som behövs.

I HDInsight 3.6 är GUI-klienten för interaktion med Hive-servern Ambari Hive View. HDInsight 4.0 levereras inte med Ambari View. Vi har gett våra kunder ett sätt att använda Data Analytics Studio (DAS), som inte är en central HDInsight-tjänst. DAS levereras inte med HDInsight-kluster out-of-the-box och är inte ett officiellt stödpaket. DAS kan dock installeras i klustret med hjälp av en [skriptåtgärd](../hdinsight-hadoop-customize-cluster-linux.md) på följande sätt:

|Egenskap | Värde |
|---|---|
|Skripttyp|- Anpassad|
|Namn|Das|
|Bash skript URI|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Nodtyper|Head|

Vänta 10 till 15 minuter och starta sedan `https://CLUSTERNAME.azurehdinsight.net/das/`Data Analytics Studio med den här webbadressen: .

En uppdatering av Ambari UI och / eller en omstart av alla Ambari komponenter kan krävas innan du öppnar DAS.

När DAS har installerats gör du följande om du inte ser de frågor som du har kört i visningsprogrammet för frågor:

1. Ange konfigurationerna för Hive, Tez och DAS enligt beskrivningen i [den här guiden för felsökning av DAS-installation](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Kontrollera att följande Azure-lagringskatalogkonfigurationer är sidblobar och att de `fs.azure.page.blob.dirs`visas under :
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Starta om HDFS, Hive, Tez och DAS på båda headnodes.

## <a name="next-steps"></a>Nästa steg

* [HDInsight 4,0 Meddelande](../hdinsight-version-release.md)
* [HDInsight 4,0 djupdykning](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID Tabeller](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
