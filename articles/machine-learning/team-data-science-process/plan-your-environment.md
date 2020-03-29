---
title: Identifiera scenarier och planera analysprocessen - Team Data Science Process | Azure Machine Learning
description: Identifiera scenarier och planera för avancerad databehandling av analys genom att överväga en rad viktiga frågor.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b0b811a2b7ed432b7fc5015886b28337ca33424e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710320"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Så här identifierar du scenarier och planerar för avancerade analyser vid databearbetning

Vilka resurser krävs för att du ska kunna skapa en miljö som kan utföra avancerad analysbearbetning på en datauppsättning? I den här artikeln föreslås en rad frågor att ställa som kan hjälpa dig att identifiera aktiviteter och resurser som är relevanta för ditt scenario.

Mer information om ordningen på högnivåsteg för prediktiv analys finns i [Vad är Team Data Science Process (TDSP)](overview.md). Varje steg kräver specifika resurser för de aktiviteter som är relevanta för just ditt scenario.

Svara på viktiga frågor inom följande områden för att identifiera ditt scenario:

* datalogistik
* dataegenskaper
* datauppsättning kvalitet
* önskade verktyg och språk

## <a name="logistic-questions-data-locations-and-movement"></a>Logistiska frågor: dataplatser och förflyttning

De logistiska frågorna omfattar följande punkter:

* plats för datakälla
* målmål i Azure
* krav för att flytta data, inklusive tidsplan, belopp och resurser som

Du kan behöva flytta data flera gånger under analysprocessen. Ett vanligt scenario är att flytta lokala data till någon form av lagring på Azure och sedan till Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Vad är din datakälla?

Är dina data lokala eller i molnet? Möjliga platser inkluderar:

* en offentligt tillgänglig HTTP-adress
* en lokal eller nätverksfil plats
* en SQL Server-databas
* en Azure Storage-behållare

### <a name="what-is-the-azure-destination"></a>Vad är Azure-målet?

Var behöver dina data vara för bearbetning eller modellering? 

* Azure Blob Storage
* SQL Azure-databaser
* SQL Server på virtuella Azure-datorer
* HDInsight (Hadoop på Azure) eller Hive-tabeller
* Azure Machine Learning
* Montera virtuella Azure-hårddiskar

### <a name="how-are-you-going-to-move-the-data"></a>Hur ska du flytta data?

Förfaranden och resurser för att få in eller läsa in data i en mängd olika lagrings- och bearbetningsmiljöer finns i:

* [Läs in data i lagringsmiljöer för analys](ingest-data.md)
* [Importera dina träningsdata till Azure Machine Learning Studio (klassisk) från olika datakällor](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Måste data flyttas enligt ett regelbundet schema eller ändras under migreringen?

Överväg att använda Azure Data Factory (ADF) när data måste migreras kontinuerligt. ADF kan vara till hjälp för:

* ett hybridscenario som involverar både lokala resurser och molnresurser
* ett scenario där data överförs, ändras eller ändras av affärslogik under migrerande

Mer information finns i [Flytta data från en lokal SQL-server till SQL Azure med Azure Data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Hur mycket av data ska flyttas till Azure?

Stora datamängder kan överskrida lagringskapaciteten i vissa miljöer. En exempelvis se diskussionen om storleksbegränsningar för Machine Learning Studio (klassisk) i nästa avsnitt. I sådana fall kan du använda ett urval av data under analysen. Mer information om hur du nedextpelser en datauppsättning i olika Azure-miljöer finns [i Exempeldata i Team Data Science Process](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Frågor om dataegenskaper: typ, format och storlek

Dessa frågor är nyckeln till att planera dina lagrings- och bearbetningsmiljöer. De hjälper dig att välja lämpligt scenario för din datatyp och förstå eventuella begränsningar.

### <a name="what-are-the-data-types"></a>Vilka är datatyperna?

* Numeriska
* Kategoriska
* Strängar
* Binär

### <a name="how-is-your-data-formatted"></a>Hur formateras dina data?

* Comma-separerade (CSV) eller flikavgränsade (TSV) platta filer
* Komprimerad eller okomprimerad
* Azure-blobbar
* Hadoop Hive tabeller
* SQL Server-tabeller

### <a name="how-large-is-your-data"></a>Hur stora är dina data?

* Liten: Mindre än 2 GB
* Medium: Större än 2 GB och mindre än 10 GB
* Stor: Större än 10 GB

Ta azure machine learning studio (klassisk) miljö till exempel:

* En lista över de dataformat och typer som stöds av Azure Machine Learning Studio finns i avsnittet [Dataformat och datatyper.](../studio/import-data.md#supported-data-formats-and-data-types)
* Information om begränsningar för andra Azure-tjänster som används i analysprocessen finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Frågor om datakvalitet: prospektering och förbehandling

### <a name="what-do-you-know-about-your-data"></a>Vad vet du om dina data?

Förstå de grundläggande egenskaperna för dina data:

* Vilka mönster eller trender den uppvisar
* Vilka extremvärden den har
* Hur många värden saknas

Det här steget är viktigt för att hjälpa dig:

* Bestäm hur mycket förbehandling som behövs
* Formulera hypoteser som föreslår de lämpligaste funktionerna eller typen av analys
* Formulera planer för ytterligare datainsamling

Användbara tekniker för datainspektion inkluderar beskrivande statistik beräkning och visualisering diagram. Mer information om hur du utforskar en datauppsättning i olika Azure-miljöer finns [i Utforska data i Team Data Science Process](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Kräver uppgifterna förbehandling eller rengöring?

Du kan behöva förbehandla och rensa data innan du kan använda datauppsättningen effektivt för maskininlärning. Rådata är ofta bullriga och opålitliga. Det kan saknas värden. Att använda sådana data för modellering kan ge vilseledande resultat. En beskrivning finns i [Uppgifter för att förbereda data för förbättrad maskininlärning](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Frågor om verktyg och språk

Det finns många alternativ för språk, utvecklingsmiljöer och verktyg. Var medveten om dina behov och preferenser.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Vilka språk föredrar du att använda för analys?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Vilka verktyg ska du använda för dataanalys?

* [Microsoft Azure Powershell](/powershell/azure/overview) – ett skriptspråk som används för att administrera dina Azure-resurser på ett skriptspråk
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [Rstudio](https://www.rstudio.com)
* [Python Tools för Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Jupyter Notebook](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identifiera ditt avancerade analysscenario

När du har svarat på frågorna i föregående avsnitt är du redo att avgöra vilket scenario som bäst passar ditt ärende. Exempelscenarierna beskrivs i [Scenarier för avancerad analys i Azure Machine Learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Vad är TDSP (Team Data Science Process)?](overview.md)
