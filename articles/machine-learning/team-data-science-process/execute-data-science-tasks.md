---
title: Köra datavetenskapsaktiviteter - Team Data Science Process
description: Hur datavetare kan köra ett datavetenskapsprojekt i en spårningsbar version kontrolleras och samarbetsfunktioner sätt.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 984b03288b8dae644fc04a2cd78fb03a2e027f62
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722211"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Köra datavetenskapsaktiviteter: utforskning, modellering och distribution

Vanliga datavetenskapsuppgifter inkludera datautforskning, modellering och distribution. Den här artikeln visar hur du använder verktygen för **interaktiva data utforsknings-, analys-och rapporterings** -och **Amar-** verktyg för att utföra flera gemensamma data vetenskaps uppgifter, till exempel interaktiva data utforskning, data analys, rapportering och skapande av modeller. Alternativ för att distribuera en modell i en produktions miljö kan vara:

- [Azure Machine Learning](../index.yml)
- [SQL-Server med ML-tjänster](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> utforskning 

Dataexpert kan utföra utforskning och rapportering i en mängd olika sätt: med hjälp av bibliotek och paket som är tillgängliga för Python (matplotlib till exempel) eller med R (ggplot eller gitter till exempel). Dataexperter kan anpassa sådan kod så att de passar behoven för datagranskning för specifika scenarier. Behov för att hantera strukturerade data är olika för Ostrukturerade data, till exempel text eller bilder. 

Produkter som Azure Machine Learning även tillhandahålla [Avancerad data förberedelse](../how-to-create-register-datasets.md) för data datatransformering och utforskning, inklusive att skapa funktioner. Användaren bör de verktyg, bibliotek och paket som bäst suite bestämma deras behov. 

Leverans i slutet av den här fasen är en rapport för utforskning av data. Rapporten bör ge en ganska omfattande vy över data som ska användas för modellering och en utvärdering av om data är lämpligt att gå vidare till steg modellering. Team Data Science Process TDSP ()-verktyg som beskrivs i följande avsnitt för halvautomatiserat utforskning, modellering och rapportering dessutom standardiserade datagranskning och modellering rapporter. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interaktiv datagranskning, analys och rapportering med hjälp av verktyget IDEAR

Den här markdown-baserade R eller Python notebook-baserade verktyg ger ett flexibelt och interaktiva verktyg för att utvärdera och utforska datauppsättningar. Användarna kan snabbt generera rapporter från datauppsättningen med minimal kodning. Användarna kan klicka på knappar för att exportera resultaten i verktyget interaktiv utforskning till slutrapporten som kan levereras till klienter eller används för att fatta beslut rörande vilka variabler som ska inkluderas i efterföljande modellering steg.

För tillfället fungerar verktyget bara på dataramar i minnet. En YAML-fil krävs för att ange parametrarna för datauppsättning att undersökas. Mer information finns [i idé i TDSP data science-verktyg](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a> modellering

Det finns många verktyg och -paket för utbildning-modeller i olika språk. Dataexperter bör passa på att använda vilket ever som de är nöjd med, så länge prestandaöverväganden om Precision och svarstiden är uppfyllda för relevanta företag använder fall och produktionsscenarier.

Nästa avsnitt visar hur du använder ett R-baserad TDSP-verktyg för halvautomatiserat modellering. Den här AMAR verktyg kan användas för att generera baslinjen modeller snabbt samt de parametrar som behöver justeras för att ge en bättre utför modell.
Modellen management nedan visar hur du har ett system för att registrera och hantera flera modeller.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Modellera utbildning: modellering och rapportering med hjälp av verktyget AMAR

[Verktyget automatiserad modellering och rapportering (Amar)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) ger ett anpassningsbart, inkonsekvent verktyg för att skapa modeller med rensning av Hyper-parameter och för att jämföra noggrannheten i dessa modeller. 

Verktyget Skapa modellen är en R Markdown-fil som kan köras för att skapa fristående HTML-utdata med en innehållsförteckning för enkel navigering via dess olika avsnitt. Tre algoritmer körs när den Markdown-fil körs (knit): reglerats regression med glmnet paketera slumpmässiga skog med hjälp av randomForest-paketet och öka träd med hjälp av xgboost-paketet). Var och en av dessa algoritmer producerar en tränad modell. Hur korrekt referensdatorns dessa modeller jämförs sedan och relativa funktionen vikten områden har rapporterats. Det finns två verktyg: en för en binär klassificering och en är för en regression aktivitet. De huvudsakliga skillnaderna mellan dem är kontrollparametrar sätt och noggrannhet mått har angetts för dessa learning-aktiviteter. 

En YAML-fil för att ange:

- indata (en SQL-datakälla eller en R-fil) 
- hur stor del av informationen som används för utbildning och vilken del för testning
- vilka algoritmer som ska köras 
- valet av kontrollparametrar för modellen optimering:
    - korsvalidering 
    - startprogram
    - vikningar för korsvalidering
- hyper-parametern anger för varje algoritm. 

Antalet algoritmer, antalet vikningar för optimering, hyper-parametrarna och antalet hyper parameteruppsättningar till svepvinkeln över kan också ändras i Yaml-filen kör modellerna snabbt. Exempelvis kan de köras med ett lägre antal KA vikningar, ett lägre antal parameteruppsättningar. Om den är korrekt, kan de även köra fler ett mer omfattande sätt med ett högre antal KA vikningar eller ett större antal parameteruppsättningar.

Mer information finns i [automatiserad modellering och rapporterings verktyg i TDSP data science-verktyg](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Modellhantering
När flera modeller har skapats, måste vanligtvis du ha ett system för att registrera och hantera modeller. Vanligtvis behöver du en kombination av skript eller API: er och ett serverdelsystem för databasen eller versionshantering. Ett par alternativ som du kan tänka på för dessa hanteringsaktiviteter finns:

1. [Azure Machine Learning-modell hanterings tjänst](../index.yml)
2. [ModelDB från MIT](https://mitdbg.github.io/modeldb/) 
3. [SQL-Server som ett modell hanterings system](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a> distribution

Produktionsdistribution gör det möjligt för en modell att spela upp en aktiv roll i ett företag. Förutsägelser från en distribuerad modell kan användas för bättre affärsbeslut.

### <a name="production-platforms"></a>Plattformar för produktion
Det finns olika metoder och plattformar för att använda modeller i produktion. Här följer några alternativ:


- [Modell distribution i Azure Machine Learning](../how-to-deploy-and-where.md)
- [Distribution av en modell i SQL-Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Ett måste försäkra dig om svarstiden för bedömning av modellen är låg ska användas i produktion före distributionen.
>
>

Fler exempel finns i genom gångar som demonstrerar alla steg i processen för **olika scenarier**. De visas och länkas med miniatyr beskrivningar i [exempel](walkthroughs.md) artikeln. De visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program.

> [!NOTE]
> Information om distribution med hjälp av Azure Machine Learning Studio finns i [distribuera en Azure Machine Learning-webbtjänst](../studio/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>A / B-testning
När flera modeller är i produktion kan det vara praktiskt att utföra [ett/B-test](https://en.wikipedia.org/wiki/A/B_testing) för att jämföra modellernas prestanda. 

 
## <a name="next-steps"></a>Nästa steg

[Spåra förloppet för data vetenskaps projekt](track-progress.md) visar hur en data expert kan spåra förloppet för ett data vetenskaps projekt.

[Modell åtgärden och CI/CD](ci-cd-flask.md) visar hur CI/CD kan utföras med utvecklade modeller.


