---
title: Vanliga frågor och svar om Azure Synapse Analytics (för hands versioner av arbets ytor)
description: Vanliga frågor och svar om Azure Synapse Analytics (för hands versioner av arbets ytor)
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: d7e8ea231dd6db9eab6cf9115f6ce2e62893371a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305416"
---
# <a name="azure-synapse-analytics-workspaces-preview-frequently-asked-questions"></a>Azure Synapse Analytics (för hands versioner av arbets ytor) vanliga frågor och svar

I den här guiden hittar du de vanligaste frågorna för Synapse-analys.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Allmänt

### <a name="q-what-is-azure-synapse-analytics"></a>F: Vad är Azure Synapse Analytics

A: Azure Synapse är en integrerad data plattform för BI, AI och kontinuerlig intelligens. Den ansluter olika analys körningar som SQL och Apache Spark via en enda plattform som gör det möjligt att:

- Skydda dina analys resurser, inklusive nätverk, hantering av enkel inloggning för att få åtkomst till pooler, data och utvecklings artefakter.
- Du kan enkelt övervaka och snabbt optimera, reagera och felsöka händelser i dina arbets ytans aktiviteter i valfritt lager.
- Hantera dina metadata i olika motorer. Skapa en server lös Apache Spark pool-tabell och den kommer automatiskt att vara tillgänglig i dina Azure Synapse-databaser.
- Interagera med data via en enhetlig användar upplevelse. Synapse Studio ger stor data utvecklare, data tekniker, databas administratörer, data analytiker och data experter på samma plattform.

Mer information finns i [Vad är Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics/overview-what-is).

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>F: vilka är huvud komponenterna i Azure Synapse Analytics

A: Azure Synapse har följande funktioner:

- Analys funktioner erbjuds via dedikerad SQL-pool eller SQL-pool utan server (för hands version).
- Server lös Apache Spark pool (för hands version) med fullständigt stöd för Scala, python, Spark, SQL och C #
- Data flöde erbjuder en kod fri transformering av Big data Transformation
- Data integrering & Orchestration för att integrera dina data och operationalisera all kod utveckling
- Studio för att få åtkomst till alla dessa funktioner via ett enda webb gränssnitt

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>F: Hur relaterar Azure Synapse Analytics till Azure SQL Data Warehouse

A: Azure Synapse Analytics är en utveckling av Azure SQL Data Warehouse i en analys plattform, som innehåller dedikerad SQL-pool som data lager lösning. Den här plattformen kombinerar data utforskning, inmatning, omvandling, förberedelse och ett betjänande analys lager.

## <a name="use-cases"></a>Användningsfall

### <a name="q-how-do-i-rename-a-published-artifact-dataset-notebook-sql-script-and-so-on-in-azure-synapse"></a>F: Hur gör jag för att byta namn på en publicerad artefakt (data uppsättning, Notebook, SQL-skript och så vidare) i Azure-Synapse?

A: om du vill byta namn på en publicerad artefakt fil måste du först klona filen och byta namn på den nya filen till det namn som du föredrar. Du måste manuellt uppdatera alla referenserna för artefakten till det nya fil namnet och ta bort den gamla.

### <a name="q-what-is-a-good-use-case-for-dedicated-sql-pool"></a>F: Vad är ett bra användnings fall för dedikerad SQL-pool

A: en dedikerad SQL-pool är hjärtat i ditt informations lager behov. Det är den ledande informations lager lösningen i [pris/prestanda](https://azure.microsoft.com/services/sql-data-warehouse/compare/). Dedikerad SQL-pool är den branschledande moln informations lager lösningen eftersom du kan:

- hantera en stor och blandad mängd arbets belastningar utan påverkan på prestanda tack vare hög samtidighet och arbets belastnings isolering
- skydda dina data enkelt genom avancerade funktioner som sträcker sig från nätverks säkerhet till detaljerad åtkomst
- Dra nytta av ett brett spektrum av eko system

### <a name="q-what-is-a-good-use-case-for-serverless-apache-spark-pool-in-azure-synapse"></a>F: Vad är ett bra användnings fall för Server lös Apache Spark pool i Azure Synapse

A: vårt första mål är att tillhandahålla en bra data teknik för att transformera data över sjön i batch eller Stream. Den nära och enkla integrationen med vår datadirigering gör driftsättning av utvecklings arbetet enkelt.

Ett annat användnings fall för Apache Spark är för en data expert för att:

- extrahera en funktion
- utforska data
- träna en modell

### <a name="q-what-is-a-good-use-case-for-serverless-sql-pool-in-azure-synapse"></a>F: Vad är ett bra användnings fall för SQL-poolen utan server i Azure Synapse

A: SQL-pool utan server är en fråga till tjänsten över data i data Lake. Det ger dig möjlighet att demokratisera identifieringen av åtkomst till alla dina data genom att tillhandahålla en välbekant T-SQL-syntax för att skicka frågor till data på plats, utan att behöva kopiera eller läsa in data i ett specialiserat lager.

Exempel på användnings exempel är följande:

- grundläggande identifiering och utforskning – tillhandahåller dataanalytiker, nya data forskare och data tekniker med en enkel väg till insikter om data boende i data Lake med schema för Läs-T-SQL-frågor
- det logiska informations lagret – data analytiker kan köra hela Expressiveness för T-SQL-språk för att direkt fråga och analysera data som finns i Azure Storage och använda välkända BI-verktyg (t. ex. Azures analys tjänster, Power BI Premium osv.) för att uppdatera instrument paneler genom att köra frågor om Starlight frågor
- "enskild fråga" ETL – gör det möjligt för data tekniker att transformera Azure Storage baserade data från ett format till ett annat, filter, agg regering osv. i massivt parallell bearbetnings sätt, spara frågeresultat för att Azure Storage och göra dem omedelbart tillgängliga för vidare bearbetning i Starlight-fråga eller andra tjänster av intresse

### <a name="q-what-is-a-good-use-case-for-data-flow-in-azure-synapse"></a>F: Vad är ett bra användnings fall för data flödet i Azure Synapse

A: data flödet gör det möjligt för data tekniker att utveckla grafisk data omvandlings logik utan att skriva kod. De resulterande data flödena körs som aktiviteter i data integration & Orchestration. Data flödes aktiviteter kan användas via befintliga funktioner för schemaläggning, kontroll, flöde och övervakning.

## <a name="security-and-access"></a>Säkerhet och åtkomst

A: slut punkt till slut punkt för enkel inloggning är en viktig autentiseringsprocess i Synapse Analytics. Att hantera och skicka identiteten via via en fullständig Azure AD-integrering är en måste.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adls-gen2"></a>F: Hur gör jag för att få åtkomst till filer och mappar i ADLS Gen2

A: åtkomst till filer och mappar hanteras för närvarande via ADLS Gen2. Mer information finns i [data Lake Storage Access Control](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>F: kan jag använda Business Intelligence-verktyg från tredje part för att få åtkomst till Azure Synapse Analytics

A: Ja, du kan använda företags program från tredje part, t. ex. Tableau och Power BI, för att ansluta till en dedikerad SQL-pool och en server lös SQL-pool. Spark stöder IntelliJ.

## <a name="next-steps"></a>Nästa steg

- [Skapa en arbetsyta](quickstart-create-workspace.md)
- [Använda Synapse Studio](quickstart-synapse-studio.md)
- [Skapa en dedikerad SQL-pool](quickstart-create-sql-pool-portal.md)
- [Använd Server lös SQL-pool](quickstart-sql-on-demand.md)
- [Skapa en server lös Apache Spark-pool](quickstart-create-apache-spark-pool-portal.md) 
