---
title: Köra uppgifter datavetenskap - Azure Machine Learning | Microsoft Docs
description: Hur en data-forskare kan köra ett datavetenskap projekt i en trackable version kontrollerade och samarbetsfunktioner sätt.
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev
ms.openlocfilehash: da09ab7daba115e570bb8b97549cc5f01e7496b6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Köra datavetenskap aktiviteter: utforskning, modellering och distribution

Vanliga vetenskap aktiviteter inkluderar datagranskning, modellering och distribution. Den här artikeln visar hur du använder den **interaktiv Datagranskning, analys och rapportering (IDEAR)** och **automatisk modellering och rapportering (AMAR)** verktyg för att slutföra flera gemensamma datavetenskap uppgifter till exempel interaktiv datagranskning, dataanalys, rapportering och skapa en modell. Här beskrivs också alternativ för distribution av en modell i en produktionsmiljö med hjälp av en mängd olika verktyg och data plattformar, till exempel följande:

- [Azure Machine Learning](../preview/index.yml)
- [SQL-Server med ML-tjänster](https://docs.microsoft.com/sql/advanced-analytics/r/r-services#in-database-analytics-with-sql-server)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> Undersökning 

En data-forskare kan utföra undersökning och rapportering i en mängd olika sätt: genom att använda bibliotek och paket som är tillgängliga för Python (matplotlib till exempel) eller med R (ggplot eller gitter till exempel). Datavetare kan anpassa sådan kod så att de passar bäst för datagranskning för specifika scenarier. Behov för att hantera strukturerade data är olika för Ostrukturerade data, till exempel text och bilder. 

Produkter som Azure Machine Learning arbetsstationen även ange [avancerade förberedelse av data](../preview/tutorial-bikeshare-dataprep.md) för data som wrangling och undersökning, inklusive funktionen skapas. Användaren ska fatta beslut om verktyg, bibliotek och paket som bäst suite deras behov. 

Leverans i slutet av den här fasen är en rapport för undersökning av data. Rapporten bör ge en ganska omfattande vy av data som ska användas för modellering och bedöma om data är lämpligt att gå vidare till steg modellering. Team Data vetenskap processen (TDSP)-verktyg som beskrivs i följande avsnitt för delvis automatiserad utforskning, modellering och rapportering dessutom standardiserade datagranskning och modellering rapporter. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interaktiv datagranskning, analys och rapportering med hjälp av verktyget IDEAR

Denna R markdown-baserade eller Python anteckningsboken-baserat verktyg ger ett flexibelt och interaktiva verktyg för att utvärdera och utforska datauppsättningar. Användarna kan snabbt skapa rapporter från datauppsättningen med minimal kodning. Användarna kan klicka på knappar för att exportera resultaten utforskning i verktyget interaktivt till en slutlig rapport som kan skickas till klienter eller används för att fatta beslut om vilka variabler som ska inkluderas i efterföljande modellering steg.

För tillfället fungerar verktyget bara på dataramar i minnet. En YAML-fil behövs för att ange parametrarna för de data som ska undersökas. Mer information finns i [IDEAR i TDSP datavetenskap verktyg](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a> Modeling

Det finns flera verktyg och paket för utbildning-modeller i en mängd olika språk. Datavetare bör passa på att använda som ever som de är nöjd med, så länge prestandaöverväganden om noggrannhet och svarstid uppfylls för relevanta företag använder fall och produktion scenarier.

I nästa avsnitt visar hur du använder ett R-baserade TDSP verktyg för delvis automatiserad modellering. Verktyget AMAR kan användas för att generera baslinjen modeller snabbt samt de parametrar som behöver justeras för att ge en bättre utför modell.
Modellen management nedan visar hur du har ett system för att registrera och hantera flera modeller.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Modellen utbildning: modellering och rapportering med hjälp av verktyget AMAR

Den [automatisk modellering och rapportering (AMAR)-verktyget](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) anpassningsbara, delvis automatiserad verktyg som utför modellen skapas med hyper-parametern omfattande och jämför dessa modeller noggrannhet. 

Verktyget Skapa modellen är en R Markdown-fil som kan köras för att skapa fristående HTML-utdata skapas med en innehållsförteckning för enkel navigering via dess olika avsnitt. Tre algoritmer utförs när Markdown-fil körs (knit): reglerats regression som använder glmnet paketet slumpmässiga skog randomForest paketera och förstärkning träd med xgboost-paket). Var och en av dessa algoritmer producerar en tränad modell. Riktighet modellers jämförs sedan och relativa funktionen vikten områden rapporteras. Det finns två verktyg: en är en binär klassificering aktivitet och en är en regression-aktivitet. De huvudsakliga skillnaderna mellan dem är kontrollparametrar sätt och noggrannhet mått har angetts för åtgärderna learning. 

En YAML-fil används för att ange:

- data-indata (en SQL-källa eller ett R-fil) 
- hur stor del av informationen som används för träning och vilken del för testning
- vilka algoritmer som ska köras 
- valet av kontrollparametrar för optimering av modellen:
    - korsvalidering 
    - startprogram
    - vikningar för korsvalidering
- hyper-parametern anger för varje algoritm. 

Antalet algoritmer, antalet vikningar för optimering, hyper-parametrarna och antalet hyper-parametern anger att Sopa över kan också ändras i Yaml-filen för att köra modeller snabbt. De kan till exempel köras med ett lägre antal KA vikningar, ett lägre antal parameteruppsättningar. Om den garanteras, kan de även köra mer utförligt med ett högre antal KA vikningar eller ett större antal parameteruppsättningar.

Mer information finns i [automatisk modellering och rapportering Utility i TDSP datavetenskap verktyg](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Modellhantering
När flera modeller har skapats, måste vanligtvis du ha ett system för att registrera och hantera modeller. Vanligtvis behöver du en kombination av skript eller API: er och backend-databas eller versionshantering systemet. Några alternativ som du kan tänka på för dessa hanteringsaktiviteter är:

1. [Azure Machine Learning - modellen management-tjänsten](../preview/index.yml)
2. [ModelDB från MIT](https://mitdbg.github.io/modeldb/) 
3. [SQL-seerver som ett system för hantering av modellen](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a> Distribution

Produktionsdistribution gör det möjligt för en modell att spela upp en aktiv roll i ett företag. Förutsägelser från en distribuerad modell kan användas för affärsbeslut.

### <a name="production-platforms"></a>Produktion plattformar
Det finns olika strategier och plattformar för att placera modeller i produktionen. Här följer några alternativ:


- [Distribution av modellen i Azure Machine Learning](../preview/model-management-overview.md)
- [Distribution av en modell i SQL server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>
>
>Obs: Före distributionen för en är att försäkra dig om svarstiden för modellen bedömningen är tillräckligt små för att använda i produktionen.
>

Ytterligare exempel finns i genomgång som visar alla steg i processen för **specifika scenarier**. De anges och är kopplad till miniatyr beskrivningar i den [exempel genomgång](walkthroughs.md) artikel. De visar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program.

Obs: Distribution med hjälp av Azure Machine Learning Studio finns [distribuera en Azure Machine Learning-webbtjänst](../studio/publish-a-machine-learning-web-service.md).

### <a name="ab-testing"></a>A / B-testning
När det finns flera modeller i produktion kan det vara bra att utföra [A / B-testning](https://en.wikipedia.org/wiki/A/B_testing) att jämföra prestanda av modeller. 

 
## <a name="next-steps"></a>Nästa steg

[Spåra förloppet för datavetenskap projekt](track-progress.md) visar hur en data-forskare kan spåra förloppet för ett projekt för vetenskapliga data.
 


