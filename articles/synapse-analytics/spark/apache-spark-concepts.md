---
title: Apache Spark i Azure Synapse Analytics-core Concepts
description: Den här artikeln innehåller en introduktion till Apache Spark i Azure Synapse Analytics och de olika begreppen.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: aa1b4287899aaff9ede4832e999603773d79d28c
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194135"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark i Azure Synapse Analytics core-koncept

Apache Spark är ett ramverk för parallellbearbetning som stöder minnesintern bearbetning för att öka prestanda i program för stordataanalys. Apache Spark i Azure Synapse Analytics är en av Microsofts implementeringar av Apache Spark i molnet. 

Med Azure Synapse kan du enkelt skapa och konfigurera Spark-funktioner i Azure. Azure Synapse tillhandahåller en annan implementering av dessa Spark-funktioner som dokumenteras här.

## <a name="spark-pools-preview"></a>Spark-pooler (för hands version)

En spark-pool (för hands version) skapas i Azure Portal. Det är definitionen av en spark-pool som, när den instansieras, används för att skapa en spark-instans som bearbetar data. När en spark-pool skapas, finns den bara som metadata. inga resurser förbrukas, körs eller debiteras för. En spark-pool har en serie egenskaper som styr egenskaperna för en spark-instans. dessa egenskaper inkluderar, men är inte begränsade till namn, storlek, skalnings beteende, Time to Live.

Eftersom det inte finns några dollar-eller resurs kostnader kopplade till att skapa Spark-pooler, kan alla siffror skapas med valfritt antal olika konfigurationer. Behörigheter kan också tillämpas på Spark-pooler som gör det möjligt för användare endast att ha åtkomst till vissa och inte andra.

Ett bra tips är att skapa mindre Spark-pooler som kan användas för utveckling och fel sökning och sedan större för att köra produktions arbets belastningar.

Du kan läsa hur du skapar en spark-pool och ser alla deras egenskaper här [komma igång med Spark-pooler i Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md)

## <a name="spark-instances"></a>Spark-instanser

Spark-instanser skapas när du ansluter till en spark-pool, skapar en session och kör ett jobb. Eftersom flera användare kan ha åtkomst till en enda Spark-pool skapas en ny Spark-instans för varje användare som ansluter. 

När du skickar ett andra jobb, och om det finns kapacitet i poolen, har den befintliga Spark-instansen också kapacitet och den befintliga instansen bearbetar jobbet. om inte och det finns kapacitet på Poolnivå skapas en ny Spark-instans.

## <a name="examples"></a>Exempel

### <a name="example-1"></a>Exempel 1

- Du skapar en spark-pool med namnet SP1; den har en fast kluster storlek på 20 noder.
- Du skickar ett anteckningsbok-jobb, J1 som använder 10 noder, en spark-instans, SI1 skapas för att bearbeta jobbet.
- Nu skickar du ett annat jobb, J2, som använder 10 noder eftersom det fortfarande finns kapacitet i poolen och instansen, J2, bearbetas av SI1.
- Om J2 hade bett om 11 noder har det inte funnits någon kapacitet i SP1 eller SI1. I det här fallet kommer jobbet att avvisas om J2 kommer från en antecknings bok. om J2 kommer från ett batch-jobb placeras det i kö.

### <a name="example-2"></a>Exempel 2

- Du skapar en spark-pool-anrop SP2; den har aktiverade 10 – 20 noder med autoskalning
- Du skickar ett anteckningsbok-jobb, J1 som använder 10 noder, en spark-instans, SI1, skapas för att bearbeta jobbet.
- Nu kan du skicka ett annat jobb, J2, som använder 10 noder, eftersom det fortfarande finns kapacitet i poolen att instansen automatiskt växer till 20 noder och bearbetar J2.

### <a name="example-3"></a>Exempel 3

- Du skapar en spark-pool med namnet SP1; den har en fast kluster storlek på 20 noder.
- Du skickar ett anteckningsbok-jobb, J1 som använder 10 noder, en spark-instans, SI1 skapas för att bearbeta jobbet.
- En annan användare, U2, skickar ett jobb, J3, som använder 10 noder, en ny Spark-instans, SI2, skapas för att bearbeta jobbet.
- Nu skickar du ett annat jobb, J2, som använder 10 noder eftersom det fortfarande finns kapacitet i poolen och instansen J2 bearbetas av SI1.

## <a name="next-steps"></a>Nästa steg

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark dokumentation](https://spark.apache.org/docs/2.4.4/)
