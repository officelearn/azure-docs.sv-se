---
title: Identifiera scenarier och planera analytics-process - Team Data Science Process | Azure Machine Learning
description: Identifiera scenarier och planerar för avancerade analyser vid databearbetning genom att fundera över ett antal viktiga frågor.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a8ca08d93992a6e1bfe8ae24b83354503154ef31
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416164"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Så här identifierar du scenarier och planerar för avancerade analyser vid databearbetning

Vilka resurser som krävs att skapa en miljö som kan utföra avancerad analysbehandling på en datauppsättning? Den här artikeln föreslår ett antal frågor som kan hjälpa dig att identifiera aktiviteter och resurser som är relevanta ditt scenario.

Läs om anvisningar för förutsägande analyser i [vad är Team Data Science Process (TDSP)](overview.md). Varje steg kräver specifika resurser för uppgifterna som är relevanta för din specifika scenario.

Svarar på viktiga frågor inom följande områden att identifiera ditt scenario:

* datalogistik
* dataegenskaper
* datauppsättningen kvalitet
* föredragna verktyg och språk

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Logistic frågor: dataplatserna och dataförflyttning

Logistic frågor omfattar följande:

* Datakällans plats
* målområdet i Azure
* krav för att flytta data, inklusive den schema och mängden resurser som är inblandade

Du kan behöva flytta data flera gånger under analytics-processen. Ett vanligt scenario är att flytta lokala data till någon form av lagring på Azure och sedan till Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Vad är din datakälla?

Är dina data lokalt eller i molnet? Det är möjligt platser:

* en offentligt tillgänglig HTTP-adress
* en lokal eller platsen för filen
* en SQL Server-databas
* en Azure storage-behållare

### <a name="what-is-the-azure-destination"></a>Vad är Azure-mål?

Där dina data behöver vara bearbetning eller modellering? 

* Azure Blob Storage
* SQL Azure-databaser
* SQL Server på virtuella Azure-datorer
* HDInsight (Hadoop på Azure) eller Hive-tabeller
* Azure Machine Learning
* Montera Azure virtuella hårddiskar

### <a name="how-are-you-going-to-move-the-data"></a>Hur ska du flytta data?

Procedurer och resurser för att mata in eller läsa in data i en mängd olika lagring och bearbetning av miljöer finns:

* [Läs in data i lagringsmiljöer för analys](ingest-data.md)
* [Importera dina utbildningsdata till Azure Machine Learning Studio från olika datakällor](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Behöver data flyttas enligt ett schema eller ändras under migreringen?

Överväg att använda Azure Data Factory (ADF) när data ska migreras kontinuerligt. ADF kan vara användbart för att:

* ett scenario med hybridanvändning som omfattar både lokalt och i molnresurser
* ett scenario där data överförda, ändras eller ändras av affärslogik under migreras

Mer information finns i [flytta data från en lokal SQLServer till SQL Azure med Azure Data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Hur mycket data som flyttas till Azure?

Mycket stora datamängder kan överskrida lagringskapaciteten för vissa miljöer. Exempelvis finns i avsnittet om storleksgränser för Machine Learning Studio i nästa avsnitt. I sådana fall kan du använda ett exempel på data under analysen. Mer information om hur du nedåtsampla en datauppsättning i olika Azure-miljöer finns i [exempeldata i Team Data Science Process](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Egenskaper för datafrågor: typ, format och storlek

Dessa frågor är att planera din lagring och bearbetning av miljöer. De hjälper dig att välja rätt scenario för din datatyp av och förstå några begränsningar.

### <a name="what-are-the-data-types"></a>Vilka är datatyperna som?

* Numeriskt
* Kategoriska
* Strängar
* Binär

### <a name="how-is-your-data-formatted"></a>Hur formateras dina data?

* .Csv-fil (CSV) eller tabbavgränsade (TSV) flata filer
* Komprimerad eller okomprimerad
* Azure-blobar
* Hadoop Hive-tabeller
* SQL Server-tabeller

### <a name="how-large-is-your-data"></a>Hur stora är dina data?

* Små: Mindre än 2 GB
* Medel: Större än 2 GB och mindre än 10 GB
* Stora: Större än 10 GB

Ta till exempel Azure Machine Learning Studio-miljön:

* En lista över dataformat och typer som stöds av Azure Machine Learning Studio finns i [dataformat och datatyper som stöds](../studio/import-data.md#supported-data-formats-and-data-types) avsnittet.
* Information om begränsningarna i andra Azure-tjänster används i analytics-processen finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Data quality frågor: utforskning och bearbeta data i förväg

### <a name="what-do-you-know-about-your-data"></a>Vad vet du om dina data?

Förstå de grundläggande egenskaperna om dina data:

* Vad mönster eller trender det uppvisar
* Vilka extremvärden som den innehåller
* Hur många värden som saknas

Det här steget är viktigt att hjälpa dig att:

* Avgöra hur mycket förbearbetning krävs
* Formulera hypoteser som föreslår den mest lämpliga funktioner eller typen av analyser
* Formulera planer för insamling av ytterligare data

Bra metoder för granskning av data är beskrivande statistik beräkning och visualisering ritar. Mer information om hur du utforskar en datauppsättning i olika Azure-miljöer finns i [utforska data i Team Data Science Process](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Kräver data Förbearbeta eller rensning?

Du kan behöva Förbearbeta och rensa data innan du kan använda datauppsättningen effektivt för machine learning. Rådata är ofta bort störande och otillförlitliga. Det kanske saknar värden. Med hjälp av sådana data för modellering kan ge vilseledande resultat. En beskrivning finns i [uppgifter för att förbereda data för förbättrad machine learning](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Verktyg och språk-frågor

Det finns många alternativ för språk, utvecklingsmiljöer och verktyg. Tänk på dina behov och önskemål.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Vilka språk du vill använda för analys?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Vilka verktyg för analys av data ska du använda?

* [Microsoft Azure Powershell](/powershell/azure/overview) -ett skriptspråk som används för att administrera dina Azure-resurser i ett skriptspråk
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](http://www.rstudio.com)
* [Python Tools för Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Jupyter-anteckningsböcker](http://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identifiera ditt scenario för avancerad analys

När du har besvarat frågor i föregående avsnitt, är det dags att fastställa vilket scenario som bäst passar ditt ärende. Exempelscenarier beskrivs i [scenarier för avancerade analyser i Azure Machine Learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Vad är Team Data Science Process (TDSP)?](overview.md)