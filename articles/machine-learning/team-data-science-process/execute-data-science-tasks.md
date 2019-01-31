---
title: Köra datavetenskapsaktiviteter - Team Data Science Process
description: Hur datavetare kan köra ett datavetenskapsprojekt i en spårningsbar version kontrolleras och samarbetsfunktioner sätt.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 51d76e16e93ccffc1a069e64808563d62f428dbf
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55476357"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Köra datavetenskapsaktiviteter: utforskning, modellering och distribution

Vanliga datavetenskapsuppgifter inkludera datautforskning, modellering och distribution. Den här artikeln visar hur du använder den **interaktiv Datagranskning, analys och rapportering (IDEAR)** och **automatiserad modellering och rapportering (AMAR)** verktyg för att slutföra flera vanliga datavetenskapsuppgifter till exempel interaktiv datagranskning, dataanalys, rapportering och skapa en modell. Här beskrivs också alternativ för att distribuera en modell till en produktionsmiljö med hjälp av en rad olika verktyg och data plattformar, till exempel följande:

- [Azure Machine Learning](../service/index.yml)
- [SQL-Server med ML-tjänster](https://docs.microsoft.com/sql/advanced-analytics/r/r-services#in-database-analytics-with-sql-server)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> Utforskning 

Dataexpert kan utföra utforskning och rapportering i en mängd olika sätt: med hjälp av bibliotek och paket som är tillgängliga för Python (matplotlib till exempel) eller med R (ggplot eller gitter till exempel). Dataexperter kan anpassa sådan kod så att de passar behoven för datagranskning för specifika scenarier. Behov för att hantera strukturerade data är olika för Ostrukturerade data, till exempel text eller bilder. 

Produkter som Azure Machine Learning-tjänsten tillhandahåller även [avancerad dataförberedelse](../service/how-to-transform-data.md) för data Datatransformering och undersökning, inklusive funktionen skapas. Användaren bör de verktyg, bibliotek och paket som bäst suite bestämma deras behov. 

Leverans i slutet av den här fasen är en rapport för utforskning av data. Rapporten bör ge en ganska omfattande vy över data som ska användas för modellering och en utvärdering av om data är lämpligt att gå vidare till steg modellering. Team Data Science Process TDSP ()-verktyg som beskrivs i följande avsnitt för halvautomatiserat utforskning, modellering och rapportering dessutom standardiserade datagranskning och modellering rapporter. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interaktiv datagranskning, analys och rapportering med hjälp av verktyget IDEAR

Den här markdown-baserade R eller Python notebook-baserade verktyg ger ett flexibelt och interaktiva verktyg för att utvärdera och utforska datauppsättningar. Användarna kan snabbt generera rapporter från datauppsättningen med minimal kodning. Användarna kan klicka på knappar för att exportera resultaten i verktyget interaktiv utforskning till slutrapporten som kan levereras till klienter eller används för att fatta beslut rörande vilka variabler som ska inkluderas i efterföljande modellering steg.

För tillfället fungerar verktyget bara på dataramar i minnet. En YAML-fil krävs för att ange parametrarna för datauppsättning att undersökas. Mer information finns i [IDEAR i TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a> Modellering

Det finns många verktyg och -paket för utbildning-modeller i olika språk. Dataexperter bör passa på att använda vilket ever som de är nöjd med, så länge prestandaöverväganden om Precision och svarstiden är uppfyllda för relevanta företag använder fall och produktionsscenarier.

Nästa avsnitt visar hur du använder ett R-baserad TDSP-verktyg för halvautomatiserat modellering. Den här AMAR verktyg kan användas för att generera baslinjen modeller snabbt samt de parametrar som behöver justeras för att ge en bättre utför modell.
Modellen management nedan visar hur du har ett system för att registrera och hantera flera modeller.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Modellera utbildning: modellering och rapportering med hjälp av verktyget AMAR

Den [automatiserad modellering och rapportering (AMAR)-verktyget](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) ger ett anpassningsbar, delvis automatiserad Verktyg att utföra modellskapandet med hyper-parametern omfattande och jämföra det arbete du utfört dessa modeller. 

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

Mer information finns i [automatiserad modellering och rapportering Utility i TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Modellhantering
När flera modeller har skapats, måste vanligtvis du ha ett system för att registrera och hantera modeller. Vanligtvis behöver du en kombination av skript eller API: er och ett serverdelsystem för databasen eller versionshantering. Ett par alternativ som du kan tänka på för dessa hanteringsaktiviteter finns:

1. [Azure Machine Learning - modell management-tjänsten](../service/index.yml)
2. [ModelDB från MIT](https://mitdbg.github.io/modeldb/) 
3. [SQL-server som ett hanteringssystem för modellen](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a> Distribution

Produktionsdistribution gör det möjligt för en modell att spela upp en aktiv roll i ett företag. Förutsägelser från en distribuerad modell kan användas för bättre affärsbeslut.

### <a name="production-platforms"></a>Plattformar för produktion
Det finns olika metoder och plattformar för att använda modeller i produktion. Här följer några alternativ:


- [Distribution av modeller i Azure Machine Learning-tjänsten](../service/how-to-deploy-and-where.md)
- [Distribution av en modell i SQL-server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Ett måste försäkra dig om svarstiden för bedömning av modellen är låg ska användas i produktion före distributionen.
>
>

Ytterligare exempel finns i genomgångar som visar alla steg i processen för **specifika scenarier**. De visas och som är kopplad till miniatyrbilder beskrivningarna i den [exempel genomgångar](walkthroughs.md) artikeln. De visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program.

> [!NOTE]
> Distribution med hjälp av Azure Machine Learning Studio finns i [distribuera en Azure Machine Learning-webbtjänst](../studio/publish-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>A / B-testning
När det finns flera modeller i produktion, kan det vara praktiskt att utföra [A / B-testning](https://en.wikipedia.org/wiki/A/B_testing) att jämföra prestanda för modellerna. 

 
## <a name="next-steps"></a>Nästa steg

[Spåra förloppet för dataforskningsprojekt](track-progress.md) visar hur datavetare kan följa förloppet för ett datavetenskapsprojekt.

[Modellera åtgärden och CI/CD](ci-cd-flask.md) visar hur du kan utföra CI/CD med utvecklade modeller.


