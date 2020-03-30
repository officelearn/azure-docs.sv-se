---
title: Vanliga frågor om Apache Kafka i Azure HDInsight
description: Få svar på vanliga frågor om Apache Kafka på Azure HDInsight, en hanterad Hadoop-molntjänst.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: ff4079263fd7afb02e132a798997687fad7e9961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206987"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Vanliga frågor och svar om Apache Kafka i Azure HDInsight

Den här artikeln innehåller några vanliga frågor om hur du använder Apache Kafka på Azure HDInsight.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Vilka Kafka-versioner stöds av HDInsight?

Hitta mer information om HDInsight officiellt stöds komponentversioner i [Vad är Apache Hadoop komponenter och versioner tillgängliga med HDInsight?](../hdinsight-component-versioning.md#supported-hdinsight-versions). Vi rekommenderar att du alltid använder den senaste versionen för att säkerställa bästa möjliga prestanda och användarupplevelse.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Vilka resurser finns i ett HDInsight Kafka-kluster och vilka resurser debiteras jag för?

Ett HDInsight Kafka-kluster innehåller följande resurser:

* Huvudnoder
* Zookeeper noder
* Broker -noder (arbetare) 
* Azure Managed Disks kopplade till mäklare noderna
* Gateway-noder

Alla dessa resurser debiteras baserat på vår [HDInsight-prismodell,](https://azure.microsoft.com/pricing/details/hdinsight/)med undantag för gatewaynoder. Du debiteras inte för gateway-noder.

En mer detaljerad beskrivning av olika nodtyper finns i [Azure HDInsight virtuell nätverksarkitektur](../hdinsight-virtual-network-architecture.md). Prissättningen baseras på per minutnodanvändning. Priserna varierar beroende på nodstorlek, antal noder, typ av hanterad disk som används och region.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Fungerar Apache Kafka API:er med HDInsight?

Ja, HDInsight använder inbyggda Kafka-API:er. Klientprogramkoden behöver inte ändras. Se [självstudiekurs: Använd Apache Kafka-producent- och konsument-API:erna](./apache-kafka-producer-consumer-api.md) för att se hur du kan använda Java-baserade api:er för tillverkare/konsumenter med klustret.

## <a name="can-i-change-cluster-configurations"></a>Kan jag ändra klusterkonfigurationer?

Ja, genom Ambari-portalen. Varje komponent i portalen har ett **konfigurationsavsnitt** som kan användas för att ändra komponentkonfigurationer. Vissa ändringar kan kräva att mäklaren startas om.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Vilken typ av autentisering stöder HDInsight för Apache Kafka?

Med [Esp (Enterprise Security Package)](../domain-joined/apache-domain-joined-architecture.md)kan du få ämnesnivåsäkerhet för deras Kafka-kluster. Se [självstudiekurs: Konfigurera Apache Kafka-principer i HDInsight med Enterprise Security Package (Preview)](../domain-joined/apache-domain-joined-run-kafka.md), för mer information.

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Är mina data krypterade? Kan jag använda mina egna nycklar?

Alla Kafka-meddelanden på de hanterade diskarna krypteras med [Azure Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md). Data-in-transit (till exempel data som överförs från klienter till mäklare och tvärtom) krypteras inte som standard. Det är möjligt att kryptera sådan trafik genom [att ställa in SSL på egen hand.](./apache-kafka-ssl-encryption-authentication.md) Dessutom kan HDInsight du hantera sina egna nycklar för att kryptera data i vila. Mer information finns i [Kryptering av kundhanterad nyckeldisk](../disk-encryption.md), för mer information.

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Hur ansluter jag klienter till mitt kluster?

För Kafka kunder att kommunicera med Kafka mäklare, måste de kunna nå mäklare över nätverket. För HDInsight-kluster är det virtuella nätverket (VNet) säkerhetsgränsen. Därför är det enklaste sättet att ansluta klienter till din HDInsight-kluster att skapa klienter inom samma virtuella nätverk som klustret. Andra scenarier är:

* Ansluta klienter i ett annat Azure VNet – Peer klustret VNet och klientenVNet och konfigurera klustret för [IP-annonsering](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising). När du använder IP-annonsering måste Kafka-klienter använda Broker IP-adresser för att ansluta till mäklare, i stället för fullständigt kvalificerade domännamn (FQDNs).

* Ansluta lokala klienter – Använda ett VPN-nätverk och konfigurera anpassade DNS-servrar enligt beskrivningen i [Planera ett virtuellt nätverk för Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

* Skapa en offentlig slutpunkt för kafka-tjänsten – Om företagets säkerhetskrav tillåter det kan du distribuera en offentlig slutpunkt för dina Kafka-mäklare eller en självhanterad REST-slutpunkt med öppen källkod med en offentlig slutpunkt.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Kan jag lägga till mer diskutrymme i ett befintligt kluster?

Om du vill öka mängden tillgängligt utrymme för Kafka-meddelanden kan du öka antalet noder. Det går inte att lägga till fler diskar i ett befintligt kluster.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Kan ett Kafka-kluster fungera med Databricks? 

Ja, Kafka-kluster kan arbeta med Databricks så länge de finns i samma virtuella nätverk. Om du vill använda ett Kafka-kluster med Databricks skapar du ett VNet med ett HDInsight Kafka-kluster i och anger sedan det virtuella nätverket när du skapar din Databricks-arbetsyta och använder VNet-injektion. Mer information finns [i Distribuera Azure Databricks i ditt virtuella Azure-nätverk (VNet Injection).](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject) Du måste ange bootstrap mäklare namnen på Kafka klustret när du skapar Databricks arbetsyta. Information om hur du hämtar kafkamäklarenamnen finns i [Hämta värdinformation för Apache Zookeeper och Broker](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started#getkafkainfo).

## <a name="how-can-i-have-maximum-data-durability"></a>Hur kan jag ha maximal datahållbarhet?

Med datahållbarhet kan du uppnå den lägsta risken för meddelandeförlust. För att uppnå maximal datahållbarhet rekommenderar vi följande inställningar:

* använda en minsta replikeringsfaktor på 3 i de flesta regioner
* använda en minsta replikeringsfaktor på 4 i regioner med endast två feldomäner
* inaktivera orena ledarval
* ange **min.insync.replicas** till 2 eller mer - detta ändrar antalet repliker som måste vara helt synkroniserade med ledaren innan en skrivning kan fortsätta
* ställa in **egenskapen acks** till **alla** - den här egenskapen kräver att alla repliker ska bekräfta alla meddelanden

Konfigurera Kafka för högre datakonsekvens påverkar tillgängligheten för mäklare för att producera begäranden.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Kan jag replikera mina data till flera kluster?

Ja, data kan replikeras till flera kluster med Kafka MirrorMaker. Se information om hur du ställer in MirrorMaker finns i [Mirror Apache Kafka ämnen](apache-kafka-mirroring.md). Dessutom finns det andra självhanterade tekniker och leverantörer med öppen källkod som kan bidra till att uppnå replikering till flera kluster, till exempel [Brooklin](https://github.com/linkedin/Brooklin/).

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Kan jag uppgradera mitt kluster? Hur ska jag uppgradera mitt kluster?

Vi stöder för närvarande inte uppgraderingar av klusterversion på plats. Om du vill uppdatera klustret till en högre Kafka-version skapar du ett nytt kluster med den version du vill ha och migrerar Kafka-klienterna för att använda det nya klustret.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Hur övervakar jag mitt Kafka-kluster?

Använd Azure-övervakaren för att analysera dina [Kafka-loggar](./apache-kafka-log-analytics-operations-management.md).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera SSL-kryptering (Secure Sockets Layer) och autentisering för Apache Kafka i Azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [Använd MirrorMaker för att replikera Apache Kafka-ämnen med Kafka i HDInsight](./apache-kafka-mirroring.md)
