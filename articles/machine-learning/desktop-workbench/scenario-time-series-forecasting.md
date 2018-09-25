---
title: Energi begäran tidsserier prognoser | Microsoft Docs
description: Så här använder maskininlärning att skapa prognoser för energi begäran tidsserier i Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: ''
author: anta
manager: ireiter
editor: anta
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: anta
ROBOTS: NOINDEX
ms.openlocfilehash: 934d37783165c2e57dcabd0ff764747e1ab4b65e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946875"
---
# <a name="energy-demand-time-series-forecasting"></a>Energi begäran tidsserier prognoser

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 




Tidsserier är uppgiften att förutsäga framtida värden i en tidsordnad sekvens med observationer. Det är ett vanligt problem och om du har program inom många branscher. Detaljhandel företag behöver till exempel att skapa prognoser för framtida produktförsäljning så att de effektivt kan ordna sina leveranskedjor att möta efterfrågan. På samma sätt behöver paketet leverans företag att uppskatta behovet av sina tjänster så att de kan planera krav för anställda och leveransvägar förbereds i förväg. I många fall kan finansiella risker för felaktiga prognoser vara betydande. Prognoser är därför ofta en kritisk business-aktivitet.

Detta exempel visar hur tidsserier kan utföras genom att använda machine learning-teknik. Vägleds du genom varje steg på modellering processen, inklusive:
- Förberedelse av data att rensa och formatera data.
- Skapa funktioner för de machine learning-modeller från rådata tidsseriedata;
- Utbildning olika machine learning-modeller.
- Modellutvärdering genom att jämföra deras prestanda på en datauppsättning som lagras utanför test; och,
- Operationalisera modellen med bäst, vilket gör det tillgängligt via en webbtjänst för att skapa prognoser för efterfrågan.

Azure Machine Learning Workbench hjälper till med modelleringsprocessen vid varje steg: 
- Exemplet visar hur Jupyter notebook miljö - tillgängliga direkt från via Workbench - ser utveckla Python-koden enklare. Process för utveckling av modellen kan förklaras för andra som använder tydligare markdown anteckningar och diagram. Dessa anteckningsböcker kan visas, redigeras och körs direkt från Workbench.
- Tränade modeller kan sparas och överförts till blob storage. Detta hjälper dataexpert för att hålla reda på objekt utbildningsmodeller och se till att de är balanserade och hämtningsbara när det behövs.
- Mått kan loggas vid körning av ett Python-skript som aktiverar dataexpert för att ha koll på modellen prestanda poäng.
- Workbench producerar anpassningsbara tabeller loggade mått så att datavetare enkelt kan jämföra prestandamått för modellen. Diagram visas automatiskt så att den bästa modellen kan lätt identifieras.
- Slutligen visar exemplet hur du kan operationaliserat en tränad modell genom att distribuera den i en webbtjänst i realtid.

## <a name="link-to-the-gallery-github-repository"></a>Länka till galleriet GitHub-lagringsplatsen
Offentliga GitHub-lagringsplatsen för den här verkligt scenario innehåller allt material, inklusive kodexempel som behövs för det här exemplet:

[https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)


## <a name="use-case-overview"></a>Översikt över användningsfall

Det här scenariot fokuserar på efterfrågan på energi prognoser där målet är att förutsäga framtida belastningen på ett elnät. Det är en affärskritisk åtgärd för företag inom energisektorn som operatörer måste vara en bra balans mellan energi som förbrukas i ett rutnät och energi som angetts för den. För mycket ström som angetts för rutnätet kan leda till slöseri med energi eller tekniska fel. Men om för lite power anges kan leda till strömavbrott, lämna kunder utan power. Normalt kan grid operatörer ta kortsiktig beslut att hantera energitillförseln i rutnätet och Tänk saldo belastningen. En korrekt kortsiktig prognoser för efterfrågan på energi är därför viktigt för operatorn som ska ta de här besluten med tillförsikt.

Det här scenariot beskriver konstruktion av machine learning-lösning för prognostisering att efterfrågan på energi. Lösningen har tränats på en offentlig datauppsättning från den [New York oberoende System operatorn (NYISO)](http://www3.dps.ny.gov/W/PSCWeb.nsf/All/298372E2CE4764E885257687006F39DF?OpenDocument), vilket fungerar power rutnätet för New York tillstånd. Datauppsättningen innehåller per timme power data om efterfrågan för New York City under en period på fem år. En ytterligare datauppsättning som innehåller varje timme väderförhållanden i New York City under samma tidsperiod har kopplats från [darksky.net](https://darksky.net).

## <a name="prerequisites"></a>Förutsättningar

- En [Azure-konto](https://azure.microsoft.com/free/) (kostnadsfria utvärderingsversioner är tillgängliga).
- En installerad kopia av [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) följande den [installation snabbstartsguiden](quickstart-installation.md) att installera programmet och skapa en arbetsyta.
- Det här exemplet förutsätter att du använder Azure ML Workbench på Windows 10 med [Docker-motorn](https://www.docker.com/) installerats lokalt. Om du använder macOS är instruktionerna i stort sett desamma.
- Azure Machine Learning Operationalization installeras med en lokal distribution-miljö ställer in och ett konto för modellhantering skapats enligt beskrivningen i det här [guide](./model-management-configuration.md).
- Det här exemplet kräver att du uppdaterar Pandas-installationen till version 0.20.3 eller senare och installera matplotlib. Klicka på *öppna Kommandotolken* från den *filen* menyn i Workbench och kör följande kommandon för att installera dessa beroenden:

    ```
    conda install "pandas>=0.21.1"
    ```
    
## <a name="create-a-new-workbench-project"></a>Skapa ett nytt Workbench-projekt

Skapa ett nytt projekt med hjälp av det här exemplet som en mall:
1.  Öppna Azure Machine Learning Workbench
2.  På den **projekt** klickar du på den **+** och markera **nytt projekt**
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet
4.  I den **Sök efter projektmallar** sökrutan skriver du ”energi begäran tidsserier” och väljer mallen
5.  Klicka på **Skapa**


## <a name="data-description"></a>Databeskrivning

Två datauppsättningar tillhandahålls med det här exemplet och hämtas med hjälp av den `1-data-preparation.ipynb` anteckningsboken: `nyc_demand.csv` och `nyc_weather.csv`.

**nyc_demand.csv** innehåller per timme energi begäran värden för New York City i år 2012 2017. Data har följande enkel struktur:

| Tidsstämpel | begäran |
| --- | --- |
| 2012-01-01 00:00:00 | 4937.5 |
| 2012-01-01 01:00:00 | 4752.1 |
| 2012-01-01 02:00:00 | 4542.6 |
| 2012-01-01 03:00:00 | 4357.7 |

Begäran-värden är i megawattimmar (MWh). Nedan visas ett diagram över efterfrågan på energi under en period på 7 dagar i juli 2017:

![Efterfrågan på energi](./media/scenario-time-series-forecasting/energy_demand.png  "efterfrågan på energi")

**nyc_weather.csv** innehåller per timme väder värden för New York City år 2012-2017:

| Tidsstämpel | precip | Temp
| --- | --- | --- |
| 2012-01-01 00:00:00 | 0.0 | 46.13 |
| 2012-01-01 01:00:00 | 0.01 | 45.89 |
| 2012-01-01 02:00:00 | 0.05 | 45.04 |
| 2012-01-01 03:00:00 | 0.02 | 45.03 |

*precip* är ett procentmått av andelen nederbörd. *Temp* (temperaturvärden) har tagits skala så att alla värden som faller inom intervallet [0, 100].

## <a name="scenario-structure"></a>Scenario-struktur

När du öppnar det här projektet för första gången i Azure Machine Learning Workbench, navigera till den *filer* fönstret till vänster. Följande kodfiler tillhandahålls med det här exemplet:
- `1-data-preparation.ipynb` -den här Jupyter-anteckningsbok hämtar och bearbetar dessa data för att förbereda den för modellering. Det här är den första anteckningsboken som du vill köra.
- `2-linear-regression.ipynb` – den här anteckningsboken träna en linjär regressionsmodell på träningsdata.
- `3-ridge.ipynb` – Träna en regressionsmodell för upphöjning.
- `4-ridge-poly2.ipynb` – Träna en upphöjning regressionsmodell på polynom funktioner i grad 2.
- `5-mlp.ipynb` – Träna ett flera lager perceptron neurala nätverk.
- `6-dtree.ipynb` – Träna en modell för trädet beslut.
- `7-gbm.ipynb` – Träna en toning bättre modell.
- `8-evaluate-model.py` – Det här skriptet läser in en tränad modell och används för att göra förutsägelser kring en hålls ut test-datauppsättning. Den genererar modellen utvärderingsmått så att prestanda för olika modeller kan jämföras.
- `9-forecast-output-exploration.ipynb` – den här anteckningsboken producerar visualiseringar av de prognoser som genererats av machine learning-modeller.
- `10-deploy-model.ipynb` – den här anteckningsboken visar hur du kan operationaliserat en tränad prognosmodell i en webbtjänst i realtid.
- `evaluate-all-models.py` – Det här skriptet utvärderar alla anpassade modeller. Det är ett alternativ till körs `8-evaluate-model.py` modellera individuellt för varje tränas.

### <a name="1-data-preparation-and-cleaning"></a>1. Förberedelse av data och rensning

Om du vill börja köra exemplet, först klicka på `1-data-preparation.ipynb` att öppna anteckningsboken i förhandsgranskningsläge. Klicka på ***starta Anteckningsboksserver*** att starta en Jupyter notebook-server och kernel-Python på din dator. Du kan antingen köra anteckningsböcker från arbetsstationen eller du kan använda webbläsaren genom att gå till [ http://localhost:8888 ](http://localhost:8888). Observera att du måste ändra kerneln *projekt_namn lokal*. Du kan göra detta från den *Kernel* menyn i anteckningsboken under *ändra kernel*. Tryck på ***SKIFT + RETUR*** att köra koden i enskilda notebook celler eller klicka på *kör alla* i den *Cell* menyn för att köra anteckningsboken.

Anteckningsboken först ned data från ett blob storage-behållare som körs på Azure. Data lagras sedan på datorn i den `AZUREML_NATIVE_SHARE_DIRECTORY`. Den här platsen är tillgänglig från alla anteckningsböcker eller skript som du kör från Workbench så är en bra plats att lagra data och tränade modeller.

När data har hämtats, rensar anteckningsboken data genom att fylla luckor i tidsserien och fylla värden som saknas genom interpolation. Rensning av time series-data på det här sättet maximerar mängden data som är tillgängliga för att träna modeller.

Flera modellfunktioner skapas från rensade rådata. Dessa funktioner kan grupperas i två grupper:

- **Tid driven funktioner** härleds från den *tidsstämpel* variabeln t.ex. *månad*, *dayofweek* och *timme*.
- **Lagged funktioner** är beräkningsarbete tidsvärden för de faktiska värdena för begäran eller temperatur. De här funktionerna syftar till att samla in villkorlig beroenden mellan på varandra följande tidsperioder i modellen.

Den resulterande datauppsättningen i funktionen kan sedan användas när du utvecklar prognosmodeller.

### <a name="2-model-development"></a>2. Modeller

En första modellering-metod kan vara en enkel linjär regressionsmodell. Den `2-linear-regression.ipynb` notebook visar hur du tränar en linjär regression prognosmodell för efterfrågan på energi för framtida. I synnerhet modellen kommer utbildas i att förutse efterfrågan på energi en timme (*t + 1*) före den aktuella tidsperioden (*t*). Vi kan dock använda den här ”steg”-modellen rekursivt vid test för att skapa prognoser för framtida perioder, *t + n*.

För att träna en modell, skapas en förutsägande pipeline som innebär att tre steg:

- **En Dataomvandling**: format som krävs för indata kan variera beroende på maskininlärningsalgoritmen. I det här fallet kräver den linjära regressionsmodellen kategoriska variabler en frekvent kodas.
- **En mellan verifiering rutinen**: ofta en maskininlärningsmodell har en eller flera hyperparametrar som behöver justeras via experimentering. Mellan verifiering kan användas för att hitta den optimala uppsättningen av parametervärden. Modellen är flera gånger tränas och utvärderas på olika vikningar av datauppsättningen. De lämpligaste parametrarna är de som uppnår bästa modellprestanda när ett genomsnitt över mellan verifiering vikningar. Den här processen beskrivs i detalj i `2-linear-regression.ipynb`.
- **Utbildning för den slutliga modellen**: tränas på hela datauppsättningen, använda bästa uppsättningar av hyperparametrar.

Vi har inkluderat en serie med 6 olika modeller i anteckningsböcker numrerade 2-7. Varje anteckningsbok träna en annan modell och lagrar den i den `AZUREML_NATIVE_SHARE_DIRECTORY` plats. När du har tränats alla modeller som har utvecklats för det här scenariot, vi utvärdera och jämför dem som beskrivs i nästa avsnitt.

### <a name="3-model-evaluation-and-comparison"></a>3. Modeller utvärdering och jämförelse

Vi kan använda en tränad modell för att göra prognoser för framtida tidsperioder. Det är bäst att utvärdera dessa modeller på en datauppsättning som lagras utanför test. Genom att utvärdera de olika modellerna på samma överblivna datauppsättningen vi ganska jämför deras prestanda och identifiera den bästa modellen. I det här scenariot gäller vi den tränade modellen rekursivt prognos flera tid steg i framtiden. I synnerhet vi skapa prognoser för upp till sex timmar, *t + 6* före den aktuella timman *t*. De här förutsägelserna utvärderas mot den faktiska efterfrågan som observerats för varje tidsperiod.

Om du vill utvärdera en modell, öppna den `8-evaluate-model.py` skript från den *filer* fönstret i Workbench. Kontrollerar du att *kör konfiguration* är inställd på **lokala** och skriv sedan modellnamnet i den *argument* fält. Modellnamnet måste exakt matcha de *modellnamn* variabeln värdet i början av anteckningsboken modellen tränas. Ange till exempel ”linear_regression” för att utvärdera tränade linjär regression-modellen. Du kan också när alla modeller har tränats, du kan utvärdera dem med ett kommando genom att köra `evaluate-all-models.py`. Öppna en kommandotolk från Workbench för att köra skriptet och kör följande kommando:

```
python evaluate-all-models.py
```
Den `8-evaluate-model.py` skriptet utför följande åtgärder:

- Läser in en tränad pipeline från disk
- Förutsägelser mot testdatauppsättningen
- Beräknar modellen prestandamått och loggar dem
- Sparar testet datauppsättning förutsägelser till `AZUREML_NATIVE_SHARE_DIRECTORY` för senare granskning och analys
- Sparar tränade modellen att pipelinen den *matar ut* mapp.

> [!Note]
> Spara modellen till den *matar ut* mappen kopieras automatiskt model-objektet till Azure Blob Storage-konto som är associerade med ditt experimenteringskonto. Det innebär att du alltid kommer att kunna hämta sparade model-objektet från bloben, även om du ändrar datorn eller beräkningskontexten.

Navigera till den *Körningshistorik* fönstret och klicka på `8-evaluate-model.py`. Diagram som visar flera prestandamått för modellen visas:

- **MIG**: innebära fel av prognosen. Detta kan tolkas som genomsnittliga Basförskjutning för prognosen.
- **Största tillåtna fel**: [innebära procentandel fel](https://en.wikipedia.org/wiki/Mean_percentage_error) (ME uttryckt i procent av den faktiska efterfrågan)
- **MSE**: [innebära kvadratfel](https://en.wikipedia.org/wiki/Mean_squared_error)
- **RMSE**: rot medelvärdet cirkels fel (kvadratroten ur MSE)
- **MAPE**: [innebära absolut procentandel fel](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
- **sMAPE**: [symmetriska mean absolut procentandel fel](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)
- **MAPE_base**: MAPE för en baslinje Prognostisera där förutsägelser är lika med det senaste kända begäran-värdet.
- **MdRAE**: Median relativa absoluta fel. Median förhållandet mellan prognoser felet till i baslinjen Prognostisera fel. Ett värde mindre än 1 innebär prognosen fungerar bättre än baslinjen.

Alla mått ovan avser den *t + 1* prognos. Förutom ovanstående mått, visas också en uppsättning motsvarande mått med den *_horizon* suffix. Det här är måttet genomsnitt av alla punkter i prognoser intervallet från period *t + 1* till period *t + 6*.

Om mått inte visas i diagramområdet, klicka på kugghjulet i det övre högra hörnet. Se till att modellen prestandamått som du är intresserad av att kontrolleras. Mått visas också i en tabell under diagrammen. Igen, den här tabellen kan anpassas vara att klicka på kugghjulet. Sortera tabellen efter ett prestandamått för att identifiera den bästa modellen. Om du är intresserad av att se hur prognoser prestanda varierar från period *t + 1* till *t + 6*, klickar du på posten för modellen i tabellen. Diagram som visar MAPE största tillåtna fel och MdRAE mått över prognoser perioden visas *visualiseringar*.

När du utvärderar prognosmodellen modeller, kan det vara väldigt användbar för att visualisera utdata förutsägelser. Detta hjälper dataexpert för att avgöra om prognosen produceras visas realistisk. Det kan också bidra till att identifiera problem i prognosen om till exempel prognosen utför dåligt i vissa tidsperioder. Den `9-forecast-output-exploration.ipynb` anteckningsbok skapas med visualiseringar av de prognoser som genererats för test-datauppsättning.

### <a name="4-deployment"></a>4. Distribution

Den bästa modellen kan ska operationaliseras genom att distribuera den som en webbtjänst i realtid. Den här webbtjänsten kan sedan anropas för att skapa prognoser för efterfrågan när nya data blir tillgängliga. I det här scenariot måste en ny prognos genereras varje timme för att förutse efterfrågan på energi efterföljande timma. Om du vill utföra den här uppgiften, en webbtjänst kan distribueras som tar en matris med funktioner för en viss timme lång tid som indata och returnerar den förväntade begäran som utdata.

I det här exemplet distribueras en webbtjänst till en Windows 10-dator. Se till att du har slutfört nödvändiga steg för lokal distribution som anges i det här [Kom igång med](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md) för driftsättning CLI. När du har konfigurerat din lokala miljö och konto för modellhantering kan köra den `10-deploy-model.ipynb` anteckningsboken för att distribuera webbtjänsten.

## <a name="conclusion"></a>Sammanfattning

Detta exempel visar hur du skapar en slutpunkt till slutpunkt tidsserier lösning för prognostisering av efterfrågan på energi för prognostisering. Många av de principer som utforskat i det här exemplet kan utökas till andra prognosmodellen scenarier och branscher.

Det här scenariot visar hur Azure Machine Learning Workbench kan hjälpa någon dataexpert för utveckling av verkliga lösningar med användbara funktioner, till exempel Jupyter notebook-miljön och mått loggningsfunktioner. Exemplet visar också läsaren på hur en modell kan ska operationaliseras och distribueras med Azure Machine Learning Operationalization CLI. När distribuerats kan den webbtjänst-API utvecklare eller dataingenjörer att integrera en bredare datapipeline i prognosmodellen.
