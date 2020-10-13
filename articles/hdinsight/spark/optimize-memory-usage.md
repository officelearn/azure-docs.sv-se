---
title: Optimera minnes användning i Apache Spark – Azure HDInsight
description: Lär dig hur du optimerar minnes användningen i Apache Spark på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperfq1
ms.openlocfilehash: 056060f8b94747651c78c757150d5e5a5982c7af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88757786"
---
# <a name="memory-usage-optimization-for-apache-spark"></a>Optimering av minnes användning för Apache Spark

Den här artikeln beskriver hur du optimerar minnes hanteringen för ditt Apache Spark-kluster för bästa prestanda på Azure HDInsight.

## <a name="overview"></a>Översikt

Spark arbetar genom att placera data i minnet. Att hantera minnes resurser är en viktig aspekt i att optimera körningen av Spark-jobb.  Det finns flera tekniker som du kan använda för att använda klustrets minne på ett effektivt sätt.

* Föredra mindre datapartitioner och konto för data storlek, typer och distribution i din partitionerings strategi.
* Överväg att använda nyare, mer effektiv [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo) , snarare än standard Java-serialisering.
* Föredra att använda garn, eftersom det separeras `spark-submit` av batch.
* Övervaka och justera konfigurations inställningar för Spark.

För din referens visas Spark-minneskortet och vissa viktiga utförar-minnes parametrar i nästa bild.

## <a name="spark-memory-considerations"></a>Överväganden vid Spark-minne

Om du använder Apache Hadoop garn kontrollerar garn det minne som används av alla behållare på varje spark-nod.  Följande diagram visar de viktigaste objekten och deras relationer.

![GARN Spark minnes hantering](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Prova följande om du vill ta bort meddelanden om slut på minne:

* Läs om DAG hantering, blandade. Minska genom att dra av och på mappnings sidan, för bucketiseras-källdata, maximera enskilda blandade kanaler och minska mängden data som skickas.
* Föredra `ReduceByKey` med den fasta minnes gränsen till `GroupByKey` , som tillhandahåller agg regeringar, fönster och andra funktioner, men har den obegränsade minnes gränsen för Ann.
* Föredra `TreeReduce` , som fungerar mer i körnings-eller partitionerna, till `Reduce` , som gör allt arbete på driv rutinen.
* Använd DataFrames i stället för RDD-objekt på lägre nivå.
* Skapa ComplexTypes som kapslar in åtgärder, till exempel "Top N", olika agg regeringar eller fönster åtgärder.

Ytterligare fel söknings steg finns i [OutOfMemoryError-undantag för Apache Spark i Azure HDInsight](apache-spark-troubleshoot-outofmemory.md).

## <a name="next-steps"></a>Nästa steg

* [Optimera data bearbetning för Apache Spark](optimize-cluster-configuration.md)
* [Optimera data lagring för Apache Spark](optimize-data-storage.md)
* [Optimera kluster konfigurationen för Apache Spark](optimize-cluster-configuration.md)
