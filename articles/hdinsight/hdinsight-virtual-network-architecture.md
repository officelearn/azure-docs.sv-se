---
title: Virtuell nätverksarkitektur i Azure HDInsight
description: Lär dig vilka resurser som är tillgängliga när du skapar ett HDInsight-kluster i ett Virtuellt Azure-nätverk.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: ad0e0250b32f2bdef4944e6e148be3215f3822f7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390206"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Virtuell nätverksarkitektur i Azure HDInsight

I den här artikeln beskrivs de resurser som finns när du distribuerar ett HDInsight-kluster till ett anpassat Azure Virtual Network. Den här informationen hjälper dig att ansluta lokala resurser till ditt HDInsight-kluster i Azure. Mer information om Virtuella Azure-nätverk finns i [Vad är Virtuellt Azure-nätverk?](../virtual-network/virtual-networks-overview.md)

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Resurstyper i Azure HDInsight-kluster

Azure HDInsight-kluster har olika typer av virtuella datorer eller noder. Varje nodtyp spelar en roll i systemets funktion. I följande tabell sammanfattas dessa nodtyper och deras roller i klustret.

| Typ | Beskrivning |
| --- | --- |
| Huvudnod |  För alla klustertyper utom Apache Storm är huvudnoderna värd för de processer som hanterar körningen av det distribuerade programmet. Huvudnoden är också den nod som du kan SSH i och köra program som sedan samordnas för att köras över klusterresurserna. Antalet huvudnoder är fast vid två för alla klustertyper. |
| ZooKeeper-nod | Zookeeper samordnar uppgifter mellan noderna som utför databearbetning. Det gör också ledare val av huvudet noden, och håller reda på vilken huvudnod kör en viss huvudtjänst. Antalet ZooKeeper-noder är fast till tre. |
| Arbetsnod | Representerar de noder som stöder databehandlingsfunktioner. Arbetsnoder kan läggas till eller tas bort från klustret för att skala datorfunktioner och hantera kostnader. |
| R Server kantnod | R Server-kanten-noden representerar den nod som du kan SSH i och köra program som sedan samordnas för att köras över klusterresurserna. En kantnod deltar inte i dataanalys i klustret. Den här noden är också värd för R Studio Server, så att du kan köra R-program med hjälp av en webbläsare. |
| Regionnod | För HBase-klustertypen kör regionnoden (kallas även en datanod) regionservern. Regionservrar betjänar och hanterar en del av de data som hanteras av HBase. Regionnoder kan läggas till eller tas bort från klustret för att skala datorfunktioner och hantera kostnader.|
| Nimbus-nod | För klustertypen Storm tillhandahåller Noden Nimbus funktioner som liknar noden Huvud. Noden Nimbus tilldelar uppgifter till andra noder i ett kluster genom Zookeeper, som koordinerar körningen av Stormtopologier. |
| Handledare nod | För klustertypen Storm kör övervakarnoden instruktionerna från noden Nimbus för att bearbeta. |

## <a name="resource-naming-conventions"></a>Konventioner för namngivning av resurser

Använd fullständigt kvalificerade domännamn (FQDN) när du adresserar noder i klustret. Du kan hämta FQDN för olika nodtyper i klustret med [Ambari API](hdinsight-hadoop-manage-ambari-rest-api.md).

Dessa FQDN kommer att `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`vara av formuläret .

Den `<node-type-prefix>` kommer att *hn* för headnodes, *wn* för arbetstagare noder och *zn* för zookeeper noder.

Om du bara behöver värdnamnet använder du bara den första delen av FQDN:`<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Grundläggande virtuella nätverksresurser

Följande diagram visar placeringen av HDInsight-noder och nätverksresurser i Azure.

![Diagram över HDInsight-entiteter som skapats i Azure custom VNET](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Standardresurserna i ett Virtuellt Azure-nätverk innehåller de klusternodtyper som nämns i föregående tabell. Och nätverksenheter som stöder kommunikation mellan det virtuella nätverket och externa nätverk.

I följande tabell sammanfattas de nio klusternoder som skapas när HDInsight distribueras till ett anpassat Azure Virtual Network.

| Resurstyp | Antal närvarande | Information |
| --- | --- | --- |
|Huvudnod | två |    |
|Zookeeper-nod | tre | |
|Arbetsnod | två | Det här antalet kan variera beroende på klusterkonfiguration och skalning. Minst tre arbetsnoder behövs för Apache Kafka.  |
|Gateway-nod | två | Gateway-noder är virtuella Azure-datorer som skapas på Azure, men som inte visas i din prenumeration. Kontakta supporten om du behöver starta om dessa noder. |

Följande nätverksresurser skapas automatiskt i det virtuella nätverket som används med HDInsight:

| Nätverksresurs | Antal närvarande | Information |
| --- | --- | --- |
|Lastbalanserare | tre | |
|Nätverksgränssnitt | Nio | Det här värdet baseras på ett normalt kluster, där varje nod har ett eget nätverksgränssnitt. De nio gränssnitten är för: två huvudnoder, tre zookeepernoder, två arbetsnoder och två gatewaynoder som nämns i föregående tabell. |
|Offentliga IP-adresser | två |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Slutpunkter för anslutning till HDInsight

Du kan komma åt ditt HDInsight-kluster på tre sätt:

- En HTTPS-slutpunkt utanför det `CLUSTERNAME.azurehdinsight.net`virtuella nätverket på .
- En SSH-slutpunkt för direkt anslutning till `CLUSTERNAME-ssh.azurehdinsight.net`headnoden på .
- En HTTPS-slutpunkt i `CLUSTERNAME-int.azurehdinsight.net`det virtuella nätverket . Lägg märke`-int`till " " i den här webbadressen. Den här slutpunkten kommer att matcha till en privat IP i det virtuella nätverket och är inte tillgänglig från det offentliga internet.

Dessa tre slutpunkter tilldelas var och en en belastningsutjämnare.

Offentliga IP-adresser tillhandahålls också till de två slutpunkter som tillåter anslutning från utanför det virtuella nätverket.

1. En offentlig IP tilldelas belastningsutjämnaren för det fullständigt kvalificerade domännamnet (FQDN) `CLUSTERNAME.azurehdinsight.net`som ska användas vid anslutning till klustret från internet .
1. Den andra offentliga IP-adressen används endast `CLUSTERNAME-ssh.azurehdinsight.net`för domännamnet SSH .

## <a name="next-steps"></a>Nästa steg

- [Säker inkommande trafik till HDInsight-kluster i ett virtuellt nätverk med privat slutpunkt](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
