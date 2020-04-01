---
title: Migrera Apache Kafka-arbetsbelastningar till Azure HDInsight 4.0
description: Lär dig hur du migrerar Apache Kafka-arbetsbelastningar på HDInsight 3.6 till HDInsight 4.0.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 0a31c6cf32222277e033aacf7d04622c54aef9ea
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437004"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Migrera Apache Kafka-arbetsbelastningar till Azure HDInsight 4.0

Azure HDInsight 4.0 erbjuder de senaste komponenterna med öppen källkod med betydande förbättringar i prestanda, anslutning och säkerhet. I det här dokumentet beskrivs hur du migrerar Apache Kafka-arbetsbelastningar på HDInsight 3.6 till HDInsight 4.0. När du har migrerat dina arbetsbelastningar till HDInsight 4.0 kan du använda många av de nya funktionerna som inte är tillgängliga på HDInsight 3.6.

## <a name="hdinsight-36-kafka-migration-paths"></a>HDInsight 3,6 Kafka migrationsvägar

HDInsight 3.6 stöder två versioner av Kafka: 1.0.0 och 1.1.0. HDInsight 4.0 stöder versionerna 1.1.0 och 2.1.0. Beroende på vilken version av Kafka och vilken version av HDInsight du vill köra finns det flera migreringsvägar som stöds. Dessa banor förklaras nedan och illustreras i följande diagram.

* **Kör både Kafka och HDInsight på senaste versionerna (rekommenderas):** Migrera en HDInsight 3.6 och Kafka 1.0.0 eller 1.1.0 ansökan till HDInsight 4.0 med Kafka 2.1.0 (vägar D och E nedan).
* **Kör HDInsight på den senaste versionen, men Kafka bara på en nyare version:** Migrera en HDInsight 3.6 och Kafka 1.0.0 ansökan till HDInsight 4.0 med Kafka 1.1.0 (väg B nedan).
* **Kör HDInsight på den senaste versionen, behåll Kafka version:** Migrera en HDInsight 3.6 och Kafka 1.1.0 ansökan till HDInsight 4.0 med Kafka 1.1.0 (väg C nedan).
* **Kör Kafka på en nyare version, behåll HDInsight version:** Migrera en Kafka 1.0.0 ansökan till 1.1.0 och stanna på HDInsight 3.6 (väg A nedan). Observera att det här alternativet fortfarande kräver distribution av ett nytt kluster. Det går inte att uppgradera Kafka-versionen i ett befintligt kluster. När du har skapat ett kluster med den version du vill använda migrerar du Kafka-klienterna så att de använder det nya klustret.

![Uppgradera vägar för Apache Kafka på 3,6](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>Apache Kafka versioner

### <a name="kafka-110"></a>Kafka 1.1.0
  
Om du migrerar från Kafka 1.0.0 till 1.1.0 kan du dra nytta av följande nya funktioner:

* Förbättringar av Kafka-styrenheten påskyndar kontrollerad avstängning, så du kan starta om mäklare och återställa från problem snabbare. 
* Förbättringar i [FetchRequests logik](https://issues.apache.org/jira/browse/KAFKA-6254) som gör att du kan ha fler partitioner (och därmed fler ämnen) på klustret. 
* Kafka Connect stöder [postrubriker](https://issues.apache.org/jira/browse/KAFKA-5142) och [reguljära uttryck](https://issues.apache.org/jira/browse/KAFKA-3073) för ämnen. 

En fullständig lista över uppdateringar finns i [Apache Kafka 1.1-viktig information](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html).

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

Om du migrerar till Kafka 2.1 kan du dra nytta av följande funktioner:

* Bättre mäklaråtersåterlighet på grund av ett förbättrat replikeringsprotokoll.
* Nya funktioner i KafkaAdminClient API.
* Konfigurerbar kvothantering.
* Stöd för Zstandard komprimering.

En fullständig lista över uppdateringar finns i [Apache Kafka 2.0-versionsanteckningar](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) och [Apache Kafka 2.1-versionsanteckningar](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html).

## <a name="kafka-client-compatibility"></a>Kafka-klientkompatibilitet

Nya Kafka mäklare stödja äldre kunder. [KIP-35 - Hämta protokollversion](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) infört en mekanism för att dynamiskt bestämma funktionaliteten hos en Kafka mäklare och [KIP-97: Förbättrad Kafka Client RPC Kompatibilitetspolicy](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) infört en ny kompatibilitetspolicy och garantier för Java-klienten. Tidigare var en Kafka-klient tvungen att interagera med en mäklare av samma version eller en nyare version. Nu, nyare versioner av Java-klienter och andra klienter som `librdkafka` stöder KIP-35 som kan falla tillbaka till äldre begäran typer eller kasta lämpliga fel om funktionaliteten inte är tillgänglig.

![Uppgradera Kafka-klientkompatibilitet](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

Observera att det inte betyder att klienten stöder äldre mäklare.  Mer information finns i [Kompatibilitetsmatris](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix).

## <a name="general-migration-process"></a>Allmän migreringsprocess

Följande migreringsvägledning förutsätter ett Apache Kafka 1.0.0- eller 1.1.0-kluster som distribueras på HDInsight 3.6 i ett enda virtuellt nätverk. Den befintliga mäklaren har vissa ämnen och används aktivt av producenter och konsumenter.

![Nuvarande Kafka förmodad miljö](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

Gör så här för att slutföra migreringen:

1. **Distribuera ett nytt HDInsight 4.0-kluster och klienter för test.** Distribuera ett nytt HDInsight 4.0 Kafka-kluster. Om flera Kafka-klusterversioner kan väljas rekommenderar vi att du väljer den senaste versionen. Efter distributionen anger du vissa parametrar efter behov och skapar ett ämne med samma namn som din befintliga miljö. Ställ också in TLS- och byok-kryptering (bring-your-own-key) efter behov. Kontrollera sedan om det fungerar korrekt med det nya klustret.

    ![Distribuera nya HDInsight 4.0-kluster](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **Växla klustret för producentprogrammet och vänta tills alla ködata förbrukas av de aktuella konsumenterna.** När det nya HDInsight 4.0 Kafka-klustret är klart växlar du det befintliga producentmålet till det nya klustret. Lämna det som det är tills den befintliga konsumentappen har förbrukat alla data från det befintliga klustret.

    ![Växla kluster för producentapp](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **Växla klustret på konsumentprogrammet.** När du har bekräftat att det befintliga konsumentprogrammet har förbrukat alla data från det befintliga klustret växlar du anslutningen till det nya klustret.

    ![Växla kluster på konsumentapp](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **Ta bort det gamla klustret och testa program efter behov.** När växeln är klar och fungerar korrekt, ta bort den gamla HDInsight 3,6 Kafka kluster och producenter och konsumenter som används i testet efter behov.

## <a name="next-steps"></a>Nästa steg

* [Optimera prestanda för Apache Kafka HDInsight-kluster](apache-kafka-performance-tuning.md)
* [Snabbstart: Skapa Apache Kafka-kluster i Azure HDInsight med Azure-portal](apache-kafka-get-started.md)
