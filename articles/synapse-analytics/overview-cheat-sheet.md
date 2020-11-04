---
title: Lathund-blad – Azure Synapse Analytics (för hands versioner av arbets ytor)
description: Referens guide som vägleder användaren genom Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: c8ae47f89dcddd2253abd21239eb1a9bffee27c7
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307831"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Lathund-blad för Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

Azure Synapse Analytics lathund-bladet hjälper dig genom de grundläggande begreppen i tjänsten och viktiga kommandon. Den här artikeln är användbar för både nya elever och de som vill ha högdagrar om de viktiga Azure Synapse-ämnena.

## <a name="basics"></a>Grunder

En **Synapse-arbetsyta** är en skydds bara samarbets gränser för att utföra molnbaserad företags analys i Azure. En arbets yta distribueras i en angiven region och har ett associerat ADLS Gen2-konto och fil system (för lagring av temporära data). En arbets yta är under en resurs grupp.

I en arbets yta kan du utföra analyser med SQL och Apache Spark. Resurser som är tillgängliga för SQL och Spark Analytics är indelade i SQL-och Spark- **pooler**. 

## <a name="synapse-sql"></a>Synapse SQL

**SYNAPSE SQL** är möjligheten att utföra T-SQL-baserad analys i Synapse-arbetsytan. Synapse SQL har två förbruknings modeller: dedikerade och Server lös.  För den dedikerade modellen använder du **dedikerade SQL-pooler**. En arbets yta kan ha valfritt antal pooler. Använd Server lös **SQL-pooler** om du vill använda en server lös modell. Varje arbets yta har en av dessa pooler.

## <a name="apache-spark-for-synapse"></a>Apache Spark för Synapse

Om du vill använda Spark Analytics skapar du och använder **Server lös Apache Spark pooler** på din Synapse-arbetsyta.

## <a name="sql-terminology"></a>SQL-terminologi
| Term                         | Definition      |
|:---                                 |:---                 |
| **SQL-begäran**  |   Åtgärd, till exempel en fråga som körs via dedikerad SQL-pool eller SQL-pool utan server. |

## <a name="spark-terminology"></a>Spark-terminologi
| Term                         | Definition      |
|:---                                 |:---                 |
|**Apache Spark för Synapse** | Spark-körning används i en server lös Spark-pool. Den aktuella versionen som stöds är Spark 2,4 med python 3.6.1, Scala 2.11.12, .NET support för Apache Spark 0,5 och delta Lake 0,3.  | 
| **Apache Spark pool**  | 0-till-N Spark-etablerade resurser med motsvarande databaser kan distribueras i en arbets yta. En spark-pool kan pausas automatiskt, återupptas och skalas.  |
| **Spark-program**  |   Det består av en driv rutins process och en uppsättning utförar processer. Ett Spark-program körs på en server lös Spark-pool.            |
| **Spark-session**  |   Enhetlig start punkt för ett Spark-program. Det ger ett sätt att interagera med Spark: s olika funktioner och med ett mindre antal konstruktioner. Om du vill köra en antecknings bok måste du skapa en session. En session kan konfigureras för att köras på ett angivet antal körningar av en speciell storlek. Standard konfigurationen för en Notebook-session körs på 2 medel stora körningar. |
|**Data integrering**| Ger möjlighet att mata in data mellan olika källor och att dirigera aktiviteter som körs på en arbets yta eller utanför en arbets yta.| 
|**Artifacts**| Koncept som kapslar in alla objekt som krävs för att en användare ska kunna hantera data källor, utveckla, dirigera och visualisera.|
|**Notebook-fil**| Interaktivt och reaktivt gränssnitt för data vetenskap och teknik som stöder Scala, PySpark, C# och SparkSQL. |
|**Definition av Spark-jobb**|Gränssnitt för att skicka ett Spark-jobb med hjälp av Assembly jar som innehåller koden och dess beroenden.|
|**Dataflöde**|  Ger en helt visuell upplevelse utan kodning som krävs för att utföra stor data omvandling. All optimering och körning hanteras utan server. |
|**SQL-skript**| Uppsättning SQL-kommandon som sparas i en fil. Ett SQL-skript kan innehålla ett eller flera SQL-uttryck. Den kan användas för att köra SQL-begäranden via dedikerad SQL-pool eller Server lös SQL-pool.|
|**Pipeline**| Logisk gruppering av aktiviteter som utför en aktivitet tillsammans.|
|**Aktivitet**| Definierar åtgärder som ska utföras på data, till exempel kopiera data, köra en bärbar dator eller ett SQL-skript.|
|**Utlösare**| Kör en pipeline. Den kan köras manuellt eller automatiskt (schema, rullande-fönster eller event-based).|
|**Länkad tjänst**| Anslutnings strängar som definierar den anslutnings information som behövs för arbets ytan för att ansluta till externa resurser.|
|**Data uppsättning**|  Namngiven vy av data som bara pekar eller refererar till de data som ska användas i en aktivitet som indata och utdata. Den tillhör en länkad tjänst.|

## <a name="next-steps"></a>Nästa steg

- [Skapa en arbetsyta](quickstart-create-workspace.md)
- [Använda Synapse Studio](quickstart-synapse-studio.md)
- [Skapa en dedikerad SQL-pool](quickstart-create-sql-pool-portal.md)
- [Skapa en server lös Apache Spark-pool](quickstart-create-apache-spark-pool-portal.md)
- [Använd Server lös SQL-pool](quickstart-sql-on-demand.md)

