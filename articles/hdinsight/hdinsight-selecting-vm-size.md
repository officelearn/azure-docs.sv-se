---
title: Så här väljer du rätt vm-storlek för ditt Azure HDInsight-kluster
description: Lär dig hur du väljer rätt VM-storlek för ditt HDInsight-kluster.
keywords: vm-storlekar, klusterstorlekar, klusterkonfiguration
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: a21e8d6c76c93b3084619c09f6a7664a25c1929c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73682209"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>Välja rätt vm-storlek för ditt Azure HDInsight-kluster

I den här artikeln beskrivs hur du väljer rätt vm-storlek för de olika noderna i HDInsight-klustret. 

Börja med att förstå hur egenskaperna för en virtuell dator, till exempel CPU-bearbetning, RAM-storlek och nätverksfördröjning, påverkar bearbetningen av dina arbetsbelastningar. Tänk sedan på ditt program och hur det matchar med vad olika VM-familjer är optimerade för. Kontrollera att den virtuella datorn-familjen som du vill använda är kompatibel med den klustertyp som du planerar att distribuera. En lista över alla vm-storlekar som stöds och rekommenderas för varje klustertyp finns i [Nodkonfigurationer som stöds av Azure HDInsight](hdinsight-supported-node-configuration.md). Slutligen kan du använda en benchmarkingprocess för att testa några exempelarbetsbelastningar och kontrollera vilken SKU inom den familjen som är rätt för dig.

Mer information om hur du planerar andra aspekter av klustret, till exempel välja en lagringstyp eller klusterstorlek, finns i [Kapacitetsplanering för HDInsight-kluster](hdinsight-capacity-planning.md).

## <a name="vm-properties-and-big-data-workloads"></a>VM-egenskaper och stordataarbetsbelastningar

Vm-storlek och typ bestäms av processorhantering, RAM-storlek och nätverksfördröjning:

- CPU: Vm-storleken avgör antalet kärnor. Ju fler kärnor, desto högre grad av parallell beräkning varje nod kan uppnå. Dessutom har vissa vm-typer snabbare kärnor.

- RAM: Vm-storleken dikterar också mängden RAM-minne som är tillgängligt i den virtuella datorn. För arbetsbelastningar som lagrar data i minnet för bearbetning, i stället för att läsa från disk, se till att dina arbetsnoder har tillräckligt med minne för att passa data.

- Nätverk: För de flesta klustertyper finns inte de data som bearbetas av klustret på lokal disk, utan i en extern lagringstjänst som DataSjölagring eller Azure Storage. Tänk på nätverkets bandbredd och dataflöde mellan noden VM och lagringstjänsten. Nätverksbandbredden som är tillgänglig för en virtuell dator ökar vanligtvis med större storlekar. Mer information finns i [översikt över vm-storlekar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="understanding-vm-optimization"></a>Förstå VM-optimering

Virtuella datorfamiljer i Azure är optimerade för att passa olika användningsfall. I tabellen nedan hittar du några av de mest populära användningsfallen och de virtuella familjerna som matchar dem.

| Typ                     | Storlekar           |    Beskrivning       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Nybörjar](../virtual-machines/linux/sizes-general.md)          | A, Av2  | Har CPU-prestanda och minneskonfigurationer bäst lämpade för arbetsbelastningar på ingångsnivå som utveckling och test. De är ekonomiska och ger ett billigt alternativ för att komma igång med Azure. |
| [Generellt syfte](../virtual-machines/linux/sizes-general.md)          | D, DSv2, Dv2  | Balanserat förhållande mellan CPU och minne. Utmärkt för testning och utveckling, små till medelstora databaser och webbservrar med låg till medelhög trafik. |
| [Beräkningsoptimerad](../virtual-machines/linux/sizes-compute.md)        | F           | Högt förhållande mellan CPU och minne. Bra för mellantrafik webbservrar, nätverksenheter, batchprocesser och programservrar.        |
| [Minnesoptimerad](../virtual-machines/linux/sizes-memory.md)         | Esv3, Ev3  | Högt förhållande mellan minne och CPU. Utmärkt för relationsdatabasservrar, mellanstora till stora cacheminnen och minnesinterna analyser.                 |

- Information om prissättning av tillgängliga VM-instanser i regioner som stöds av HDInsight finns i [HDInsight Pricing](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="cost-saving-vm-types-for-light-workloads"></a>Kostnadsbesparande VM-typer för lätta arbetsbelastningar

Om du har krav på ljusbearbetning kan [F-serien](https://azure.microsoft.com/blog/f-series-vm-size/) vara ett bra val för att komma igång med HDInsight. F-serien har ett lägre listpris per timme och har bästa prisprestanda i Azure-portföljen baserat på Azure-beräkningsenhet (ACU, Azure Compute Unit) per virtuell processor.

I följande tabell beskrivs klustertyper och nodtyper, som kan skapas med virtuella datorer i Fsv2-serien.

| Typ av kluster | Version | Arbetsnod | Huvudnod | Zookeeper Nod |
|---|---|---|---|---|
| Spark | Alla | F4 och högre | nej | nej |
| Hadoop | Alla | F4 och högre | nej | nej |
| Kafka | Alla | F4 och högre | nej | nej |
| HBase | Alla | F4 och högre | nej | nej |
| LLAP (PÅ ANDRA) | Inaktiverad | nej | nej | nej |
| Storm | Inaktiverad | nej | nej | nej |
| ML-tjänst | ENDAST HDI 3.6 | F4 och högre | nej | nej |

Information om hur du ser specifikationerna för varje SKU i F-serien finns i [VM-storlekar i F-serien](https://azure.microsoft.com/blog/f-series-vm-size/).

## <a name="benchmarking"></a>Benchmarking

Benchmarking är processen att köra simulerade arbetsbelastningar på olika virtuella datorer för att mäta hur väl de kommer att prestera för dina produktionsarbetsbelastningar. 

Mer information om benchmarking för SKU:er för virtuella datorer och klusterstorlekar finns [i Planering av klusterkapacitet i Azure HDInsight ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type).

## <a name="next-steps"></a>Nästa steg

- [Azure HDInsight stöds nod konfigurationer](hdinsight-supported-node-configuration.md)
- [Storlekar för virtuella Linux-datorer i Azure](../virtual-machines/linux/sizes.md)
