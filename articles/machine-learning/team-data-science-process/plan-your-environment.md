---
title: Identifiera scenarier och planera din analytics-process - Azure | Microsoft Docs
description: Planera för avancerad analys genom att fundera över ett antal viktiga frågor.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 421520dd-7728-4d29-889c-ebe6a0a6fb07
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 949bd8337ced7aa12d4354a46f6ee887a1922a7c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227747"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Så här identifierar du scenarier och planerar för avancerade analyser vid databearbetning
Vilka resurser bör du planera att inkludera när du konfigurerar en miljö för att göra avancerade analyser på en datauppsättning? Den här artikeln tyder på ett antal frågor som hjälper dig att identifiera aktiviteter och resurser som är relevanta ditt scenario. Anvisningar för förutsägande analyser beskrivs i [vad är Team Data Science Process (TDSP)?](overview.md). Var och en av de här stegen kräver specifika resurser för uppgifterna som är relevanta för din specifika scenario. Viktiga frågor för att identifiera ditt scenario avse datalogistik, egenskaper, kvaliteten på datauppsättningar, och de verktyg och språk du föredrar att göra analysen.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Logistic frågor: dataplatserna och dataförflyttning
Logistic frågor rör platsen för den **datakälla**, **målområdet** i Azure och krav för att flytta data, inklusive schema, belopp och resurser som ingår. Data kan behöva flyttas flera gånger under analytics-processen. Ett vanligt scenario är att flytta lokala data till någon form av lagring på Azure och sedan till Machine Learning Studio.

1. **Vad är din datakälla?** Är det lokalt eller i molnet? Exempel:
   
   * Data är offentligt tillgänglig på en HTTP-adress.
   * Data som finns i en lokal fil/nätverksplats.
   * Data är i en SQL Server-databas.
   * Data lagras i en Azure storage-behållare
2. **Vad är Azure-mål?** Där behöver den för bearbetning eller modellering? Exempel:
   
   * Azure Blob Storage
   * SQL Azure-databaser
   * SQL Server på virtuella Azure-datorer
   * HDInsight (Hadoop på Azure) eller Hive-tabeller
   * Azure Machine Learning
   * Montera Azure virtuella hårddiskar.
3. **Hur ska du flytta data?** Metoderna och resurser som är tillgängliga att mata in eller läsa in data i en mängd olika lagrings- och standardmiljöer för bearbetning av beskrivs i följande artiklar:
   
   * [Läs in data i lagringsmiljöer för analys](ingest-data.md)
   * [Importera dina utbildningsdata till Azure Machine Learning Studio från olika datakällor](../studio/import-data.md).
4. **Behöver data flyttas enligt ett schema eller ändras under migreringen?** Överväg att använda Azure Data Factory (ADF) när data ska migreras kontinuerligt, särskilt om ett hybridscenario som har åtkomst till både lokalt och molnresurser ingår eller när data är överförda eller behöver ändras eller ha affärslogik som lagts till den under migreras. Mer information finns i [flytta data från en lokal SQLServer till SQL Azure med Azure Data Factory](move-sql-azure-adf.md)
5. **Hur mycket data som flyttas till Azure?** Datauppsättningar som är mycket stor kan överskrida lagringskapaciteten för vissa miljöer. Exempelvis finns i avsnittet om storleksgränser för Machine Learning Studio i nästa avsnitt. I sådana fall kan ett exempel på data användas under analysen. Mer information om hur du nedåtsampla en datauppsättning i olika Azure-miljöer finns i [exempeldata i Team Data Science Process](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Egenskaper för datafrågor: typ, format och storlek
Dessa frågor är att planera din lagring och bearbetning av miljöer, som är lämpligt för olika typer av data och som har vissa begränsningar.

1. **Vilka är datatyperna som?** Till exempel:
   
   * Numeriskt
   * Kategoriska
   * Strängar
   * Binär
2. **Hur formateras dina data?** Till exempel:
   
   * .Csv-fil (CSV) eller tabbavgränsade (TSV) flata filer
   * Komprimerad eller okomprimerad
   * Azure-blobar
   * Hadoop Hive-tabeller
   * SQL Server-tabeller
3. **Hur stora är dina data?**
   
   * Små: mindre än 2 GB
   * Medel: Är större än 2 GB och mindre än 10 GB
   * Stora: Är större än 10 GB

Ta till exempel Azure Machine Learning Studio-miljön:

* En lista över dataformat och typer som stöds av Azure Machine Learning Studio finns i [dataformat och datatyper som stöds](../studio/import-data.md#data-formats-and-data-types-supported) avsnittet.
* Information om begränsningar för Azure Machine Learning Studio finns i den **hur stor kan datauppsättningen vara för Mina moduler?** delen av [importera och exportera data för Machine Learning](../studio/faq.md#machine-learning-studio-questions)

Information om begränsningarna i andra Azure-tjänster används i analytics-processen finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Data quality frågor: utforskning och bearbeta data i förväg
1. **Vad vet du om dina data?** Utforska data för att få en förstå av dess grundläggande egenskaper. Vilka mönster eller trender som den ger saknas vilka extremvärden som den har eller hur många värden. Det här steget är viktigt för att fastställa omfattningen av förbearbetning som krävs för utformningen av hypoteser som kan föreslå de mest lämpliga funktionerna eller typ av analys och utformningen av planer för insamling av ytterligare data. Beräkna beskrivande statistik och rita visualiseringar finns användbara metoder för granskning av data. Mer information om hur du utforskar en datauppsättning i olika Azure-miljöer finns i [utforska data i Team Data Science Process](explore-data.md).
2. **Kräver data bearbeta data i förväg eller rensning?**
   Bearbeta data i förväg och rensa data är viktiga uppgifter som normalt måste utföras innan datauppsättning kan användas effektivt för machine learning. Rådata är ofta bort störande och otillförlitliga och kan sakna värden. Med hjälp av sådana data för modellering kan ge vilseledande resultat. En beskrivning finns i [uppgifter för att förbereda data för förbättrad machine learning](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Verktyg och språk-frågor
Det finns många olika alternativ visas beroende på vilka språk och utvecklingsmiljöer eller verktyg du behöver eller är mest conformable använder.

1. **Vilka språk du vill använda för analys?**  
   
   * R
   * Python
   * SQL
2. **Vilka verktyg för analys av data ska du använda?**
   
   * [Microsoft Azure Powershell](/powershell/azure/overview) -ett skriptspråk som används för att administrera dina Azure-resurser i ett skriptspråk.
   * [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
   * [Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
   * [RStudio](http://www.rstudio.com)
   * [Python Tools för Visual Studio](https://aka.ms/ptvsdocs)
   * [Anaconda](https://www.continuum.io/why-anaconda)
   * [Jupyter-anteckningsböcker](http://jupyter.org/)
   * [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identifiera ditt scenario för avancerad analys
När du har besvarat frågor i föregående avsnitt, är du redo att avgöra vilket scenario som bäst passar ditt ärende. Exempelscenarier beskrivs i [scenarier för avancerade analyser i Azure Machine Learning](plan-sample-scenarios.md).

