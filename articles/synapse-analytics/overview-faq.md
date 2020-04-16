---
title: Vanliga frågor och svar – Azure Synapse Analytics
description: Vanliga frågor och svar om Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: ec2791911d1461ae47d9353629d23a5d7dc30791
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424652"
---
# <a name="azure-synapse-analytics-workspace-preview-frequently-asked-questions"></a>Vanliga frågor och svar i Azure Synapse Analytics (förhandsversion av arbetsytan)

I den här guiden hittar du de vanligaste frågorna för Synapse Analytics.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Allmänt

### <a name="q-what-is-azure-synapse-analytics"></a>F: Vad är Azure Synapse Analytics

S: Azure Synapse är en integrerad dataplattform för BI, AI och kontinuerlig intelligens. Den ansluter olika analyskörningar som SQL och Spark via en enda plattform som ger ett enhetligt sätt att:

- Skydda dina analysresurser, inklusive nätverk, hantera enkel inloggningsåtkomst till pool-, data- och utvecklingsartefakter.
- Övervaka enkelt och optimera, reagera och felsöka händelser som händer i dina aktiviteter på arbetsytan i alla lager.
- Hantera dina metadata mellan motorer. Skapa en Spark-tabell så blir den automatiskt tillgänglig i dina Azure Synapse-databaser.
- Interagera med data genom en enhetlig användarupplevelse. Synapse Studio tar big data utvecklare, dataingenjörer, DBAs, dataanalytiker och dataforskare på samma plattform.

### <a name="q-how-do-i-get-started-with-azure-synapse-analytics"></a>F: Hur kommer jag igång med Azure Synapse Analytics

S: Om du vill börja använda Azure Synapse Analytics skapar du en [Synapse-arbetsyta](https://portal.azure.com) (den är kostnadsfri!) och skapar de resurser som du vill ha under arbetsytan. Du kan följa en av våra snabbstartshandledningar, till exempel [Skapa en Synapse SQL-pool](quickstart-create-sql-pool.md) eller [Skapa en arbetsyta](quickstart-create-workspace.md), som hjälper dig att använda enkla användningsfall. 

Du kan också hitta exempel anteckningsböcker och SQL-skript i vår [databas](https://github.com/Azure/azure-synapse-analytics/tree/master/samples). Om du behöver ansluta till en offentlig datauppsättning skapar du en ny länkad tjänst med följande attribut:

- azure_storage_account_name = "azureopendatastorage"
- azure_storage_sas_token = "" (skriv **""**)

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>F: Vilka är huvudkomponenterna i Azure Synapse Analytics

S: Azure Synapse har följande funktioner:

- Analytics-funktioner erbjuds via SQL-pool eller SQL on-demand (preview) (Serverless).
- Apache Spark-pool (förhandsversion) med fullt stöd för Scala, Python, SparkSQL och C #
- Dataflöde som erbjuder en kodfri upplevelse för omvandling av stordata
- Dataintegration & Orchestration för att integrera dina data och operationalisera all din kodutveckling
- Studio för att komma åt alla dessa funktioner via ett enda webbgränssnitt

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>F: Hur relaterar Azure Synapse Analytics till Azure SQL Data Warehouse

S: Azure Synapse Analytics är en utveckling av Azure SQL Data Warehouse till en analysplattform, som inkluderar SQL-pool som informationslagerlösning. Den här plattformen kombinerar datautforskning, inmatning, omvandling, förberedelse och ett serveringsanalyslager.

## <a name="use-cases"></a>Användningsfall

### <a name="q-what-is-a-good-use-case-for-synapse-sql-pool"></a>F: Vad är ett bra användningsfall för Synapse SQL-pool

S: SQL-poolen är hjärtat i dina informationslagerbehov. Det är den ledande datalagerlösningen i [pris/prestanda.](https://azure.microsoft.com/services/sql-data-warehouse/compare/) SQL-pool är den branschledande molninformationslagerlösningen eftersom du kan:

- betjäna en stor och blandad mängd arbetsbelastningar utan inverkan på prestanda tack vare hög samtidighet och arbetsbelastningsisolering
- enkelt skydda dina data genom avancerade funktioner som sträcker sig från nätverkssäkerhet till åtkomst till finkornig
- dra nytta av ett brett utbud av ekosystem

### <a name="q-what-is-a-good-use-case-for-spark-in-synapse"></a>F: Vad är ett bra användningsfall för Spark i Synapse

S: Vårt första mål är att ge en bra datateknikupplevelse för att omvandla data över sjön i batch eller ström. Dess snäva och enkla integration till vår dataorkestrering gör operationalization av ditt utvecklingsarbete enkelt.

Ett annat användningsfall för Spark är att en datavetenskapare:

- extrahera en funktion
- utforska data
- träna en modell

### <a name="q-what-is-a-good-use-case-for-sql-on-demand-in-synapse"></a>F: Vad är ett bra användningsfall för SQL on-demand i Synapse

S: SQL on-demand är en frågetjänst över data i datasjön. Det gör att du kan demokratisera åtkomsten till alla dina data genom att tillhandahålla en välbekant T-SQL-syntax för att fråga data på plats, utan att behöva kopiera eller läsa in data i ett specialiserat arkiv.

Exempel på användningsfall är följande:

- grundläggande upptäckt och utforskning - ger dataanalytiker, nya datavetare och dataingenjörer en enkel väg till första inblick i data som lever i deras datasjö med schema på lästa T-SQL-frågor
- logisk informationslager - dataanalytiker kan köra fullständig uttrycksfullhet i T-SQL-språk för att direkt fråga och analysera data som finns i Azure Storage och använda välbekanta BI-verktyg (t.ex. Azure Analyses Services, Power BI Premium, etc.) för att uppdatera instrumentpaneler genom att köra Starlight Query-frågor igen
- "single query" ETL - gör det möjligt för datatekniker att omvandla Azure Storage-baserade data från ett format till ett annat, filtrera, aggregera, etc. på ett massivt parallellt bearbetningssätt, beständiga frågeresultat till Azure Storage och göra dem omedelbart tillgängliga för vidare bearbetning i Starlight Query eller andra tjänster av intresse

### <a name="q-what-is-a-good-use-case-for-data-flow-in-synapse"></a>F: Vad är ett bra användningsfall för dataflöde i Synapse

S: Dataflödet gör det möjligt för datatekniker att utveckla grafisk dataomvandlingslogik utan att skriva kod. De resulterande dataflödena körs som aktiviteter inom dataintegration & Orchestration. Dataflödesaktiviteter kan operationaliseras via befintliga schemaläggnings-, kontroll-, flödes- och övervakningsfunktioner.

## <a name="security-and-access"></a>Säkerhet och åtkomst

S: End-to-end enkel inloggningsupplevelse är en viktig autentiseringsprocess i Synapse Analytics. Att hantera och passera genom identiteten via en fullständig AAD-integration är ett måste.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adlsg2"></a>F: Hur får jag tillgång till filer och mappar i ADLSg2

S: Åtkomst till filer och mappar hanteras för närvarande via ADLSg2. Mer information finns i [DataSjölagringsåtkomstkontroll](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>F: Kan jag använda business intelligence-verktyg från tredje part för att komma åt Azure Synapse Analytics

S: Ja, du kan använda dina affärsprogram från tredje part, till exempel Tableau och Power BI, för att ansluta till SQL-pool och SQL på begäran. Spark stöder IntelliJ.

## <a name="next-steps"></a>Nästa steg

- [Skapa en arbetsyta](quickstart-create-workspace.md)
- [Använd Synapse Studio](quickstart-synapse-studio.md)
- [Skapa en SQL-pool](quickstart-create-sql-pool.md)
- [Använda SQL på begäran](quickstart-sql-on-demand.md)
- [Skapa en Apache Spark-pool](quickstart-create-apache-spark-pool.md) 