---
title: Komponenter med hög tillgänglighet i Azure HDInsight
description: Översikt över de olika komponenter med hög tillgänglighet som används av HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 38fb45fd339b5e2c7cab6f66a1ed6c0df73fb29e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74069634"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Tjänster med hög tillgänglighet som stöds av Azure HDInsight

 För att ge dig optimal tillgänglighet för dina analyskomponenter har HDInsight utvecklats med en unik arkitektur för att säkerställa hög tillgänglighet (HA) för kritiska tjänster. Vissa komponenter i den här arkitekturen har utvecklats av Microsoft för att tillhandahålla automatisk redundans. Andra komponenter är standard Apache-komponenter som distribueras för att stödja specifika tjänster. I den här artikeln beskrivs arkitekturen för HA-tjänstmodellen i HDInsight, hur HDInsight stöder redundans för HA-tjänster och bästa praxis för att återställa från andra avbrott i tjänsten.

## <a name="high-availability-infrastructure"></a>Infrastruktur med hög tillgänglighet

HDInsight tillhandahåller anpassad infrastruktur för att säkerställa att fyra primära tjänster är hög tillgänglighet med automatisk redundansfunktioner:

- Apache Ambari server
- Application Tidslinje Server för Apache YARN
- Jobbhistorik server för Hadoop MapReduce
- Apache Livy

Den här infrastrukturen består av ett antal tjänster och programvarukomponenter, varav vissa är designade av Microsoft. Följande komponenter är unika för HDInsight-plattformen:

- Kontrollant för slavväxling
- Styrenhet för hanterare av redundans
- Slav tjänst med hög tillgänglighet
- Master hög tillgänglighet service

![infrastruktur med hög tillgänglighet](./media/hdinsight-high-availability-components/high-availability-architecture.png)

Det finns också andra tjänster med hög tillgänglighet, som stöds av Apache-tillförlitlighetskomponenter med öppen källkod. Dessa komponenter finns också på HDInsight-kluster:

- Psfs-namn (Hadoop File System)
- GARN ResourceManager
- HBase-hanterare

I följande avsnitt finns mer information om hur dessa tjänster fungerar tillsammans.

## <a name="hdinsight-high-availability-services"></a>HDInsight tjänster med hög tillgänglighet

Microsoft tillhandahåller stöd för de fyra Apache-tjänsterna i följande tabell i HDInsight-kluster. För att skilja dem från tjänster med hög tillgänglighet som stöds av komponenter från Apache kallas de *HDInsight HA-tjänster*.

| Tjänst | Klusternoder | Klustertyper | Syfte |
|---|---|---|---|
| Apache Ambari server| Aktiv headnode | Alla | Övervakar och hanterar klustret.|
| Application Tidslinje Server för Apache YARN | Aktiv headnode | Alla utom Kafka | Underhåller felsökningsinformation om YARN-jobb som körs i klustret.|
| Jobbhistorik server för Hadoop MapReduce | Aktiv headnode | Alla utom Kafka | Underhåller felsökningsdata för MapReduce-jobb.|
| Apache Livy | Aktiv headnode | Spark | Möjliggör enkel interaktion med ett Spark-kluster över ett REST-gränssnitt |

>[!Note]
> ESP-kluster (HDInsight Enterprise Security Package) ger för närvarande endast Ambari-servern hög tillgänglighet.

### <a name="architecture"></a>Arkitektur

Varje HDInsight-kluster har två headnodes i aktiva respektive standby-lägen. HDInsight HA-tjänsterna körs endast på headnodes. Dessa tjänster ska alltid köras på den aktiva huvudnoden och stoppas och placeras i underhållsläge på standby-huvudnoden.

För att upprätthålla rätt tillstånd för HA-tjänster och ge en snabb redundans använder HDInsight Apache ZooKeeper, som är en samordningstjänst för distribuerade applikationer, för att genomföra aktivt headnode val. HDInsight innehåller också några bakgrunds-Java-processer, som samordnar redundansproceduren för HDInsight HA-tjänster. Dessa tjänster är följande: huvudstyrenheten för redundans, kontrollanten för slavundanundanställning, *master-ha-service*och *slav-ha-service*.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper är en högpresterande samordningstjänst för distribuerade program. I produktion körs ZooKeeper vanligtvis i replikerat läge där en replikerad grupp ZooKeeper-servrar utgör ett kvorum. Varje HDInsight-kluster har tre ZooKeeper-noder som gör att tre ZooKeeper-servrar kan skapa kvorum. HDInsight har två ZooKeeper kvorum som löper parallellt med varandra. Ett kvorum avgör den aktiva headnoden i ett kluster där HDInsight HA-tjänster ska köras. Ett annat kvorum används för att samordna HA-tjänster som tillhandahålls av Apache, som beskrivs i senare avsnitt.

### <a name="slave-failover-controller"></a>Kontrollant för slavväxling

Slavundanstyrenheten körs på varje nod i ett HDInsight-kluster. Den här styrenheten ansvarar för att starta Ambari-agenten och *slav-ha-tjänsten* på varje nod. Den frågar regelbundet den första ZooKeeper kvorum om den aktiva headnode. När den aktiva och standby-headnoden ändras utför den slav-redundansstyrenheten följande:

1. Uppdaterar värdkonfigurationsfilen.
1. Startar om Ambari-agenten.

*Slav-ha-tjänsten* ansvarar för att stoppa HDInsight HA-tjänsterna (utom Ambari-servern) i standby-nacknoden.

### <a name="master-failover-controller"></a>Styrenhet för hanterare av redundans

En huvudväxlingskontroller körs på båda headnodes. Båda master failover controllers kommunicera med den första ZooKeeper kvorum att nominera headnode att de körs på som aktiv headnode.

Om till exempel huvudstyrenheten på headnode 0 vinner valet sker följande ändringar:

1. Headnode 0 blir aktiv.
1. Huvudstyrenheten startar Ambari-servern och *master-ha-servicen* på headnode 0.
1. Den andra huvudstyrenheten stoppar Ambari-servern och *master-ha-tjänsten* på headnode 1.

Master-ha-tjänsten körs bara på den aktiva headnode, stoppar det HDInsight HA-tjänsterna (utom Ambari-servern) i vänteläge och startar dem på aktiv headnode.

### <a name="the-failover-process"></a>Redundansprocessen

![redundansprocess](./media/hdinsight-high-availability-components/failover-steps.png)

En hälsoövervakare körs på varje headnode tillsammans med huvudbivikaren för att skicka hearbeat-meddelanden till Zookeeper-kvorumet. Headnode betraktas som en HA-tjänst i det här scenariot. Hälsoövervakaren kontrollerar om varje tjänst med hög tillgänglighet är hälsosam och om den är redo att delta i ledarskapsvalet. Om ja, kommer denna headnode att tävla i valet. Om inte, kommer det att avsluta valet tills det blir klart igen.

Om standby-headnoden någonsin uppnår ledarskap och blir aktiv (till exempel vid ett fel med den tidigare aktiva noden), startar huvudstyrenheten för växlingstjänst alla HDInsight HA-tjänster på den. Huvudstyrenheten stoppar även dessa tjänster på den andra huvudnoden.

För HDInsight HA-tjänstfel, till exempel en tjänst som är nere eller felaktig, bör huvudstyrenheten för redundans automatiskt starta om eller stoppa tjänsterna enligt headnode-statusen. Användare bör inte manuellt starta HDInsight HA-tjänster på båda huvudnoderna. Tillåt i stället automatisk eller manuell redundans för att hjälpa tjänsten att återställas.

### <a name="inadvertent-manual-intervention"></a>Oavsiktligt manuellt ingripande

HDInsight HA-tjänster bör endast köras på den aktiva huvudnoden och startas om automatiskt vid behov. Eftersom enskilda HA-tjänster inte har en egen hälsoövervakare kan redundans inte utlösas på nivån för den enskilda tjänsten. Redundans säkerställs på nodnivå och inte på servicenivå.

### <a name="some-known-issues"></a>Några kända problem

- När du startar en HA-tjänst manuellt i standby-nacknoden kommer den inte att stoppas förrän nästa redundans inträffar. När HA-tjänster körs på båda headnodes, vissa potentiella problem inkluderar: Ambari UI är otillgänglig, Ambari kastar fel, YARN, Spark och Oozie jobb kan fastna.

- När en HA-tjänst på den aktiva headnoden stoppas startas den inte om förrän nästa redundans händer eller huvudstyrenheten/master-ha-tjänsten startas om. När en eller flera HA-tjänster stannar på den aktiva headnoden, särskilt när Ambari-servern stannar, är Ambari UI otillgängligt, andra potentiella problem är yarn-, spark- och Oozie-jobbfel.

## <a name="apache-high-availability-services"></a>Apache tjänster med hög tillgänglighet

Apache ger hög tillgänglighet för HDFS NameNode, YARN ResourceManager och HBase Master, som också finns i HDInsight-kluster. Till skillnad från HDInsight HA-tjänster stöds de i ESP-kluster. Apache HA-tjänster kommunicerar med den andra ZooKeeper-kvorumet (som beskrivs i ovanstående avsnitt) för att välja aktiva/standby-lägen och genomföra automatisk redundans. I följande avsnitt beskrivs hur dessa tjänster fungerar.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Psfs-namn (Distributed File System) (HADoop Distributed File System)

HDInsight-kluster baserade på Apache Hadoop 2.0 eller senare ger NameNode hög tillgänglighet. Det finns två NameNodes som körs på headnodes, som är konfigurerade för automatisk redundans. NameNodes använder *ZKFailoverController* för att kommunicera med Zookeeper för att välja för aktiv/standby-status. *ZKFailoverController* körs på båda headnodes, och fungerar på samma sätt som befälhavaren redundans controller ovan.

Den andra Zookeeper kvorum är oberoende av den första kvorum, så den aktiva NameNode får inte köras på den aktiva headnode. När den aktiva NameNode är död eller ohälsosam vinner standby NameNode valet och blir aktivt.

### <a name="yarn-resourcemanager"></a>GARN ResourceManager

HDInsight-kluster baserade på Apache Hadoop 2.4 eller senare stöder YARN ResourceManager hög tillgänglighet. Det finns två ResourceManagers, rm1 och rm2, som körs på headnode 0 respektive headnode 1. Precis som NameNode är YARN ResourceManager också konfigurerad för automatisk redundans. En annan ResourceManager väljs automatiskt för att vara aktiv när den aktuella aktiva ResourceManager går ner eller svarar inte.

YARN ResourceManager använder sin inbyggda *ActiveStandbyElector* som feldetektor och ledare väljare. Till skillnad från HDFS NameNode behöver YARN ResourceManager inte en separat ZKFC-demon. Den aktiva ResourceManager skriver sina tillstånd i Apache Zookeeper.

Den höga tillgängligheten för YARN ResourceManager är oberoende av NameNode och andra HDInsight HA-tjänster. Den aktiva ResourceManager kanske inte körs på den aktiva headnoden eller headnode där den aktiva NameNode körs. Mer information om YARN ResourceManager hög tillgänglighet finns i [ResourceManager hög tillgänglighet](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html).

### <a name="hbase-master"></a>HBase-hanterare

HDInsight HBase-kluster stöder HBase Master hög tillgänglighet. Till skillnad från andra HA-tjänster, som körs på headnodes, körs HBase Masters på de tre Zookeeper-noderna, där en av dem är den aktiva mästaren och de andra två är i vänteläge. Liksom NameNode, HBase Master koordinater med Apache Zookeeper för ledare val och gör automatisk redundans när den nuvarande aktiva befälhavaren har problem. Det finns bara en aktiv HBase Master när som helst.

## <a name="next-steps"></a>Nästa steg

- [Tillgänglighet och tillförlitlighet för Apache Hadoop-kluster i HDInsight](hdinsight-high-availability-linux.md)
- [Virtuell nätverksarkitektur i Azure HDInsight](hdinsight-virtual-network-architecture.md)
