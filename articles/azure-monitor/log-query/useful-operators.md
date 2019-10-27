---
title: Användbara operatorer i Azure Monitor logg frågor | Microsoft Docs
description: Vanliga funktioner som ska användas för olika scenarier i Azure Monitor logg frågor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 022a9f638b3a7d8ae4ebeff8062f258ada7a14f8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932886"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Användbara operatorer i Azure Monitor logg frågor

Tabellen nedan innehåller några vanliga funktioner som kan användas i olika scenarier i Azure Monitor logg frågor.

## <a name="useful-operators"></a>Användbara operatorer

Kategori                                |Relevant analys funktion
----------------------------------------|----------------------------------------
Markerings-och kolumnalias            |`project`, `project-away`, `extend`
Temporära tabeller och konstanter          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Jämförelse-och sträng operatorer         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Vanliga sträng funktioner                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Vanliga matematiska funktioner                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Tolka text                            |`extract()`, `extractjson()`, `parse`, `split()`
Begränsa utdata                         |`take`, `limit`, `top`, `sample`
Datum funktioner                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Gruppering och agg regering                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Kopplingar och unioner                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Sortera, sortera                             |`sort`, `order` 
Dynamiskt objekt (JSON och matris)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Logiska operatorer                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Machine Learning                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Nästa steg

- Gå igenom en lektion om att [skriva logg frågor i Azure Monitor](get-started-queries.md).
