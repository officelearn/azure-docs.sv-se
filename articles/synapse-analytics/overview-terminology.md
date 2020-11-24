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
ms.openlocfilehash: 20e635d606676a526fac4c3720931525beb66876
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95790413"
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

* **SQL-begäran** – åtgärd som en fråga som körs via dedikerad SQL-pool eller SQL-pool utan server.
* **SQL-skript** – uppsättning av SQL-kommandon som sparats i en fil. Ett SQL-skript kan innehålla ett eller flera SQL-uttryck. Den kan användas för att köra SQL-begäranden via dedikerad SQL-pool eller Server lös SQL-pool.

## <a name="apache-spark-for-synapse"></a>Apache Spark för Synapse

Om du vill använda Spark Analytics skapar du och använder **Server lös Apache Spark pooler** på din Synapse-arbetsyta.

* **Apache Spark pool** -0-till-N Spark-etablerade resurser med motsvarande databaser kan distribueras i en arbets yta. En spark-pool kan pausas automatiskt, återupptas och skalas.  
* **Spark-program** – det består av en driv rutins process och en uppsättning utförar-processer. Ett Spark-program körs på en server lös Spark-pool.            
* **Spark-session**– enhetlig start punkt för ett Spark-program. Det ger ett sätt att interagera med Spark: s olika funktioner och med ett mindre antal konstruktioner. Om du vill köra en antecknings bok måste du skapa en session. En session kan konfigureras för att köras på ett angivet antal körningar av en speciell storlek. Standard konfigurationen för en Notebook-session körs på 2 medel stora körningar.
* **Notebook** – interaktiv och reaktiv data vetenskap och teknik gränssnitt som stöder Scala, PySpark, C# och SparkSQL.
* **Spark-jobb definition** – gränssnitt för att skicka ett Spark-jobb med hjälp av Assembly jar som innehåller koden och dess beroenden.

Versions stöd:
* Spark 2,4
* Python-3.6.1
* Scala 2.11.12
* .NET för Apache Spark 1,0
* Delta Lake 0,3.  

## <a name="pipelines"></a>Pipelines

* **Data integrering** – ger möjlighet att mata in data mellan olika källor och dirigera aktiviteter som körs i en arbets yta eller utanför en arbets yta.
* **Data flöde** – ger en helt visuell upplevelse utan kodning som krävs för att utföra stor data omvandling. All optimering och körning hanteras utan server.
* **Pipeline** – logisk gruppering av aktiviteter som utför en aktivitet tillsammans.
* **Activity** -definierar åtgärder som ska utföras på data, till exempel kopiera data, köra en bärbar dator eller ett SQL-skript.
* **Trigger** – kör en pipeline. Den kan köras manuellt eller automatiskt (schema, rullande Window eller event-based)
* **Integrations data uppsättning** – namngiven vy av data som bara pekar eller refererar till de data som ska användas i en aktivitet som indata och utdata. Den tillhör en länkad tjänst.

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Azure Synapse Analytics](get-started.md)
* [Skapa en arbetsyta](quickstart-create-workspace.md)
* [Använda en serverlös SQL-pool](quickstart-sql-on-demand.md)

