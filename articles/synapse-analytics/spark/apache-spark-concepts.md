---
title: Apache Spark i Azure Synapse Analytics – Kärnkoncept
description: Den här artikeln innehåller en introduktion till Apache Spark i Azure Synapse Analytics och de olika begreppen.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 3cf654e77bf68c5194a0213d4452242b5c44e234
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423672"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark i Azure Synapse Analytics kärnkoncept

Apache Spark är ett ramverk för parallellbearbetning som stöder minnesintern bearbetning för att öka prestanda i program för stordataanalys. Apache Spark i Azure Synapse Analytics är en av Microsofts implementeringar av Apache Spark i molnet. 

Azure Synapse gör det enkelt att skapa och konfigurera Spark-funktioner i Azure. Azure Synapse tillhandahåller en annan implementering av dessa Spark-funktioner som dokumenteras här.

## <a name="spark-pools-preview"></a>Spark pooler (förhandsvisning)

En Spark-pool (förhandsversion) skapas i Azure-portalen. Det är definitionen av en Spark-pool som, när den instansieras, används för att skapa en Spark-instans som bearbetar data. När en Spark-pool skapas finns den bara som metadata. inga resurser förbrukas, körs eller debiteras för. En Spark-pool har en serie egenskaper som styr egenskaperna för en Spark-förekomst. Dessa egenskaper inkluderar men är inte begränsade till namn, storlek, skalning beteende, tid att leva.

Eftersom det inte finns någon dollar- eller resurskostnad i samband med att skapa Spark-pooler kan valfritt antal skapas med valfritt antal olika konfigurationer. Behörigheter kan också tillämpas på Spark-pooler som tillåter användare att endast ha åtkomst till vissa och inte andra.

En bästa praxis är att skapa mindre Spark-pooler som kan användas för utveckling och felsökning och sedan större för att köra produktionsarbetsbelastningar.

Du kan läsa hur du skapar en Spark-pool och se alla deras egenskaper här [Kom igång med Spark-pooler i Synapse Analytics](apache-spark-notebook-create-spark-use-sql.md#create-an-apache-spark-pool)

## <a name="spark-instances"></a>Spark-instanser

Spark-instanser skapas när du ansluter till en Spark-pool, skapar en session och kör ett jobb. Eftersom flera användare kan ha åtkomst till en enda Spark-pool skapas en ny Spark-instans för varje användare som ansluter. 

När du skickar ett andra jobb, sedan om det finns kapacitet i poolen, den befintliga Spark instansen har också kapacitet då den befintliga instansen kommer att bearbeta jobbet; Om inte och det finns kapacitet på poolnivå skapas en ny Spark-instans.

## <a name="examples"></a>Exempel

### <a name="example-1"></a>Exempel 1

- Du skapar en Spark-pool som heter SP1. Den har en fast klusterstorlek på 20 noder.
- Du skickar ett anteckningsboksjobb, J1 som använder 10 noder, en Spark-instans, SI1 skapas för att bearbeta jobbet.
- Du skickar nu ett annat jobb, J2, som använder 10 noder eftersom det fortfarande finns kapacitet i poolen och instansen, J2, bearbetas av SI1.
- Om J2 hade bett om 11 noder skulle det inte ha funnits någon kapacitet i SP1 eller SI1. I det här fallet, om J2 kommer från en anteckningsbok, kommer jobbet att avvisas. Om J2 kommer från ett batchjobb, kommer det att köas.

### <a name="example-2"></a>Exempel 2

- Du skapar ett Spark-poolanrop SP2. den har en automatisk skalning aktiverad 10 - 20 noder
- Du skickar ett anteckningsboksjobb, J1 som använder 10 noder, en Spark-instans, SI1, skapas för att bearbeta jobbet.
- Du skickar nu ett annat jobb, J2, som använder 10 noder, eftersom det fortfarande finns kapacitet i poolen instansen automatiskt växer till 20 noder och processer J2.

### <a name="example-3"></a>Exempel 3

- Du skapar en Spark-pool som heter SP1. Den har en fast klusterstorlek på 20 noder.
- Du skickar ett anteckningsboksjobb, J1 som använder 10 noder, en Spark-instans, SI1 skapas för att bearbeta jobbet.
- En annan användare, U2, skickar ett jobb, J3, som använder 10 noder, skapas en ny Spark-instans, SI2, för att bearbeta jobbet.
- Du skickar nu ett annat jobb, J2, som använder 10 noder eftersom det fortfarande finns kapacitet i poolen och instansen, J2, bearbetas av SI1.

## <a name="next-steps"></a>Nästa steg

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark-dokumentation](https://spark.apache.org/docs/2.4.4/)
