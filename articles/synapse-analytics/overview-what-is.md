---
title: Vad är Azure Synapse Analytics?
description: En översikt över Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 65f6c6627093358f8cbc66055bb9b16561f7c610
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424666"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Vad är Azure Synapse Analytics (förhandsversion av arbetsytor)?

[!INCLUDE [preview](includes/note-preview.md)]

Idag företagsanalys kräver att arbeta i stor skala på alla typer av data, oavsett om råa, raffinerade eller mycket kurerade. Förr i tiden, bygga denna typ av analyslösningar krävs företag att sy ihop stordata och data lagerteknik som Spark och SQL. Därefter skulle de behöva integrera dem i avancerade datapipelpipelar som fungerar över data i relationslager och datasjöar.  

Lösningar som denna är svåra att bygga, konfigurera, säkra och underhålla, vilket försenar den snabba utvinningen av intelligent insikt.

**Azure Synapse** är en integrerad analystjänst som påskyndar tiden till insikt från alla data i valfri skala, över datalager och stordataanalyssystem. Det sammanför det bästa av **DE SQL-tekniker** som används i företagsdatalagring, **Spark-teknik** som används i stordataanalys och **pipelines** för att dirigera aktiviteter och dataförflyttningar. 

Azure Synapse levereras med **Studio** en webbbaserad Studio-användarupplevelse som ger en enda upplevelse och modell för hantering, övervakning, kodning och säkerhet.

Azure Synapse är det enklaste och snabbaste sättet för ett företag att samla in insikter om alla data i alla storlekar, med hjälp av de analyser de är mest bekanta med. Den integreras djupt med **Power BI** så att datatekniker kan skapa analyslösningar som arbetar från på tid för att tillhandahålla Business Intelligence. 

Dessutom underlättar Azure Synapse förutsägande modelluppbyggnad och avancerad analys med maskininlärning via dess inbyggda stöd för **AzureML**.

## <a name="key-features--benefits"></a>Viktiga funktioner & fördelar

### <a name="industry-leading-sql"></a>Branschledande SQL

* **Synapse SQL** är ett distribuerat frågesystem som gör det möjligt för företag att implementera datalagring och datavirtualiseringsscenarier med hjälp av vanliga T-SQL-upplevelser som är välbekanta för datatekniker. Det utökar också funktionerna i SQL för att hantera streaming och machine learning scenarier.

* Synapse SQL erbjuder både **serverlösa** och **etablerade** resursmodeller, som erbjuder förbruknings- och faktureringsalternativ som passar dina behov. För förutsägbara prestanda och kostnader, etablera pooler för att reservera processorkraft för data som lagras i SQL-tabeller. För oplanerade eller bursty arbetsbelastningar använder du den serverlösa, alltid tillgängliga SQL-slutpunkten.
* Använd inbyggda **streamingfunktioner** för att landa data från molndatakällor i SQL-tabeller
* Integrera AI med SQL genom att använda **maskininlärningsmodeller** för att få data med hjälp av funktionen T-SQL PREDICT

### <a name="industry-standard-apache-spark"></a>Apache Spark av branschstandard

**Synapse Spark integrerar** på ett djupt och smidigt sätt Apache Spark – den mest populära stordatamotorn med öppen källkod som används för dataförberedelse, datateknik, ETL och maskininlärning.

* ML-modeller med SparkML-algoritmer och AzureML-integrering för Apache Spark 2.4 med inbyggt stöd för Linux Foundation Delta Lake.
* Förenklad resursmodell som befriar dig från att behöva oroa dig för att hantera kluster.
* Snabb Spark start-up och aggressiv autoscaling.
* Inbyggt stöd för .NET for Spark så att du kan återanvända din C#-expertis och befintlig .NET-kod i ett Spark-program.

### <a name="interop-of-sql-and-spark-on-your-data-lake"></a>Interop av SQL och Spark på din Data Lake

Azure Synapse tar bort de traditionella teknikbarriärerna mellan att använda SQL och Spark tillsammans. Du kan sömlöst blanda och matcha baserat på dina behov och expertis.

* Ett delat Hive-kompatibelt metadatasystem gör att tabeller som definierats på filer i datasjön kan förbrukas sömlöst av antingen Spark eller Hive.
* SQL- och Spark kan direkt utforska och analysera Parett-, CSV-, TSV- och JSON-filer som lagras i datasjön.
* Snabb skalbar belastning och lossning för data som går mellan SQL- och Spark-databaser

### <a name="built-in-orchestration-via-pipelines"></a>Inbyggd orkestrering via rörledningar

Azure Synapse levereras inbyggt med samma dataintegrationsmotor och -upplevelser som Azure Data Factory, så att du kan skapa omfattande datapipelpipelsar utan att använda en separat orkestreringsmotor.

* Flytta data mellan Synapse och 85+ lokala datakällor
* Orkestrera anteckningsböcker, pipelines, Spark-jobb, SQL-skript, lagrade procedurer
* Kodfri ETL med dataflödesaktiviteter

### <a name="unified-management-monitoring-and-security"></a>Enhetlig hantering, övervakning och säkerhet

Azure Synapse är ett enda sätt för företag att hantera analysresurser, övervaka användning och aktivitet och upprätthålla säkerhet.

* Tilldela användare till rollen för att förenkla åtkomsten till analysresurser
* Finkornig åtkomstkontroll av data och kod
* En enda instrumentpanel för att övervaka resurser, användning och användare i SQL och Spark

### <a name="synapse-studio"></a>Synapse Studio

**Synapse Studio** är den webbinbyggda upplevelsen som binder ihop allt för datatekniker, så att de på en plats kan utföra alla uppgifter de behöver för att bygga en komplett lösning.

* Skapa en heltäckande analyslösning på ett ställe: inta, utforska, förbereda, orkestrera, visualisera
* Branschledande produktivitet för datatekniker som skriver SQL- eller Spark-kod: redigering, felsökning och prestandaoptimering
* Integrera med CI/CD-processer för företag

## <a name="next-steps"></a>Nästa steg

* [Skapa en arbetsyta](quickstart-create-workspace.md)
* [Använd Synapse Studio](quickstart-synapse-studio.md)
* [Skapa en SQL-pool](quickstart-create-sql-pool.md)
* [Använda SQL på begäran](quickstart-sql-on-demand.md)
* [Skapa en Apache Spark-pool](quickstart-create-apache-spark-pool.md)
