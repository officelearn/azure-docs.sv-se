---
title: Migrera Azure HDInsight 3,6 Hive-arbetsbelastningar till HDInsight 4,0
description: Lär dig hur du migrerar Apache Hive-arbetsbelastningar i HDInsight 3,6 till HDInsight 4,0.
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: b9bf3b41bcd0a79027c5dd9a4f3df979fb0bd9f0
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250156"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrera Azure HDInsight 3,6 Hive-arbetsbelastningar till HDInsight 4,0

Det här dokumentet visar hur du migrerar Apache Hive-och LLAP-arbetsbelastningar på HDInsight 3,6 till HDInsight 4,0. HDInsight 4,0 innehåller nyare Hive-och LLAP-funktioner som materialiserade vyer och cachelagring av frågeresultat. När du migrerar dina arbets belastningar till HDInsight 4,0 kan du använda många nyare funktioner i Hive 3 som inte är tillgängliga på HDInsight 3,6.

Den här artikeln beskriver följande ämnen:

* Migrering av Hive-metadata till HDInsight 4,0
* Säker migrering av syror och icke-syror tabeller
* Bevarande av Hive-säkerhets principer i HDInsight-versioner
* Frågekörning och fel sökning från HDInsight 3,6 till HDInsight 4,0

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Migrera Apache Hive metadata till HDInsight 4,0

En fördel med Hive är möjligheten att exportera metadata till en extern databas (kallas Hive-Metaarkiv). **Hive-metaarkiv** ansvarar för att lagra tabell statistik, inklusive tabell lagrings plats, kolumn namn och tabell index information. Metaarkiv-databasschemat skiljer sig mellan Hive-versioner. Gör följande för att uppgradera en HDInsight 3,6 Hive-Metaarkiv så att den är kompatibel med HDInsight 4,0.

1. Skapa en ny kopia av din externa metaarkiv. HDInsight 3,6 och HDInsight 4,0 kräver olika metaarkiv-scheman och kan inte dela en enda metaarkiv. Mer information om hur du kopplar en extern metaarkiv till ett HDInsight-kluster finns i [använda externa metadata butiker i Azure HDInsight](../hdinsight-use-external-metadata-stores.md) . 
2. Starta en skript åtgärd mot ditt HDI 3,6-kluster med "Head Nodes" som nodtypen för körning. Klistra in följande URI i text rutan som är markerad som "bash script https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh URI":. I text rutan som marker ATS som "argument" anger du Server namn, databas, användar namn  och lösen ord för den kopierade Hive-metaarkiv, avgränsade med blank steg. Ta inte med ". database.windows.net" när du anger servername.

> [!Warning]
> Uppgraderingen som konverterar HDInsight 3,6-metadata-schemat till HDInsight 4,0-schemat kan inte ångras.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrera Hive-tabeller till HDInsight 4,0

När du har slutfört den föregående uppsättningen steg för att migrera Hive-metaarkiv till HDInsight 4,0 kommer de tabeller och databaser som registrerats i metaarkiv att visas inifrån HDInsight 4,0-klustret genom `show tables` att `show databases` köra eller från i klustret . Information om hur du kör frågor i HDInsight 4,0-kluster finns i [köra frågor i HDInsight-versioner](#query-execution-across-hdinsight-versions) .

De faktiska data från tabellerna är dock inte tillgängliga förrän klustret har åtkomst till de nödvändiga lagrings kontona. För att se till att ditt HDInsight 4,0-kluster har åtkomst till samma data som ditt gamla HDInsight 3,6-kluster utför du följande steg:

1. Bestäm Azure Storage-kontot för din tabell eller databas med hjälp av Beskriv formaterad.
2. Om ditt HDInsight 4,0-kluster redan körs ansluter du Azure Storage-kontot till klustret via Ambari. Om du inte redan har skapat HDInsight 4,0-klustret, kontrollerar du att Azure Storage-kontot har angetts som antingen det primära eller sekundära kluster lagrings kontot. Mer information om hur du lägger till lagrings konton i HDInsight-kluster finns i [lägga till ytterligare lagrings konton i HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> Tabeller behandlas annorlunda i HDInsight 3,6 och HDInsight 4,0. Därför kan du inte dela samma tabeller för kluster med olika versioner. Om du vill använda HDInsight 3,6 på samma tid som HDInsight 4,0 måste du ha separata kopior av data för varje version.

Din Hive-arbetsbelastning kan innehålla en blandning av syror och icke-syre tabeller. En viktig skillnad mellan Hive på HDInsight 3,6 (Hive 2) och Hive på HDInsight 4,0 (Hive 3) är syra-efterlevnad för tabeller. I HDInsight 3,6 kräver Hive-kompatibilitet ytterligare konfiguration, men i HDInsight 4,0-tabeller är syra-kompatibla som standard. Den enda åtgärd som krävs innan migreringen är att köra en stor komprimering mot sur-tabellen i 3,6-klustret. Kör följande fråga från Hive-vyn eller från Beeline:

```bash
alter table myacidtable compact 'major';
```

Den här komprimeringen krävs eftersom HDInsight 3,6 och HDInsight 4,0 syra tabeller förstår sur delta på olika sätt. Komprimeringen tillämpar en ren bakgrunds kraft som garanterar konsekvens. Avsnitt 4 i [dokumentationen för Hive-migrering](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) innehåller vägledning för Mass komprimering av HDInsight 3,6 syra tabeller.

När du har slutfört metaarkiv-migreringen och komprimerings stegen kan du migrera det aktuella lagret. När du har slutfört strukturen för Hive-lagret har HDInsight 4,0-lagret följande egenskaper:

* Externa tabeller i HDInsight 3,6 kommer att vara externa tabeller i HDInsight 4,0
* Icke-transaktionella hanterade tabeller i HDInsight 3,6 kommer att vara externa tabeller i HDInsight 4,0
* Transaktionella hanterade tabeller i HDInsight 3,6 kommer att vara hanterade tabeller i HDInsight 4,0

Du kan behöva justera egenskaperna för ditt lager innan du utför migreringen. Om du till exempel förväntar dig att en viss tabell ska kommas åt av en tredje part (till exempel ett HDInsight 3,6-kluster) måste tabellen vara extern när migreringen är klar. I HDInsight 4,0 är alla hanterade tabeller transaktionella. Därför bör hanterade tabeller i HDInsight 4,0 endast nås av HDInsight 4,0-kluster.

När tabell egenskaperna har angetts korrekt kör du verktyget Hive-migrering från ett av klustrets huvudnoderna med SSH-gränssnittet:

1. Anslut till klustrets huvudnoden med SSH. Instruktioner finns i [ansluta till HDInsight med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Öppna ett inloggnings gränssnitt som Hive-användare genom att köra`sudo su - hive`
1. Fastställ stack-versionen för Hortonworks Data Platform genom att `ls /usr/hdp`köra. Då visas en versions sträng som du bör använda i nästa kommando.
1. Kör följande kommando från gränssnittet. Ersätt `${{STACK_VERSION}}` med versions strängen från föregående steg:

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true  -m automatic  automatic  --modifyManagedTables --oldWarehouseRoot /apps/hive/warehouse
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

## <a name="query-execution-across-hdinsight-versions"></a>Frågekörningen i HDInsight-versioner

Det finns två sätt att köra och felsöka Hive/LLAP-frågor i ett HDInsight 3,6-kluster. HiveCLI tillhandahåller en kommando rad upplevelse och vyn Tez View/Hive innehåller ett GUI-baserat arbets flöde.

I HDInsight 4,0 har HiveCLI ersatts med Beeline. HiveCLI är en Thrift-klient för Hiveserver 1 och Beeline är en JDBC-klient som ger åtkomst till Hiveserver 2. Beeline kan också användas för att ansluta till en annan JDBC-kompatibel databas slut punkt. Beeline är tillgängligt i HDInsight 4,0 utan någon installation.

I HDInsight 3,6 är GUI-klienten för att interagera med Hive-Server den Ambari Hive-vyn. HDInsight 4,0 ersätter Hive-vyn med Hortonworks Data Analytics Studio (DAS). DAS levereras inte med HDInsight-kluster och är inte ett paket som stöds officiellt. DAS kan dock installeras i klustret på följande sätt:

Starta en skript åtgärd mot klustret med "Head Nodes" som nodtyp för körning. Klistra in följande URI i text rutan som är markerad som "bash script URI": https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

Data Analytics Studio kan startas med URL: https://\<kluster namn >. azurehdinsight. net/Das/



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
