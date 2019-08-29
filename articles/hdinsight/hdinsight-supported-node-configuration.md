---
title: Nodkonfigurationer som stöds av Azure HDInsight
description: Läs om minsta och rekommenderade konfigurationer för HDInsight-klusternoder.
keywords: VM-storlekar, kluster storlekar, kluster konfiguration
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e482cf9b5367beba00784e69c5bad88142df5225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076217"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Vilka är standard och rekommenderade nodkonfigurationer för Azure HDInsight?

I den här artikeln beskrivs standard-och rekommenderade nodkonfigurationer för Azure HDInsight-kluster.

## <a name="default-and-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Standard-och rekommenderad konfiguration av noder och virtuella dator storlekar för kluster

I följande tabell listas standard-och rekommenderade storlekar för virtuella datorer för HDInsight-kluster.  Den här informationen är nödvändig för att förstå de VM-storlekar som ska användas när du skapar PowerShell-eller Azure CLI-skript för att distribuera HDInsight-kluster. 

Om du behöver fler än 32 arbetsnoder i ett kluster väljer du en head-nods storlek med minst 8 kärnor och 14 GB RAM. 

De enda kluster typerna som innehåller data diskar är Kafka-och HBase-kluster med funktionen för accelererade skrivningar aktiverade. HDInsight stöder P30-och S30 disk storlekar i dessa scenarier.

Mer information om specifikationen för varje VM-typ finns i följande dokument:

* [Generella storlekar för virtuella datorer: Dv2-serien 1-5](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dv2-series)
* [Minnesoptimerade storlekar för virtuella datorer: Dv2-serien 11-15](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#dv2-series-11-15)
* [Generella storlekar för virtuella datorer: AV2-serien 1-8](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#av2-series)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Alla regioner som stöds förutom Brasilien, södra och Japan, väst

> [!Note]
> Du kan hämta SKU-identifieraren för användning i PowerShell och andra skript `Standard_` genom att lägga till i början av alla VM-SKU: er i tabellerna nedan. Till exempel `D12_v2` skulle bli `Standard_D12_v2`.

| Klustertyp | Hadoop | HBase | Interaktiv fråga | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Huvud: storlek på virtuell dator | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 | D3_v2 |
| Head: rekommenderade storlekar för virtuella datorer | D13_v2,<br/>D14_v2,<br/>D5_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 | D13_v2,<br/>D14_v2 | A4_v2,<br/>A8_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 |
| Anställd: standard storlek för virtuell dator | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 med 2 S30-diskar per Broker |
| Arbetare: Rekommenderad VM-storlek | D5_v2,<br>D12_v2,<br/>D13_v2 | D3_v2,<br/>D4_v2,<br/>D13_v2 | D13_v2,<br/>D14_v2 | D3_v2<br/>D4_v2,<br/>D12_v2 | D12_v2,<br>D13_v2,<br>D14_v2 | D4_v2,<br/>D12_v2,<br>D13_v2,<br>D14_v2 | D3_v2,<br/>D4_v2,<br/>DS3_v2,<br/>DS4_v2 |
| ZooKeeper: standard storlek för virtuell dator |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: rekommenderade storlekar för virtuella datorer |  | A4_v2, <br/>A8_v2, <br/>A2m_v2 | A4_v2,<br/>A8_v2,<br/>A2m_v2 | A4_v2,<br/>A2_v2,<br/>A8_v2 |  | A2_v2 | A4_v2,<br/> A8_v2,<br/>A2m_v2 |
| ML-tjänster: standard storlek för virtuell dator |  |  |  |  |  | D4_v2 |  |
| ML-tjänster: Rekommenderad VM-storlek |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/>D14_v2 |  |

### <a name="brazil-south-and-japan-west-only"></a>Endast Brasilien, södra och Japan, väst

| Klustertyp | Hadoop | HBase | Interaktiv fråga | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Huvud: storlek på virtuell dator | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Head: rekommenderade storlekar för virtuella datorer | D5_v2,<br/> D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D13_v2,<br/> D14_v2 | A4_v2,<br/> A8_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 |
| Anställd: standard storlek för virtuell dator | D4 | D4 | D14 | D3 | D13 | D4 |
| Arbetare: Rekommenderad VM-storlek | D5_v2,<br/> D12_v2,<br/> D13_v2 | D3_v2,<br/> D4_v2,<br/> D13_v2 | D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |
| ZooKeeper: standard storlek för virtuell dator |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: rekommenderade storlekar för virtuella datorer |  | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2 |  | A2_v2 |
| ML-tjänster: standard storlekar för virtuella datorer |  |  |  |  |  | D4 |
| ML-tjänster: rekommenderade storlekar för virtuella datorer |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |

> [!NOTE]
> - Head är känt som *Nimbus* för Storm-kluster typen.
> - Arbetaren är känd som *administratör* för Storm-klustret.
> - Worker är känt som *region* för kluster typen HBase.

## <a name="next-steps"></a>Nästa steg

* [Vad är Apache Hadoop-komponenter och versioner som är tillgängliga med HDInsight?](hdinsight-component-versioning.md)
