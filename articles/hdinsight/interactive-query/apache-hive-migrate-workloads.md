---
title: Migrera Azure HDInsight 3,6 Hive-arbetsbelastningar till HDInsight 4,0
description: Lär dig hur du migrerar Apache Hive-arbetsbelastningar i HDInsight 3,6 till HDInsight 4,0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: eceb4b312476d701ec8ce4eb0ce4886621824b3a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841599"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrera Azure HDInsight 3,6 Hive-arbetsbelastningar till HDInsight 4,0

Det här dokumentet visar hur du migrerar Apache Hive-och LLAP-arbetsbelastningar på HDInsight 3,6 till HDInsight 4,0. HDInsight 4,0 innehåller nyare Hive-och LLAP-funktioner som materialiserade vyer och cachelagring av frågeresultat. När du migrerar dina arbets belastningar till HDInsight 4,0 kan du använda många nyare funktioner i Hive 3 som inte är tillgängliga på HDInsight 3,6.

Den här artikeln beskriver följande ämnen:

* Migrering av Hive-metadata till HDInsight 4,0
* Säker migrering av syror och icke-syror tabeller
* Bevarande av Hive-säkerhets principer i HDInsight-versioner
* Frågekörning och fel sökning från HDInsight 3,6 till HDInsight 4,0

En fördel med Hive är möjligheten att exportera metadata till en extern databas (kallas Hive-Metaarkiv). **Hive-metaarkiv** ansvarar för att lagra tabell statistik, inklusive tabell lagrings plats, kolumn namn och tabell index information. Metaarkiv-databasschemat skiljer sig mellan Hive-versioner. Det rekommenderade sättet att uppgradera Hive-metaarkiv säkert är att skapa en kopia och uppgradera kopian i stället för den aktuella produktions miljön.

## <a name="copy-metastore"></a>Kopiera metaarkiv

HDInsight 3,6 och HDInsight 4,0 kräver olika metaarkiv-scheman och kan inte dela en enda metaarkiv.

### <a name="external-metastore"></a>Extern metaarkiv

Skapa en ny kopia av din externa metaarkiv. Om du använder en extern metaarkiv är ett av de säkraste och enkla sätten att göra en kopia av metaarkiv att [återställa databasen](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) med ett annat namn med hjälp av funktionen för återställning av SQL Database.  Mer information om hur du kopplar en extern metaarkiv till ett HDInsight-kluster finns i [använda externa metadata butiker i Azure HDInsight](../hdinsight-use-external-metadata-stores.md) .

### <a name="internal-metastore"></a>Internt metaarkiv

Om du använder den interna metaarkiv kan du använda frågor för att exportera objekt definitioner i Hive-metaarkiv och importera dem till en ny databas.

1. Anslut till HDInsight-klustret med hjälp av en [SSH-klient (Secure Shell)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Anslut till HiveServer2 med [Beeline-klienten](../hadoop/apache-hadoop-use-hive-beeline.md) från den öppna SSH-sessionen genom att ange följande kommando:

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; do echo "create database $d; use $d;" >> alltables.sql; for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"` ; do ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

    Det här kommandot skapar en fil med namnet **alltables. SQL**. Eftersom standard databasen inte kan tas bort/återskapas kan du ta bort `create database default;`-instruktionen i **alltables. SQL**.

1. Avsluta SSH-sessionen. Ange sedan ett SCP-kommando för att ladda ned **alltables. SQL** lokalt.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.sql c:/hdi
    ```

1. Ladda upp **alltables. SQL** till det *nya* HDInsight-klustret.

    ```bash
    scp c:/hdi/alltables.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Använd sedan SSH för att ansluta till det *nya* HDInsight-klustret. Kör följande kod från SSH-sessionen:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.sql
    ```

## <a name="upgrade-metastore"></a>Uppgradera metaarkiv

När metaarkiv- **kopieringen** är klar kör du ett skript för schema uppgradering i [skript åtgärd](../hdinsight-hadoop-customize-cluster-linux.md) på det befintliga HDInsight 3,6-klustret för att uppgradera det nya metaarkiv till Hive 3-schemat. Detta gör att databasen kan anslutas som HDInsight 4,0 metaarkiv.

Använd värdena i tabellen nedan. Ersätt `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` med lämpliga värden för den **kopierade** Hive-metaarkiv, avgränsade med blank steg. Ta inte med ". database.windows.net" när du anger SQL Server-namnet.

|Egenskap | Värde |
|---|---|
|Skript typ|– Anpassad|
|Namn|Hive-uppgradering|
|Bash-skript-URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Node-typ (er)|Huvud|
|Parametrar|LÖSEN ORD FÖR SQLSERVERNAME DATABASENAME USERNAME|

> [!Warning]  
> Uppgraderingen som konverterar HDInsight 3,6-metadata-schemat till HDInsight 4,0-schemat kan inte ångras.

Du kan verifiera uppgraderingen genom att köra följande SQL-fråga mot databasen:

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrera Hive-tabeller till HDInsight 4,0

När du har slutfört den föregående uppsättningen steg för att migrera Hive-Metaarkiv till HDInsight 4,0 kommer de tabeller och databaser som registrerats i metaarkiv att visas inifrån HDInsight 4,0-klustret genom att köra `show tables` eller `show databases` inifrån klustret. Information om hur du kör frågor i HDInsight 4,0-kluster finns i [köra frågor i HDInsight-versioner](#query-execution-across-hdinsight-versions) .

De faktiska data från tabellerna är dock inte tillgängliga förrän klustret har åtkomst till de nödvändiga lagrings kontona. För att se till att ditt HDInsight 4,0-kluster har åtkomst till samma data som ditt gamla HDInsight 3,6-kluster utför du följande steg:

1. Bestäm Azure Storage-kontot för din tabell eller databas.

1. Om ditt HDInsight 4,0-kluster redan körs ansluter du Azure Storage-kontot till klustret via Ambari. Om du inte redan har skapat HDInsight 4,0-klustret, kontrollerar du att Azure Storage-kontot har angetts som antingen det primära eller sekundära kluster lagrings kontot. Mer information om hur du lägger till lagrings konton i HDInsight-kluster finns i [lägga till ytterligare lagrings konton i HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Distribuera nya HDInsight 4,0 och Anslut till den nya metaarkiv

När Schema uppgraderingen har slutförts distribuerar du ett nytt HDInsight 4,0-kluster och ansluter den uppgraderade metaarkiv. Om du redan har distribuerat 4,0 anger du det så att du kan ansluta till metaarkiv från Ambari.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Kör skript för schema migrering från HDInsight 4,0

Tabeller behandlas annorlunda i HDInsight 3,6 och HDInsight 4,0. Därför kan du inte dela samma tabeller för kluster med olika versioner. Om du vill använda HDInsight 3,6 på samma tid som HDInsight 4,0 måste du ha separata kopior av data för varje version.

Din Hive-arbetsbelastning kan innehålla en blandning av syror och icke-syre tabeller. En viktig skillnad mellan Hive på HDInsight 3,6 (Hive 2) och Hive på HDInsight 4,0 (Hive 3) är syra-efterlevnad för tabeller. I HDInsight 3,6 kräver Hive-kompatibilitet ytterligare konfiguration, men i HDInsight 4,0-tabeller är syra-kompatibla som standard. Den enda åtgärd som krävs innan migreringen är att köra en stor komprimering mot sur-tabellen i 3,6-klustret. Kör följande fråga från Hive-vyn eller från Beeline:

```sql
alter table myacidtable compact 'major';
```

Den här komprimeringen krävs eftersom HDInsight 3,6 och HDInsight 4,0 syra tabeller förstår sur delta på olika sätt. Komprimeringen tillämpar en ren bakgrunds kraft som garanterar konsekvens. Avsnitt 4 i [dokumentationen för Hive-migrering](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) innehåller vägledning för Mass komprimering av HDInsight 3,6 syra tabeller.

När du har slutfört metaarkiv-migreringen och komprimerings stegen kan du migrera det aktuella lagret. När du har slutfört strukturen för Hive-lagret har HDInsight 4,0-lagret följande egenskaper:

|3.6 |4.0 |
|---|---|
|Externa tabeller|Externa tabeller|
|Icke-transaktionella hanterade tabeller|Externa tabeller|
|Transaktionella hanterade tabeller|Hanterade tabeller|

Du kan behöva justera egenskaperna för ditt lager innan du utför migreringen. Om du till exempel förväntar dig att en viss tabell ska kommas åt av en tredje part (till exempel ett HDInsight 3,6-kluster) måste tabellen vara extern när migreringen är klar. I HDInsight 4,0 är alla hanterade tabeller transaktionella. Därför bör hanterade tabeller i HDInsight 4,0 endast nås av HDInsight 4,0-kluster.

När tabell egenskaperna har angetts korrekt kör du verktyget Hive-migrering från ett av klustrets huvudnoderna med SSH-gränssnittet:

1. Anslut till klustrets huvudnoden med SSH. Instruktioner finns i [ansluta till HDInsight med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Öppna ett inloggnings gränssnitt som Hive-användare genom att köra `sudo su - hive`
1. Ta reda på data plattforms stack versionen genom att köra `ls /usr/hdp`. Då visas en versions sträng som du bör använda i nästa kommando.
1. Kör följande kommando från gränssnittet. Ersätt `STACK_VERSION` med versions strängen från föregående steg:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

När Migreringsverktyg är klar är ditt Hive-lager klart för HDInsight 4,0.

> [!Important]  
> Hanterade tabeller i HDInsight 4,0 (inklusive tabeller som migrerats från 3,6) bör inte nås av andra tjänster eller program, inklusive HDInsight 3,6-kluster.

## <a name="secure-hive-across-hdinsight-versions"></a>Säker Hive i HDInsight-versioner

Eftersom HDInsight 3,6 integrerar HDInsight med Azure Active Directory som använder HDInsight Enterprise Security Package (ESP). ESP använder Kerberos och Apache Ranger för att hantera behörigheter för vissa resurser i klustret. Ranger-principer som distribueras mot Hive i HDInsight 3,6 kan migreras till HDInsight 4,0 med följande steg:

1. Navigera till fönstret Ranger Service Manager i ditt HDInsight 3,6-kluster.
2. Navigera till principen med namnet **HIVE** och exportera principen till en JSON-fil.
3. Se till att alla användare som anges i den exporterade princip-JSON finns i det nya klustret. Om en användare refereras till i princip-JSON men inte finns i det nya klustret kan du antingen lägga till användaren i det nya klustret eller ta bort referensen från principen.
4. Navigera till fönstret **Ranger Service Manager** i ditt HDInsight 4,0-kluster.
5. Navigera till principen med namnet **HIVE** och importera Ranger policy JSON från steg 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Kontrol lera kompatibilitet och ändra koder efter behov i appen test

När du migrerar arbets belastningar som befintliga program och frågor bör du läsa viktig information och dokumentation för ändringar och tillämpa ändringarna vid behov. Om ditt HDInsight 3,6-kluster använder en delad Spark-och Hive-metaarkiv krävs [ytterligare konfiguration med hjälp av Hive Warehouse Connector](./apache-hive-warehouse-connector.md) .

## <a name="deploy-new-app-for-production"></a>Distribuera ny app för produktion

För att växla till det nya klustret, t. ex. kan du installera ett nytt klient program och använda det som en ny produktions miljö, eller så kan du uppgradera det befintliga klient programmet och växla till HDInsight 4,0.

## <a name="switch-hdinsight-40-to-the-production"></a>Växla HDInsight 4,0 till produktion

Om skillnaderna skapades i metaarkiv vid testning måste du uppdatera ändringarna precis innan du växlar. I så fall kan du exportera & Importera metaarkiv och sedan uppgradera igen.

## <a name="remove-the-old-production"></a>Ta bort den gamla produktionen

När du har bekräftat att versionen är slutförd och fullt fungerande kan du ta bort version 3,6 och föregående metaarkiv. Kontrol lera att allt är migrerat innan du tar bort miljön.

## <a name="query-execution-across-hdinsight-versions"></a>Frågekörningen i HDInsight-versioner

Det finns två sätt att köra och felsöka Hive/LLAP-frågor i ett HDInsight 3,6-kluster. HiveCLI tillhandahåller en kommando rad upplevelse och vyn Tez View/Hive innehåller ett GUI-baserat arbets flöde.

I HDInsight 4,0 har HiveCLI ersatts med Beeline. HiveCLI är en Thrift-klient för Hiveserver 1 och Beeline är en JDBC-klient som ger åtkomst till Hiveserver 2. Beeline kan också användas för att ansluta till en annan JDBC-kompatibel databas slut punkt. Beeline är tillgängligt i HDInsight 4,0 utan någon installation.

I HDInsight 3,6 är GUI-klienten för att interagera med Hive-Server den Ambari Hive-vyn. HDInsight 4,0 levereras inte med Ambari-vyn. Vi har gett oss ett sätt för våra kunder att använda data Analytics Studio (DAS), som inte är en kärn tjänst för HDInsight. DAS levereras inte med HDInsight-kluster direkt och är inte ett paket som stöds officiellt. DAS kan dock installeras i klustret med hjälp av en [skript åtgärd](../hdinsight-hadoop-customize-cluster-linux.md) enligt följande:

|Egenskap | Värde |
|---|---|
|Skript typ|– Anpassad|
|Namn|DAS|
|Bash-skript-URI|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Node-typ (er)|Huvud|

Vänta 10 till 15 minuter och starta sedan data Analytics Studio med hjälp av följande URL: `https://CLUSTERNAME.azurehdinsight.net/das/`.

En uppdatering av Ambari-ANVÄNDARGRÄNSSNITTET och/eller en omstart av alla Ambari-komponenter kan krävas innan du får åtkomst till DAS.

När DAS har installerats, om du inte ser frågorna som du har kört i visnings programmet för frågor, så gör du följande:

1. Ange konfigurationer för Hive, Tez och DAS enligt beskrivningen i [den här hand boken för fel sökning av das-installation](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Se till att följande konfiguration för Azure Storage-katalogen är sid-blobbar och att de visas under `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Starta HDFS, Hive, Tez och DAS på båda huvudnoderna.

## <a name="next-steps"></a>Nästa steg

* [HDInsight 4,0-meddelande](../hdinsight-version-release.md)
* [HDInsight 4,0-djup](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tabeller för Hive 3-syror](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
