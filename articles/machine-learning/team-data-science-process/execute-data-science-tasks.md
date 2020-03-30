---
title: Utföra datavetenskapsuppgifter - Team Data Science Process
description: Hur en datavetare kan utföra ett datavetenskapsprojekt på ett spårbart, versionskontrollerat och samarbetsinriktat sätt.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e69a03cd142fdbcc5864ee38a4843e1c2e44a124
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477161"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Utföra datavetenskapsuppgifter: utforskning, modellering och distribution

Typiska datascience-uppgifter är datautforskning, modellering och distribution. Den här artikeln visar hur du använder verktygen **För interaktiv datautforskning, analys och rapportering (IDEAR)** och **AMAR (Automated Modeling and Reporting)** för att slutföra flera vanliga datavetenskapsuppgifter, till exempel interaktiv datautforskning, dataanalys, rapportering och skapande av modeller. Alternativ för att distribuera en modell i en produktionsmiljö kan omfatta:

- [Azure Machine Learning](../index.yml)
- [SQL-Server med ML-tjänster](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> Utforskning 

En datavetare kan utföra utforskning och rapportering på olika sätt: genom att använda bibliotek och paket som är tillgängliga för Python (matplotlib till exempel) eller med R (ggplot eller gitter till exempel). Dataexperter kan anpassa sådan kod för att passa behoven hos datautforskning för specifika scenarier. Behovet av att hantera strukturerade data är olika för ostrukturerade data som text eller bilder. 

Produkter som Azure Machine Learning ger också [avancerade dataförberedelser](../how-to-create-register-datasets.md) för datakäbbel och utforskning, inklusive att skapa funktioner. Användaren bör bestämma vilka verktyg, bibliotek och paket som bäst passar deras behov. 

Slutprodukten i slutet av denna fas är en dataprospekteringsrapport. Rapporten bör ge en ganska heltäckande bild av de uppgifter som ska användas för modellering och en bedömning av om uppgifterna är lämpliga för att gå vidare till modelleringssteget. Verktygen för Team Data Science Process (TDSP) som beskrivs i följande avsnitt för halvautomatisk utforskning, modellering och rapportering tillhandahåller också standardiserade dataprospekterings- och modelleringsrapporter. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interaktiv datautforskning, analys och rapportering med hjälp av IDEAR-verktyget

Det här R-markdown-baserade eller Python-verktyget för bärbara datorer är ett flexibelt och interaktivt verktyg för att utvärdera och utforska datauppsättningar. Användare kan snabbt generera rapporter från datauppsättningen med minimal kodning. Användare kan klicka på knappar för att exportera prospekteringsresultaten i det interaktiva verktyget till en slutrapport, som kan levereras till kunder eller användas för att fatta beslut om vilka variabler som ska inkluderas i det efterföljande modelleringssteget.

För närvarande fungerar verktyget bara på dataramar i minnet. En YAML-fil behövs för att ange parametrarna för den datauppsättning som ska utforskas. Mer information finns i [IDEAR i TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> Modellering

Det finns många verktygslådor och paket för träningsmodeller på en mängd olika språk. Datavetare bör känna sig fria att använda vilka någonsin de är bekväma med, så länge prestanda överväganden om noggrannhet och latens är uppfyllda för relevanta affärsanvändning fall och scenarier produktion.

Nästa avsnitt visar hur du använder ett R-baserat TDSP-verktyg för halvautomatisk modellering. Detta AMAR-verktyg kan användas för att generera baslinjemodeller snabbt samt de parametrar som måste justeras för att ge en modell med bättre prestanda.
Följande modellhanteringsavsnitt visar hur du har ett system för registrering och hantering av flera modeller.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Modellutbildning: modellering och rapportering med hjälp av AMAR-verktyget

[Verktyget För automatisk modellering och rapportering (AMAR)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) tillhandahåller ett anpassningsbart, halvautomatiskt verktyg för att skapa modeller med hyperparametersvepning och jämföra noggrannheten hos dessa modeller. 

Verktyget för att skapa modeller är en R Markdown-fil som kan köras för att producera fristående HTML-utdata med en innehållsförteckning för enkel navigering genom dess olika avsnitt. Tre algoritmer körs när Markdown-filen körs (stickas): reglerad regression med glmnet-paketet, slumpmässig skog med hjälp av randomForest-paketet och öka träd med hjälp av xgboost-paketet). Var och en av dessa algoritmer producerar en utbildad modell. Noggrannheten hos dessa modeller jämförs sedan och de relativa funktionsviktsdiagrammen rapporteras. För närvarande finns det två verktyg: en är för en binär klassificeringsuppgift och en är för en regressionsaktivitet. De primära skillnaderna mellan dem är hur kontrollparametrar och noggrannhetsmått anges för dessa inlärningsuppgifter. 

En YAML-fil används för att ange:

- datainmatningen (en SQL-källa eller en R-Data-fil) 
- vilken del av data som används för utbildning och vilken del för testning
- vilka algoritmer som ska köras 
- valet av kontrollparametrar för modelloptimering:
    - korsvalidering 
    - Bootstrapping
    - veck av korsvalidering
- hyperparameteruppsättningarna för varje algoritm. 

Antalet algoritmer, antalet veck för optimering, hyperparametrar och antalet hyperparameteruppsättningar som ska svepas över kan också ändras i Yaml-filen för att köra modellerna snabbt. De kan till exempel köras med ett lägre antal CV-veck, ett lägre antal parameteruppsättningar. Om det är motiverat kan de också köras mer omfattande med ett högre antal CV-veck eller ett större antal parameteruppsättningar.

Mer information finns i [Verktyget för automatiserad modellering och rapportering i TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Modellhantering
När flera modeller har byggts måste du vanligtvis ha ett system för att registrera och hantera modellerna. Vanligtvis behöver du en kombination av skript eller API:er och en backend-databas eller versionshanteringssystem. Några alternativ som du kan tänka på för dessa hanteringsuppgifter är:

1. [Azure Machine Learning – tjänst för modellhantering](../index.yml)
2. [ModelDB från MIT](http://modeldb.csail.mit.edu:3000/projects) 
3. [SQL-server som modellhanteringssystem](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a>

Produktionsdistribution gör det möjligt för en modell att spela en aktiv roll i ett företag. Förutsägelser från en distribuerad modell kan användas för affärsbeslut.

### <a name="production-platforms"></a>Produktionsplattformar
Det finns olika metoder och plattformar för att sätta modeller i produktion. Här är några alternativ:


- [Modelldistribution i Azure Machine Learning](../how-to-deploy-and-where.md)
- [Distribution av en modell i SQL-server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Före distributionen måste man försäkra att svarstiden för modellbedömning är tillräckligt låg för att användas i produktionen.
>
>

Ytterligare exempel finns i genomgångar som visar alla steg i processen för **specifika scenarier**. De visas och länkas med miniatyrbeskrivningar i artikeln [Exempelgenomgångar.](walkthroughs.md) De illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbetsflöde eller en pipeline för att skapa ett intelligent program.

> [!NOTE]
> För distribution med Azure Machine Learning Studio finns i [Distribuera en Azure Machine Learning-webbtjänst](../studio/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>A/B-testning
När flera modeller är i produktion kan det vara användbart att utföra [A/B-testning](https://en.wikipedia.org/wiki/A/B_testing) för att jämföra modellernas prestanda. 

 
## <a name="next-steps"></a>Nästa steg

[Spåra utvecklingen av datavetenskapsprojekt](track-progress.md) visar hur en datavetare kan spåra utvecklingen av ett datavetenskapligt projekt.

[Modelldrift och CI/CD](ci-cd-flask.md) visar hur CI/CD kan utföras med utvecklade modeller.


