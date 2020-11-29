---
title: Terminologi – Azure Synapse Analytics (för hands versioner av arbets ytor)
description: Referens guide som vägleder användaren genom Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 60c3fc6688dc4a25faf5ed94df25055e29d34403
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2020
ms.locfileid: "96309825"
---
# <a name="azure-synapse-analytics-terminology"></a>Azure Synapse Analytics-terminologi

[!INCLUDE [preview](includes/note-preview.md)]

Det här dokumentet vägleder dig genom de grundläggande begreppen i Azure Synapse Analytics.

## <a name="basics"></a>Grundläggande inställningar

En **Synapse-arbetsyta** är en skydds bara samarbets gränser för att utföra molnbaserad företags analys i Azure. En arbets yta distribueras i en angiven region och har ett associerat ADLS Gen2-konto och fil system (för lagring av temporära data). En arbets yta är under en resurs grupp.

I en arbets yta kan du utföra analyser med SQL och Apache Spark. Resurser som är tillgängliga för SQL och Spark Analytics är indelade i SQL-och Spark- **pooler**. 

## <a name="linked-services"></a>Länkade tjänster

En arbets yta kan innehålla valfritt antal **länkade tjänster**, i princip anslutnings strängar som definierar den anslutnings information som behövs för att arbets ytan ska kunna ansluta till externa resurser.

## <a name="synapse-sql"></a>Synapse SQL

**SYNAPSE SQL** är möjligheten att utföra T-SQL-baserad analys i Synapse-arbetsytan. Synapse SQL har två förbruknings modeller: dedikerade och Server lös.  För den dedikerade modellen använder du **dedikerade SQL-pooler**. En arbets yta kan ha valfritt antal pooler. Använd Server lös **SQL-pooler** om du vill använda en server lös modell. Varje arbets yta har en av dessa pooler.

I Synapse Studio kan du arbeta med SQL-pooler genom att skapa och köra **SQL-skript** .

## <a name="apache-spark-for-synapse"></a>Apache Spark för Synapse

Om du vill använda Spark Analytics skapar du och använder **Server lös Apache Spark pooler** på din Synapse-arbetsyta. När du börjar använda en spark-pool skapar arbets ytorna en **Spark-session** för att hantera resursernas assosociated med den sessionen. 

Det finns två sätt i Synapse att använda Spark:
* **Spark-anteckningsböcker** för data vetenskap och-teknik använder Scala, PySpark, C# och SparkSQL
* **Spark-jobb definitioner** för att köra batch Spark-jobb med JAR-filer.

## <a name="pipelines"></a>Pipelines

Pipelines är hur Azure Synapse tillhandahåller data integrering – så att du kan flytta data mellan tjänster och dirigera aktiviteter.

* **Pipeline** är logisk gruppering av aktiviteter som utför en aktivitet tillsammans.
* **Aktiviteter** definierar åtgärder i en pipeline för att utföra data, till exempel kopiera data, köra en bärbar dator eller ett SQL-skript.
* **Data flöden** är en viss typ av aktivitet som ger en kod utan kod för att utföra datatransformering som använder Synapse Spark under-The-Cover.
* **Trigger** – kör en pipeline. Den kan köras manuellt eller automatiskt (schema, rullande Window eller event-based)
* **Integrations data uppsättning** – namngiven vy av data som bara pekar eller refererar till de data som ska användas i en aktivitet som indata och utdata. Den tillhör en länkad tjänst.

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Azure Synapse Analytics](get-started.md)
* [Skapa en arbetsyta](quickstart-create-workspace.md)
* [Använda en serverlös SQL-pool](quickstart-sql-on-demand.md)

