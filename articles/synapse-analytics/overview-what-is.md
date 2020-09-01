---
title: Vad är Azure Synapse Analytics?
description: En översikt över Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: f9277842339d7c4fa74e2e0f1febcf5916d86f4a
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180787"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Vad är Azure Synapse Analytics (för hands versioner av arbets ytor)?

[!INCLUDE [preview](includes/note-preview.md)]

Idag Enterprise Analytics kräver en enorm skala på alla typer av data, både RAW, raffinerad eller mycket anpassad. Tidigare var du tvungen att skapa dessa typer av analys lösningar som krävs för att kunna häfta ihop Big data och data lager tekniker som Spark och SQL. Därefter måste de integrera dem i omfattande datapipeliner som fungerar mellan data i Relations lager och data sjöar.  

Lösningar som detta är svåra att bygga, konfigurera, skydda och underhålla, vilket fördröjer Swift-extraktionen av intelligenta insikter.

**Azure Synapse** är en integrerad analys tjänst som påskyndar tiden till insikter från alla data i alla skalor, i informations lager och Big data analys system. Det kombinerar det bästa av **SQL** -teknikerna som används i företags data lager, **Spark** -tekniker som används i Big data Analytics och **pipelines** för data integrering och ETL/ELT.

Azure Synapse levereras med en webbaserad **Studio** -användar upplevelse som ger en enda upplevelse och modell för hantering, övervakning, kodning och säkerhet.

Azure Synapse erbjuder det enklaste och snabbaste sättet för ett företag att samla in insikter om alla data i valfri storlek, med hjälp av de analyser som de är mest bekanta med. Den integreras kraftigt med **Power BI** som gör det möjligt för data tekniker att skapa analys lösningar som fungerar från slut punkt till slut punkt för att ge Business Intelligence.

Dessutom underlättar Azure-Synapse förutsägelse modell utveckling och avancerad analys med Machine Learning via det inbyggda stödet för **azureml**.

## <a name="key-features--benefits"></a>Viktiga funktioner & förmåner

### <a name="industry-leading-sql"></a>Branschledande SQL

* **SYNAPSE SQL** är ett distribuerat Query-system som gör det möjligt för företag att implementera data lager-och data virtualiseringslösningar med hjälp av vanliga T-SQL-upplevelser som är bekanta med data tekniker. Den utökar också funktionerna i SQL för att adressera strömnings-och maskin inlärnings scenarier.

* Synapse SQL erbjuder både **Server** löst och **etablerade** resurs modeller, och erbjuder användnings-och fakturerings alternativ som passar dina behov. För förutsägbar prestanda och kostnad etablera pooler för att reservera bearbetnings kraft för data som lagras i SQL-tabeller. Använd den serverbaserade, alltid tillgängliga SQL-slutpunkten för oplanerade eller burst-arbetsbelastningar.
* Använd inbyggda **strömnings** funktioner för att landa data från moln data källor till SQL-tabeller
* Integrera AI med SQL genom att använda **maskin inlärnings** modeller för att räkna data med hjälp av [funktionen T-SQL predict](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)

### <a name="industry-standard-apache-spark"></a>Bransch standard Apache Spark

**Apache Spark för Azure Synapse** djup och sömlöst integrera Apache Spark – den mest populära Big data motorn med öppen källkod som används för förberedelse av data, data teknik, ETL och maskin inlärning.

* ML-modeller med SparkML-algoritmer och AzureML-integrering för Apache Spark 2,4 med inbyggt stöd för Linux Foundation delta Lake.
* Förenklad resurs modell som gör att du inte behöver oroa dig för att hantera kluster.
* Snabb start av Spark och aggressiv automatisk skalning.
* Inbyggt stöd för .NET för Spark gör att du kan återanvända din C#-expertis och befintliga .NET-kod i ett Spark-program.

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>Interop för SQL och Apache Spark på din Data Lake

Azure Synapse tar bort de traditionella teknik barriärerna mellan att använda SQL och Spark tillsammans. Du kan kombinera och matcha på ett smidigt sätt utifrån dina behov och kunskaper.

* Ett delat Hive-kompatibelt metadata system gör det möjligt för tabeller som definierats på filer i data Lake att vara sömlöst förbrukade antingen av Spark eller Hive.
* SQL och Spark kan direkt utforska och analysera Parquet-, CSV-, TSV-och JSON-filer som lagras i data Lake.
* Snabb skalbar belastning och borttagning av data som går mellan SQL-och Spark-databaser

### <a name="built-in-data-integration-via-pipelines"></a>Inbyggd data integrering via pipelines

Azure Synapse levereras med samma data integrerings motor och upplevelser som Azure Data Factory, så att du kan skapa omfattande ETL-pipelines utan att lämna Synapse Analytics.

* Mata in data från 90 + data källor
* Kod fri ETL med data flödes aktiviteter
* Dirigera antecknings böcker, Spark-jobb, lagrade procedurer, SQL-skript med mera

### <a name="unified-management-monitoring-and-security"></a>Enhetlig hantering, övervakning och säkerhet

Azure Synapse erbjuder ett enda sätt för företag att hantera analys resurser, övervaka användning och aktivitet och upprätthålla säkerhet.

* Tilldela användare till roll för att förenkla åtkomst till analys resurser
* Detaljerad åtkomst kontroll för data och kod
* En enda instrument panel för att övervaka resurser, användning och användare i SQL och Spark

### <a name="synapse-studio"></a>Synapse Studio

**Synapse Studio** är den webb inbyggda upplevelsen som kopplar ihop allt för data tekniker, så att de kan utföra alla uppgifter som behövs för att skapa en komplett lösning.

* Bygg en lösning från slut punkt till slut punkt på en plats: mata in, utforska, förbereda, dirigera, visualisera
* Branschledande produktivitet för data tekniker som skriver SQL-eller Spark-kod: redigering, fel sökning och prestanda optimering
* Integrera med Enterprise CI/CD-processer

## <a name="next-steps"></a>Nästa steg

* [Skapa en arbetsyta](quickstart-create-workspace.md)
* [Använda Synapse Studio](quickstart-synapse-studio.md)
* [Skapa en SQL-pool](quickstart-create-sql-pool-portal.md)
* [Använda SQL på begäran](quickstart-sql-on-demand.md)
* [Skapa en Apache Spark pool](quickstart-create-apache-spark-pool-portal.md)
