---
title: Identifiera scenarier och planera din analytics-process - Azure | Microsoft Docs
description: "Planera för avancerade analyser av ett antal viktiga frågor."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 421520dd-7728-4d29-889c-ebe6a0a6fb07
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev
ms.openlocfilehash: f4cf702b899b285b18c09d7a5951589d2ae71b7d
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Så här identifierar du scenarier och planerar för avancerade analyser vid databearbetning
Vilka resurser bör du planerar att när du konfigurerar en miljö för att göra avancerade analyser bearbetning på en datamängd? Den här artikeln tyder på ett antal frågor som hjälper identifiera de uppgifter och resurser som är relevanta ditt scenario. Anvisningar för förutsägelseanalys beskrivs i [vad är Team Data vetenskap processen (TDSP)?](overview.md). Var och en av dessa steg kräver särskilda resurser för uppgifterna som är relevanta för din specifika situation. Viktiga frågor för att identifiera ditt scenario omfatta data logistik, egenskaper, kvaliteten på datauppsättningar, och de verktyg och språk som du föredrar att göra analys.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Logistic frågor: data platser och flytt
Logistic frågor rör platsen för den **datakällan**, **målområdet** i Azure och krav för att flytta data, inklusive schema, belopp och resurser som ingår. Data kan behöva flyttas flera gånger under processen analytics. Ett vanligt scenario är att flytta lokala data i någon form av lagring på Azure och sedan till Machine Learning Studio.

1. **Vad är din datakälla?** Är den lokala eller i molnet? Exempel:
   
   * Data är offentligt tillgänglig på en HTTP-adress.
   * Data som finns på en lokal fil/nätverksplats.
   * Data är i en SQL Server-databas.
   * Data lagras i en Azure storage-behållare
2. **Vad är Azure målet?** Där behöver vara under bearbetning eller modeling? Exempel:
   
   * Azure Blob Storage
   * SQL Azure-databaser
   * SQL Server på virtuella Azure-datorer
   * HDInsight (Hadoop på Azure) eller Hive-tabeller
   * Azure Machine Learning
   * Monteras Azure virtuella hårddiskar.
3. **Hur ska du flytta data?** Metoderna och tillgängliga resurser för att mata in eller läsa in data i en mängd olika lagringsplatser och standardmiljöer för bearbetning beskrivs i följande artiklar:
   
   * [Läs in data till lagring miljöer för analys](ingest-data.md)
   * [Importera utbildningsdata till Azure Machine Learning Studio från olika datakällor](../studio/import-data.md).
4. **Data måste flyttas enligt ett schema eller ändras under migreringen?** Överväg att använda Azure Data Factory (ADM) när data ska migreras kontinuerligt, särskilt om ett hybridscenario som har åtkomst till både lokalt och molnresurser ingår, eller när data är överförd eller måste ändras eller har affärslogik som lagts till den under migreras. Mer information finns i [flytta data från en lokal SQLServer till SQL Azure med Azure Data Factory](move-sql-azure-adf.md)
5. **Hur mycket data som flyttas till Azure?** Datauppsättningar som är mycket stor kan överskrida lagringskapaciteten för vissa miljöer. Ett exempel finns i beskrivning av storleksgränser för Machine Learning Studio i nästa avsnitt. I sådana fall kan en exempeldata användas under analysen. Mer information om hur du ned-sample en datamängd i olika Azure-miljöer finns [exempeldata i Team av vetenskapliga data](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Data egenskaper frågor: typ, format och storlek
Dessa frågor är nyckeln till att planera din lagring och bearbetning av miljöer som är lämpliga för olika typer av data och som har vissa begränsningar.

1. **Vilka är datatyperna?** Exempel:
   
   * Numeriskt
   * Kategoriska
   * Strängar
   * Binär
2. **Hur formateras dina data?** Exempel:
   
   * Kommaavgränsad (CSV) eller tabbavgränsade (TVS) flata filer
   * Komprimerad eller okomprimerad
   * Azure BLOB
   * Hadoop Hive-tabeller
   * SQL Server-tabeller
3. **Hur stor är dina data?**
   
   * Liten: mindre än 2 GB
   * Medel: Större än 2 GB och mindre än 10 GB
   * Stora: Större än 10 GB

Ta till exempel Azure Machine Learning Studio-miljön:

* En lista över dataformat och typer som stöds av Azure Machine Learning Studio finns [dataformat och datatyper som stöds](../studio/import-data.md#data-formats-and-data-types-supported) avsnitt.
* Mer information om begränsningar för Azure Machine Learning Studio finns i **hur stor kan datauppsättningen vara för Mina moduler?** avsnitt i [importera och exportera data för Machine Learning](../studio/faq.md#machine-learning-studio-questions)

Mer information om begränsningar för andra Azure-tjänster används i analytics finns [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Data quality frågor: undersökning och föregående bearbetning
1. **Vad kan du om dina data?** Utforska data för att få en förstå av dess grundläggande egenskaper. Vilka mönster eller trender som den ger saknas vilka avvikare har eller hur många värden som. Det här steget är viktigt för att fastställa omfattningen av föregående bearbetning som krävs för utformningen av hypoteser som kan föreslå lämpligaste funktioner eller typ av analys och utforma planer för ytterligare data samlas. Beräkna beskrivande statistik och rita visualiseringar är värdefulla metoder för kontroll av data. Mer information om hur du utforskar en datamängd i olika Azure-miljöer finns [utforska data i Team av vetenskapliga data](explore-data.md).
2. **Kräver data förbearbetning eller rensa?**
   Förbearbetning och rensa data är viktiga uppgifter som vanligtvis utföras innan dataset kan användas effektivt för machine learning. Rådata är ofta mycket brus och otillförlitliga och saknar värden. Med hjälp av dessa data för modellering kan ge missvisande resultat. En beskrivning finns [uppgifter du förbereder data för förbättrad maskininlärning](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Verktyg och språk-frågor
Det finns många alternativ beroende på vilka språk och utvecklingsmiljöer eller verktyg du behöver eller mest conformable använder.

1. **Vilka språk du vill använda för analys?**  
   
   * R
   * Python
   * SQL
2. **Vilka verktyg för dataanalys ska du använda?**
   
   * [Microsoft Azure Powershell](/powershell/azure/overview) -ett skriptspråk som används för att administrera dina Azure-resurser i ett skriptspråk.
   * [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
   * [Revolution Analytics](http://www.revolutionanalytics.com/revolution-r-open)
   * [RStudio](http://www.rstudio.com)
   * [Python Tools för Visual Studio](http://microsoft.github.io/PTVS/)
   * [Anaconda](https://www.continuum.io/why-anaconda)
   * [Jupyter-anteckningsböcker](http://jupyter.org/)
   * [Microsoft Power BI](http://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identifiera avancerade analyser-scenario
När du har besvarat frågorna i föregående avsnitt, är du redo att avgöra vilket scenario som bäst passar ditt ärende. Exemplen beskrivs i [scenarier för avancerade analyser i Azure Machine Learning](plan-sample-scenarios.md).

