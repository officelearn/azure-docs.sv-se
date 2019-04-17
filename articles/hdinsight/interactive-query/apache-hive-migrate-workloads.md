---
title: Migrera Azure HDInsight 3.6 Hive-arbetsbelastningar till HDInsight 4.0
description: Lär dig hur du migrerar Apache Hive-arbetsbelastningar på HDInsight 3.6 till HDInsight 4.0.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 04/15/2019
ms.openlocfilehash: 708df64802ace17fa77b4e0a695c9f1c3bd18a77
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610266"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrera Azure HDInsight 3.6 Hive-arbetsbelastningar till HDInsight 4.0

Det här dokumentet visar hur du migrerar Apache Hive och LLAP arbetsbelastningar på HDInsight 3.6 till HDInsight 4.0. HDInsight 4.0 innehåller nyare Hive och LLAP funktioner, till exempel materialiserade vyer och cachelagring av resultatet av frågan. När du migrerar dina arbetsbelastningar till HDInsight 4.0 kan du använda många nya funktioner i Hive 3 som inte är tillgängliga på HDInsight 3.6.

Den här artikeln innehåller följande ämnen:

* Migrering av Hive-metadata till HDInsight 4.0
* Säker migrering av ACID- och icke-ACID-tabeller
* Bevara Hive säkerhetsprinciper för HDInsight-versioner
* Frågekörning och felsökning från HDInsight 3.6 till HDInsight 4.0

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Migrera Apache Hive-metadata till HDInsight 4.0

En fördel med Hive är möjligheten att exportera metadata till en extern databas (kallas Hive-Metaarkiv). Den **Hive-Metaarkiv** ansvarar för att lagra tabellstatistik, inklusive tabell lagringsplats, kolumnnamn och tabellinformation för indexet. Databasschemat metaarkiv skiljer sig mellan Hive-versioner. Gör följande om du vill uppgradera en Hive-Metaarkiv på HDInsight 3.6 så att den är kompatibel med HDInsight 4.0.

1. Skapa en ny kopia av din extern metastore. HDInsight 3.6 och 4.0 för HDInsight kräver olika metaarkiv scheman och kan inte dela ett enda metaarkiv.
1. Koppla den nya kopian av metaarkiv till a) ett befintligt kluster för HDInsight 4.0 eller (b) ett kluster som du skapar för första gången. Se [använda extern metadatalagring i Azure HDInsight](../hdinsight-use-external-metadata-stores.md) vill veta mer om att koppla en extern metastore till ett HDInsight-kluster. När Metaarkiv är att den automatiskt konverteras till ett metaarkiv 4.0-kompatibel.

> [!Warning]
> Uppgraderingen som konverterar metadataschema HDInsight 3.6 HDInsight 4.0-schemat kan inte ångras.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrera Hive-tabeller till HDInsight 4.0

När du har slutfört den tidigare uppsättningen steg för att migrera Hive-Metaarkiv till HDInsight 4.0, tabeller och databaser som har registrerats i metaarkiv syns från inom 4.0 HDInsight-kluster genom att köra `show tables` eller `show databases` från i klustret . Se [Frågekörningen mellan olika versioner av HDInsight](#query-execution-across-hdinsight-versions) information om körningen av frågan i 4.0 HDInsight-kluster.

Faktiska data från tabeller, men är inte tillgänglig förrän klustret har åtkomst till de nödvändiga lagringskontona. Om du vill se till att ditt kluster i HDInsight 4.0 kan komma åt samma data som ditt gamla kluster med HDInsight 3.6, gör du följande:

1. Fastställa Azure storage-konto i din tabell eller databas med hjälp av beskriver formaterad.
2. Om ditt kluster i HDInsight 4.0 körs kan du ansluta Azure storage-kontot till klustret via Ambari. Om du inte har skapat klustret HDInsight 4.0, se till Azure storage-kontot har angetts som primärt eller en sekundär kluster storage-konto. Mer information om att lägga till lagringskonton till HDInsight-kluster finns i [lägga till ytterligare lagringskonton till HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> Tabellerna behandlas på olika sätt på HDInsight 3.6 och HDInsight 4.0. Du kan inte dela samma tabellerna för kluster på olika versioner av den anledningen. Om du vill använda HDInsight 3.6 samtidigt som HDInsight 4.0 måste du ha separata kopior av data för varje version.

Hive-arbetsbelastningar kan innehålla en blandning av ACID- och icke-ACID-tabeller. En viktig skillnad mellan Hive på HDInsight 3.6 (Hive-2) och Hive på HDInsight 4.0 (Hive-3) är ACID-efterlevnad för tabeller. Ytterligare konfiguration krävs för att aktivera Hive ACID-kompatibilitet i HDInsight 3.6, men i HDInsight 4.0 tabeller är ACID-kompatibla som standard. Den enda åtgärden som krävs innan migreringen är att köra en större komprimering mot tabellen ACID på 3.6-klustret. Kör följande fråga från Hive-vyn eller från Beeline:

```bash
alter table myacidtable compact 'major';
```

Den här komprimeringen är nödvändigt eftersom HDInsight 3.6-och HDInsight 4.0 ACID förstå ACID deltan olika. Komprimering framtvingar grunden som garanterar tabell konsekvens. När komprimering är klar, blir de föregående stegen för metaarkiv och tabell migrering tillräckligt för att använda alla HDInsight 3.6 ACID-tabeller i HDInsight 4.0.

## <a name="secure-hive-across-hdinsight-versions"></a>Skydda Hive i HDInsight-versioner

Eftersom HDInsight 3.6, HDInsight kan integreras med Azure Active Directory med HDInsight Enterprise Security Package (ESP). ESP använder Kerberos- och Apache Ranger för att hantera behörigheter för specifika resurser i klustret. Ranger-principer som distribuerats mot Hive i HDInsight 3.6 kan migreras till HDInsight 4.0 med följande steg:

1. Gå till panelen Ranger Service Manager i din HDInsight 3.6-klustret.
2. Navigera till principen med namnet **HIVE** och exportera principen till en json-fil.
3. Se till att alla användare som anges i den exporterade princip-json finns i det nya klustret. Om en användare är enligt principen json men finns inte i det nya klustret, Lägg till användaren till det nya klustret eller ta bort referensen från principen.
4. Navigera till den **Ranger Service Manager** panelen i ditt kluster i HDInsight 4.0.
5. Navigera till principen med namnet **HIVE** och importera princip-json ranger från steg 2.

## <a name="query-execution-across-hdinsight-versions"></a>Frågekörning mellan olika versioner av HDInsight

Det finns två sätt att köra och felsöka Hive/LLAP frågor i ett HDInsight 3.6-klustret. HiveCLI innehåller en kommandoradsmiljö och visa/Hive Tez-vyn innehåller ett GUI-baserade arbetsflöde.

I HDInsight 4.0 har HiveCLI ersatts med Beeline. HiveCLI är en thrift-klient för Hiveserver 1 och Beeline är en JDBC-klient som ger åtkomst till Hiveserver 2. Beeline kan också användas för att ansluta till alla andra JDBC-kompatibel databas-slutpunkt. Beeline är tillgängligt out-of-box HDInsight 4.0 utan någon installation som behövs.

GUI-klienten för att interagera med Hive-servern är i HDInsight 3.6, Ambari Hive-vy. HDInsight 4.0 ersätter Hive-vy med Hortonworks Data Analytics Studio (DAS). DAS medföljer inte HDInsight-kluster out of box och är inte ett paket som stöds. Dock kan DAS installeras i klustret på följande sätt:

1. Ladda ned den [DAS paketera installationsskriptet](https://hdiconfigactions.blob.core.windows.net/dasinstaller/install-das-mpack.sh) och kör den på båda-klustrets huvudnoder. Inte köra det här skriptet som en skriptåtgärd.
2. Ladda ned den [DAS tjänsten installationsskriptet](https://hdiconfigactions.blob.core.windows.net/dasinstaller/install-das-component.sh) och köra den som en skriptåtgärd. Välj **Huvudnoderna** som nodtyp valt från skriptet åtgärd-gränssnittet.
3. När skriptåtgärd har slutförts går du till Ambari och välj **Data Analytics Studio** från listan över tjänster. Alla DAS-tjänster stoppas. I det övre högra hörnet väljer **åtgärder** och **starta**. Du kan nu köra och felsöka frågor med DAS.

När DAS är installerad, om du inte ser de frågor som du har kört i visningsprogrammet för frågor, gör du följande steg:

1. Ange konfigurationer för Hive Tez och DAS enligt beskrivningen i [den här guiden för att felsöka DAS installation](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Se till att följande Azure storage directory konfigurationer är sidblobar och att de är listade `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Starta om HDFS, Hive, Tez och DAS på både huvudnoderna.

## <a name="next-steps"></a>Nästa steg

* [HDInsight 4.0 meddelande](../hdinsight-version-release.md)
* [HDInsight 4.0 djupdykning](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive-3 ACID-tabeller](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)