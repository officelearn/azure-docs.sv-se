---
title: Lathund - Azure Synapse Analytics
description: Referensguide går användare genom Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: aa93a816fc11158d928978bdec2dbf42119fa149
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424659"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Lathund för Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

Azure Synapse Analytics-lathunden guidar dig genom de grundläggande begreppen för tjänsten och viktiga kommandon. Den här artikeln är användbar för både nya elever och de som vill ha höjdpunkter i de viktiga Azure Synapse-ämnena.

## <a name="architecture"></a>Arkitektur

> [!div class="mx-imgBorder"]
>![Synapse Arkitektur](media/overview-cheat-sheet/azure-synapse-architecture-cheat-sheet.png)

## <a name="concepts"></a>Begrepp
| Substantiv och verb                         | Vad läget gör       |
|:---                                 |:---                 |
| **Synapse Arbetsyta (förhandsgranskning)** | En säkerhetsskyddad samarbetsgräns för att göra molnbaserad företagsanalys i Azure. En arbetsyta distribueras i en viss region och har ett associerat ADLSg2-konto och filsystem (för lagring av tillfälliga data). En arbetsyta finns under en resursgrupp. |
| **SQL-analys**   | Kör analyser med pooler eller med funktioner på begäran.  |
| **SQL-pool**   | 0-till-N SQL-etablerade resurser med motsvarande databaser kan distribueras på en arbetsyta. Varje SQL-pool har en associerad databas. En SQL-pool kan skalas, pausas och återupptas manuellt eller automatiskt. En SQL-pool kan skala från 100 DWU upp till 30 000 DWU.       |
| **SQL on-demand (förhandsgranskning)**   | Distribuerat databehandlingssystem som skapats för storskaliga data som gör att du kan köra T-SQL-frågor över data i datasjön. Det är serverlöst så du behöver inte hantera infrastrukturen.       |
|**Apache Spark** | Gnistkörningstid som används i en Spark-pool. Den aktuella versionen som stöds är Spark 2.4 med Python 3.6.1, Scala 2.11.12, .NET-stöd för Apache Spark 0.5 och Delta Lake 0.3.  | 
| **Apache Spark-pool (förhandsgranskning)**  | 0-till-N Spark-etablerade resurser med motsvarande databaser kan distribueras på en arbetsyta. En Spark-pool kan pausas automatiskt, återupptas och skalas.  |
| **Spark-program**  |   Den består av en drivrutinsprocess och en uppsättning körprocesser. Ett Spark-program körs på en Spark-pool.            |
| **Spark-session**  |   Enhetlig startpunkt för ett spark-program. Det ger ett sätt att interagera med Spark olika funktioner och med ett mindre antal konstruktioner. Om du vill köra en anteckningsbok måste en session skapas. En session kan konfigureras för att köras på ett visst antal utförare av en viss storlek. Standardkonfigurationen för en anteckningsbokssession är att köras på två medelstora utförare. |
| **SQL-begäran**  |   Åtgärd som en fråga som körs via SQL-pool eller SQL på begäran. |
|**Dataintegrering**| Ger möjlighet att matta data mellan olika källor och dirigera aktiviteter som körs inom en arbetsyta eller utanför en arbetsyta.| 
|**Artefakter**| Koncept som kapslar in alla objekt som krävs för att en användare ska kunna hantera datakällor, utveckla, dirigera och visualisera.|
|**Notebook-fil**| Interaktivt och reaktivt datavetenskapsgränssnitt med stöd för Scala, PySpark, C#och SparkSQL. |
|**Spark-jobbdefinition**|Gränssnitt för att skicka ett Spark-jobb med monteringsburk som innehåller koden och dess beroenden.|
|**Dataflöde**|  Ger en helt visuell upplevelse utan kodning som krävs för att göra stordataomvandling. All optimering och utförande hanteras på ett serverlöst sätt. |
|**SQL-skript**| Uppsättning SQL-kommandon som sparats i en fil. Ett SQL-skript kan innehålla ett eller flera SQL-uttryck. Den kan användas för att köra SQL-begäranden via SQL-pool eller SQL på begäran.|
|**Rörledning**| Logisk gruppering av aktiviteter som utför en uppgift tillsammans.|
|**Aktivitet**| Definierar åtgärder som ska utföras på data som att kopiera data, köra en anteckningsbok eller ett SQL-skript.|
|**Utlösare**| Kör en pipeline. Det kan köras manuellt eller automatiskt (schema, tumlande fönster eller händelsebaserad).|
|**Länkad tjänst**| Anslutningssträngar som definierar den anslutningsinformation som krävs för att arbetsytan ska kunna ansluta till externa resurser.|
|**Datamängd**|  Namngiven vy över data som helt enkelt pekar eller refererar till de data som ska användas i en aktivitet som indata och utdata. Den tillhör en länkad tjänst.|

## <a name="next-steps"></a>Nästa steg

- [Skapa en arbetsyta](quickstart-create-workspace.md)
- [Använd Synapse Studio](quickstart-synapse-studio.md)
- [Skapa en SQL-pool](quickstart-create-sql-pool.md)
- [Använda SQL på begäran](quickstart-sql-on-demand.md)
- [Skapa en Apache Spark-pool](quickstart-create-apache-spark-pool.md)

