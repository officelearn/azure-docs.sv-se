---
title: Vanliga frågor och svar om Azure Synapse
description: Vanliga frågor och svar om Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 007fe7cb90c651c346bc7fbea46d74aa41605d2d
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744083"
---
# <a name="azure-synapse-analytics-workspace-preview-frequently-asked-questions"></a>Vanliga frågor och svar om Azure Synapse Analytics (förhandsgranska arbets yta)

I den här guiden hittar du de vanligaste frågorna för Synapse-analys.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Allmänt

### <a name="q-what-is-azure-synapse-analytics"></a>F: Vad är Azure Synapse Analytics

A: Azure Synapse är en integrerad data plattform för BI, AI och kontinuerlig intelligens. Den ansluter olika analys körningar som SQL och Spark genom en enda plattform som ger ett enhetligt sätt att:

- Skydda dina analys resurser, inklusive nätverk, hantering av enkel inloggning för att få åtkomst till pooler, data och utvecklings artefakter.
- Du kan enkelt övervaka och snabbt optimera, reagera och felsöka händelser i dina arbets ytans aktiviteter i valfritt lager.
- Hantera dina metadata i olika motorer. Skapa en spark-tabell så blir den automatiskt tillgänglig i dina Azure Synapse-databaser.
- Interagera med data via en enhetlig användar upplevelse. Synapse Studio ger stor data utvecklare, data tekniker, databas administratörer, data analytiker och data experter på samma plattform.

### <a name="q-how-do-i-get-started-with-azure-synapse-analytics"></a>F: Hur gör jag för att kom igång med Azure Synapse Analytics

A: om du vill börja använda Azure Synapse Analytics [registrerar du Azure Synapse Resource Provider](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types) genom att välja *Microsoft. Synapse* i listan över resurs leverantörer. Skapa sedan en [Synapse-arbetsyta](https://portal.azure.com) (den är gratis!) och skapa de resurser som du vill ha under den arbets ytan. Du kan följa en av våra snabb starts guider, till exempel [skapa en SYNAPSE SQL-pool](quickstart-create-sql-pool-portal.md) eller [skapa en arbets yta](quickstart-create-workspace.md), som vägleder dig genom ett enkelt användnings fall. Du kan också hitta exempel på bärbara datorer och SQL-skript i vår [lagrings plats](https://github.com/Azure-Samples/Synapse). Om du behöver ansluta till en offentlig data uppsättning skapar du en ny länkad tjänst med följande attribut:

- azure_storage_account_name = "azureopendatastorage"
- azure_storage_sas_token = "" (Skriv **""**)

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>F: vilka är huvud komponenterna i Azure Synapse Analytics

A: Azure Synapse har följande funktioner:

- Analys funktioner erbjuds via SQL-pool eller SQL på begäran (för hands version) (utan server).
- Apache Spark pool (för hands version) med fullständigt stöd för Scala, python, SparkSQL och C #
- Data flöde erbjuder en kod fri transformering av Big data Transformation
- Data integrering & Orchestration för att integrera dina data och operationalisera all kod utveckling
- Studio för att få åtkomst till alla dessa funktioner via ett enda webb gränssnitt

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>F: Hur relaterar Azure Synapse Analytics till Azure SQL Data Warehouse

A: Azure Synapse Analytics är en utveckling av Azure SQL Data Warehouse i en analys plattform, som innehåller SQL-pool som data lager lösning. Den här plattformen kombinerar data utforskning, inmatning, omvandling, förberedelse och ett betjänande analys lager.

## <a name="use-cases"></a>Användningsfall

### <a name="q-what-is-a-good-use-case-for-synapse-sql-pool"></a>F: Vad är ett bra användnings fall för Synapse SQL-pool

A: SQL-poolen är hjärtat i ditt informations lager behov. Det är den ledande informations lager lösningen i [pris/prestanda](https://azure.microsoft.com/services/sql-data-warehouse/compare/). SQL-poolen är den branschledande moln informations lager lösningen eftersom du kan:

- hantera en stor och blandad mängd arbets belastningar utan påverkan på prestanda tack vare hög samtidighet och arbets belastnings isolering
- skydda dina data enkelt genom avancerade funktioner som sträcker sig från nätverks säkerhet till detaljerad åtkomst
- Dra nytta av ett brett spektrum av eko system

### <a name="q-what-is-a-good-use-case-for-spark-in-synapse"></a>F: Vad är ett bra användnings fall för Spark i Synapse

A: vårt första mål är att tillhandahålla en bra data teknik för att transformera data över sjön i batch eller Stream. Den nära och enkla integrationen med vår datadirigering gör driftsättning av utvecklings arbetet enkelt.

Ett annat användnings fall för Spark är att en data expert:

- extrahera en funktion
- utforska data
- träna en modell

### <a name="q-what-is-a-good-use-case-for-sql-on-demand-in-synapse"></a>F: Vad är ett bra användnings fall för SQL på begäran i Synapse

A: SQL på begäran är en fråga till tjänsten över data i data Lake. Det ger dig möjlighet att demokratisera identifieringen av åtkomst till alla dina data genom att tillhandahålla en välbekant T-SQL-syntax för att skicka frågor till data på plats, utan att behöva kopiera eller läsa in data i ett specialiserat lager.

Exempel på användnings exempel är följande:

- grundläggande identifiering och utforskning – tillhandahåller dataanalytiker, nya data forskare och data tekniker med en enkel väg till insikter om data boende i data Lake med schema för Läs-T-SQL-frågor
- det logiska informations lagret – data analytiker kan köra hela Expressiveness för T-SQL-språk för att direkt fråga och analysera data som finns i Azure Storage och använda välkända BI-verktyg (t. ex. Azures analys tjänster, Power BI Premium osv.) för att uppdatera instrument paneler genom att köra frågor om Starlight frågor
- "enskild fråga" ETL – gör det möjligt för data tekniker att transformera Azure Storage baserade data från ett format till ett annat, filter, agg regering osv. i massivt parallell bearbetnings sätt, spara frågeresultat för att Azure Storage och göra dem omedelbart tillgängliga för vidare bearbetning i Starlight-fråga eller andra tjänster av intresse

### <a name="q-what-is-a-good-use-case-for-data-flow-in-synapse"></a>F: Vad är ett bra användnings fall för data flödet i Synapse

A: data flödet gör det möjligt för data tekniker att utveckla grafisk data omvandlings logik utan att skriva kod. De resulterande data flödena körs som aktiviteter i data integration & Orchestration. Data flödes aktiviteter kan användas via befintliga funktioner för schemaläggning, kontroll, flöde och övervakning.

## <a name="security-and-access"></a>Säkerhet och åtkomst

A: slut punkt till slut punkt för enkel inloggning är en viktig autentiseringsprocess i Synapse Analytics. Att hantera och skicka identiteten via via en fullständig AAD-integrering måste vara en.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adls-gen2"></a>F: Hur gör jag för att få åtkomst till filer och mappar i ADLS Gen2

A: åtkomst till filer och mappar hanteras för närvarande via ADLS Gen2. Mer information finns i [data Lake Storage Access Control](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>F: kan jag använda Business Intelligence-verktyg från tredje part för att få åtkomst till Azure Synapse Analytics

A: Ja, du kan använda företags program från tredje part, t. ex. Tableau och Power BI, för att ansluta till SQL-poolen och SQL på begäran. Spark stöder IntelliJ.

## <a name="next-steps"></a>Nästa steg

- [Skapa en arbetsyta](quickstart-create-workspace.md)
- [Använda Synapse Studio](quickstart-synapse-studio.md)
- [Skapa en SQL-pool](quickstart-create-sql-pool-portal.md)
- [Använda SQL på begäran](quickstart-sql-on-demand.md)
- [Skapa en Apache Spark pool](quickstart-create-apache-spark-pool-portal.md) 
