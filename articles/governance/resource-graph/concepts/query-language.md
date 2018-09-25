---
title: Förstå Azure Resource Graph-frågespråk
description: Beskriver hur frågespråk för Azure Resource Graph fungerar.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 3600fc47a0fb318a49c1b37722cb7fffa51ec6f2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951958"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Förstå Azure Resource Graph-frågespråk

Frågespråket i Azure Resource Graph stöder ett antal operatorer och funktioner. Varje fungerar och arbetar på samma sätt till Kusto Query Language (KQL). Det är dock viktigt att förstå att medan resursen Graph frågespråk liknar [KQL](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators), det är inte densamma.

> [!NOTE]
> Länkar till KQL dokumentation kan kräva autentisering.

Det bästa sättet att lära dig frågespråket som används av resursen Graph är att börja med i dokumentationen för KQL och lär dig om hur språket är strukturerad och hur de olika stöds operatorer och funktioner fungerar tillsammans.

## <a name="supported-tabular-operators"></a>Tabular operatorer som stöds

Här är listan över tabular operatorer som stöds i resursen Graph:

- [Distinkta](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator)
- [Utöka](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator)
- [Gränsen](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/limit-operator)
- [order by-](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/order-operator)
- [Projekt](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator)
- [projekt-away](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-away-operator)
- [Exemplet](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sample-operator)
- [exempel-distinkta](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sample-distinct-operator)
- [Sortera efter](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator)
- [Sammanfatta](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator)
- [ta](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
- [längst upp](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator)
- [TOP-nested](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-nested-operator)
- [TOP-hitters](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-hitters-operator)
- [där](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

## <a name="supported-functions"></a>Funktioner som stöds

Här är listan över funktioner som stöds i resursen Graph:

- [ago()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/ago%28%29)
- [buldschema()](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/buildschema%28%29)
- [Antal()](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/count%28%29)
- [strcat()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/strcat%28%29)
- [isnotempty()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/isnotempty%28%29_-notempty%28%29)
- [toString)](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/tostring%28%29)

## <a name="next-steps"></a>Nästa steg

- Se språket som användes i [Starter frågor](../samples/starter.md)
- Se avancerade använder i [avancerade frågor](../samples/advanced.md)
- Lär dig hur du [Utforska resurser](explore-resources.md)