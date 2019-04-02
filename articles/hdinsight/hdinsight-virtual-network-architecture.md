---
title: Azure HDInsight virtuella nätverksarkitekturen
description: Läs om de tillgängliga resurserna när du skapar ett HDInsight-kluster i Azure Virtual Network.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 6d92273298c0448d7377acab6f3b8ea1cc1ed908
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762930"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight virtuella nätverksarkitekturen

Den här artikeln beskriver de resurser som finns när du distribuerar ett HDInsight-kluster i en anpassad Azure-nätverk. Den här informationen hjälper dig att ansluta lokala resurser till ditt HDInsight-kluster i Azure. Mer information om virtuella Azure-nätverk finns i [vad är Azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Resurstyper i Azure HDInsight-kluster

Azure HDInsight-kluster har olika typer av virtuella datorer eller noder. Varje nodtyp spelar en roll i användningen av systemet. I följande tabell sammanfattas dessa nodtyper och deras roller i klustret.

| Type | Beskrivning |
| --- | --- |
| Huvudnod |  För samtliga klustertyper utom Apache Storm värd huvudnoderna de processer som hanterar körning av det distribuerade programmet. Huvudnoden är också den nod som du kan SSH och kör program som koordineras sedan för att köra över klusterresurserna. Antal huvudnoder vara högst två för alla typer av klustret. |
| ZooKeeper-nod | Zookeeper samordnar aktiviteter mellan noderna som gör databearbetning. Den också gör val av ledare för huvudnoden och håller reda på vilka huvudnoden körs en specifik huvudtjänsten. Antalet ZooKeeper-noder är fast på två. |
| Arbetsnod | Representerar de noder som stöd för bearbetning av funktioner. Arbetsnoder kan läggs till eller tas bort från klustret för att skala databehandling kapaciteten och hantera kostnader. |
| R Server-kantnoden | R Server-gränsnoden representerar den nod som du kan SSH och kör program som koordineras sedan för att köra över klusterresurserna. En kantnod delta inte i dataanalys i klustret. Den här noden är också värd R Studio Server, så att du kan köra R-program med hjälp av en webbläsare. |
| Region-nod | Region Server körs i noden region (kallas även en datanod) för typen HBase-kluster. Regionservrar fungerar och hantera en del av de data som hanteras av HBase. Region-noder kan läggs till eller tas bort från klustret för att skala databehandling kapaciteten och hantera kostnader.|
| Nimbus-nod | För typ av Storm-kluster kan tillhandahåller Nimbus-noden funktioner som liknar huvudnoden. Nimbus-noden tilldelar uppgifter till andra noder i ett kluster genom Zookeeper som samordnar körning av Storm-topologier. |
| Överordnade noden | För typ av Storm-kluster kan kör den överordnade noden instruktionerna från Nimbus-noden till den önskade bearbetning. |

## <a name="basic-virtual-network-resources"></a>Grundläggande virtuella nätverksresurser

Följande diagram visar placeringen av HDInsight-noder och nätverksresurser i Azure.

![Diagram över HDInsight-entiteter som skapats i Azure anpassade virtuella nätverk](./media/hdinsight-virtual-network-architecture/vnet-diagram.png)

Standardresurser som finns när HDInsight distribueras till ett virtuellt nätverk för Azure omfattar klustret nodtyper som nämns i föregående tabell, samt nätverksenheter som stöder kommunikation mellan det virtuella nätverket och utanför nätverk.

I följande tabell sammanfattas de nio klusternoder som skapas när HDInsight distribueras i en anpassad Azure-nätverk.

| Resurstyp | Antal finns | Information |
| --- | --- | --- |
|Huvudnod | två |    |
|Zookeeper-nod | tre | |
|Arbetsnod | två | Det här antalet kan variera beroende på klusterkonfigurationen och skalning. Minst tre arbetsnoder krävs för Apache Kafka.  |
|Gateway-noden | två | Gateway-noder är Azure-datorer som skapas på Azure, men visas inte i din prenumeration. Kontakta supporten om du måste starta om dessa noder. |

I följande nätverksresurser finns skapas automatiskt i det virtuella nätverket som används med HDInsight:

| Nätverk-resurs | Antal finns | Information |
| --- | --- | --- |
|Lastbalanserare | tre | |
|Nätverksgränssnitt | nio | Det här värdet är baserad på en normal kluster, där varje nod har sin egen nätverksgränssnitt. De nio gränssnitt är för två huvudnoder, tre zookeeper-noder, två arbetarnoder och två gateway-noder som nämns i föregående tabell. |
|Offentliga IP-adresser | två |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Slutpunkter för att ansluta till HDInsight

Du kan komma åt ditt HDInsight-kluster på tre sätt:

- En HTTPS-slutpunkt utanför det virtuella nätverket på `CLUSTERNAME.azurehdinsight.net`.
- En SSH-slutpunkt för att ansluta direkt till huvudnoden på `CLUSTERNAME-ssh.azurehdinsight.net`.
- En HTTPS-slutpunkt i det virtuella nätverket `CLUSTERNAME-int.azurehdinsight.net`. Observera den ”-int” i den här URL: en. Den här slutpunkten ska matcha till en privat IP-adress i det virtuella nätverket och är inte tillgänglig från det offentliga internet.

Dessa tre slutpunkter har tilldelats en belastningsutjämnare.

Offentliga IP-adresser tillhandahålls också att de två slutpunkter som tillåter anslutning från utanför det virtuella nätverket.

1. En offentlig IP-adress tilldelas till belastningsutjämnaren för det fullständigt kvalificerade domännamnet (FQDN) ska användas vid anslutning till klustret från internet `CLUSTERNAME.azurehdinsight.net`.
1. Andra offentliga IP-adressen används för SSH endast domännamnet `CLUSTERNAME-ssh.azurehdinsight.net`.

## <a name="next-steps"></a>Nästa steg

* [Skydda inkommande trafik till HDInsight-kluster i ett virtuellt nätverk med privat slutpunkt](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
