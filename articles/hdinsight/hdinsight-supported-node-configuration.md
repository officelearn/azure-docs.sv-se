---
title: Azure HDInsight stöds nod konfigurationer
description: Lär dig de minsta och rekommenderade konfigurationerna för HDInsight-klusternoder.
keywords: vm-storlekar, klusterstorlekar, klusterkonfiguration
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e75146266568001d8fee7be26898ac8bdfffb7fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484794"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Vilka är standard- och rekommenderade nodkonfigurationer för Azure HDInsight?

I den här artikeln beskrivs standardkonfigurationer och rekommenderade nodkonfigurationer för Azure HDInsight-kluster.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Standard- och minsta rekommenderade nodkonfiguration och storlekar för virtuella datorer för kluster

Följande tabeller listar standardstorlekar för virtuella datorer (VM) för HDInsight-kluster.  Den här informationen är nödvändig för att förstå den virtuella datorns storlekar som ska användas när du skapar PowerShell- eller Azure CLI-skript för att distribuera HDInsight-kluster.

Om du behöver fler än 32 arbetsnoder i ett kluster väljer du en huvudnodstorlek med minst 8 kärnor och 14 GB RAM.If you need more than 32 worker nodes in a cluster, select a head node size with least 8 cores and 14 GB of RAM. 

De enda klustertyper som har datadiskar är Kafka- och HBase-kluster med funktionen Accelererade skrivningar aktiverad. HDInsight stöder P30- och S30-diskstorlekar i dessa scenarier.

Specifikationerna för alla minsta rekommenderade VM-typer som används i det här dokumentet sammanfattas i följande tabell.

| Storlek              | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar/dataflöde: IOPS | Max nätverkskort / Förväntad nätverksbandbredd (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12 000 / 187 / 93                                           | 16 / 16x500           | 4/3 000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24 000 / 375 / 187                                          | 32 / 32x500           | 8/6 000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48 000 / 750 / 375                                          | 64 / 64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12 000 / 187 / 93                                         | 16 / 16 x 500                         | 4/3 000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24 000 / 375 / 187                                        | 32 / 32 x 500                       | 8/6 000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48 000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1 000 / 20 / 10                                           | 2 / 2 x 500               | 2/250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2 000 / 40 / 20                                           | 4 / 4 x 500               | 2/500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4 000 / 80 / 40                                           | 8 / 8 x 500               | 4/1 000                     |

Mer information om specifikationerna för varje vm-typ finns i följande dokument:

* [Virtuella datorstorlekar för allmänna ändamål: Dv2-serien 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Minnesoptimerade storlekar för virtuella datorer: Dv2-serien 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Virtuella maskinstorlekar för allmänna ändamål: Av2-serien 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Alla regioner som stöds utom Brasilien söder och Japan väst

> [!Note]
> Om du vill hämta SKU-identifieraren för användning `Standard_` i powershell och andra skript lägger du till i början av alla virtuella SKU:er i tabellerna nedan. Till exempel `D12_v2` skulle `Standard_D12_v2`bli .

| Klustertyp | Hadoop | HBase | Interaktiv fråga | Storm | Spark | ML-server | Kafka |
|---|---|---|---|---|---|---|---|
| Huvud: standardstorlek för virtuella datorer | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2. <br/>D13_v2* | D12_v2 | D3_v2 |
| Huvud: minsta rekommenderade VM-storlekar | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2. <br/>D13_v2* | D12_v2 | D3_v2 |
| Arbetare: standardstorlek för virtuella datorer | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 med 2 S30 diskar per mäklare |
| Arbetare: minsta rekommenderade VM-storlekar | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: standardstorlek för virtuella datorer |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: minsta rekommenderade VM-storlekar |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| ML-tjänster: standardstorlek för virtuella datorer |  |  |  |  |  | D4_v2 |  |
| ML-tjänster: minsta rekommenderade VM-storlek |  |  |  |  |  | D4_v2 |  |

\*= VM-storlekar för ESP-kluster (Spark Enterprise Security Package)

### <a name="brazil-south-and-japan-west-only"></a>Endast brasilien söder och Japan väst

| Klustertyp | Hadoop | HBase | Interaktiv fråga | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Huvud: standardstorlek för virtuella datorer | D12 (på andra) | D12 (på andra) | D13 (på andra) | A4_v2 | D12 (på andra) | D12 (på andra) |
| Huvud: minsta rekommenderade VM-storlekar | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Arbetare: standardstorlek för virtuella datorer | D4 (på andra) | D4 (på andra) | D14 (på andra) | D3 | D13 (på andra) | D4 (på andra) |
| Arbetare: minsta rekommenderade VM-storlekar | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: standardstorlek för virtuella datorer |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: minsta rekommenderade VM-storlekar |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ML-tjänster: standardstorlekar för virtuella datorer |  |  |  |  |  | D4 (på andra) |
| ML-tjänster: minsta rekommenderade VM-storlekar |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - Head kallas *Nimbus* för stjärnhoptypen Storm.
> - Arbetare kallas *övervakare* för klustertypen Storm.
> - Arbetaren kallas *Region* för HBase-klustertypen.

## <a name="next-steps"></a>Nästa steg

* [Vilka är Apache Hadoop-komponenterna och versionerna med HDInsight?](hdinsight-component-versioning.md)
