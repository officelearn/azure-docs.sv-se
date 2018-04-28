---
title: Energi begäran tidsserier prognoser | Microsoft Docs
description: Så här använder maskininlärning att förutsäga energi begäran tidsserier i Azure Machine Learning-arbetsstationen.
services: machine-learning
documentationcenter: ''
author: anta
manager: ireiter
editor: anta
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: anta
ms.openlocfilehash: b40ebba3c85760d947861b4215dab6e16a86002b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="energy-demand-time-series-forecasting"></a>Energi begäran tidsserier prognoser


Tid serien prognoser är att förutsäga framtida värden i en tid sorterade sekvens av observationer. Det är ett vanligt problem och har program i många branscher. Till exempel behöver retail företag att förutsäga framtida produktförsäljning så att de effektivt kan ordna sina ange kedjor att uppfylla begäran. På liknande sätt behöver paketet leverans företag att beräkna behovet av sina tjänster så att de kan planera arbetsstyrka krav och leveransvägar i förväg. I många fall vara finansiella risker stämmer prognoser betydande. Därför prognoser är ofta en kritisk verksamhet.

Det här exemplet visar hur tiden serien prognoser kan utföras genom att använda tekniker för machine learning. Vägleds du genom alla steg i modellering processen inklusive:
- Förberedelse av data att rensa och formatera data.
- Skapa funktioner för maskininlärning modeller från rådata serien tidsdata;
- Utbildning olika machine learning-modeller.
- Utvärdera modellerna genom att jämföra deras prestanda på hålls out testdata; och
- Operationalizing bästa modellen, vilket gör det tillgängligt via en webbtjänst för att generera prognoser på begäran.

Azure Machine Learning arbetsstationen underlättar modelleringsprocessen vid varje steg: 
- Exemplet visar hur Jupyter-anteckningsbok miljö - tillgängliga direkt från via arbetsstationen - ser utvecklar enklare Python-kod. Modellen utvecklingsprocessen förklaras till andra tydligare med markdown anteckningar och diagram. Dessa datorer kan visas, redigeras och köras direkt från arbetsstationen.
- Tränade modeller kan beständiga och överföras till blob storage. Detta hjälper data forskare att hålla reda på tränade modellobjekt och kontrollera de sparade hämtningsbart strängfält vid behov.
- Mått kan loggas vid körning av Python-skriptet, aktiverar data forskare att registrera modellen prestanda resultat.
- Arbetsstationen producerar anpassningsbara tabeller av loggade mätvärden, vilket gör att data forskare enkelt kan jämföra prestandamått för modellen. Diagram visas automatiskt så att den bäst prestanda modellen enkelt kan identifieras.
- Slutligen visar exemplet hur en tränad modell kan vara operationalized genom att distribuera den i en webbtjänst i realtid.

## <a name="link-to-the-gallery-github-repository"></a>Länka till galleriet GitHub-lagringsplatsen
Offentliga GitHub-lagringsplatsen för den här verkligt scenario innehåller alla material, inklusive kodexempel som behövs för det här exemplet:

[https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)


## <a name="use-case-overview"></a>Använd case-översikt

Det här scenariot fokuserar på energiförbrukning prognoser där målet är att förutsäga framtida belastningen på ett energi rutnät. Det är en åtgärd för affärskritiska för företag inom energi som operatorer behöver en bra balans mellan energiförbrukningen i ett rutnät och energi som angetts för den. För mycket ström som har angetts i rutnätet kan resultera i skräp av energi eller tekniska fel. Men om för lite power anges det kan leda till strömavbrott, lämnar kunder utan power. Rutnätet operatorer tar normalt, kortsiktig beslut att hantera energiförsörjning i rutnätet och Tänk utjämna belastningen. Ett kortsiktigt rättvisande prognos av energiförbrukning är därför viktigt för operatorn för att göra dessa beslut med förtroende.

Det här scenariot beskrivs konstruktion av machine learning energiförbrukning prognoser lösning. Lösningen har installerats på en offentlig datamängd från den [New York oberoende System operatorn (NYISO)](http://www3.dps.ny.gov/W/PSCWeb.nsf/All/298372E2CE4764E885257687006F39DF?OpenDocument), som fungerar power rutnätet för New York tillstånd. Dataset innehåller varje timme power begäran data för New York City under en period på fem år. En ytterligare datamängd som innehåller varje timme väder i New York City under samma period hämtades från [darksky.net](https://darksky.net).

## <a name="prerequisites"></a>Förutsättningar

- En [Azure-konto](https://azure.microsoft.com/free/) (gratisutvärderingar finns).
- En installerad kopia av [Azure Machine Learning arbetsstationen](../service/overview-what-is-azure-ml.md) följande den [installation snabbstartsguiden](../service/quickstart-installation.md) att installera programmet och skapa en arbetsyta.
- Det här exemplet förutsätter att du använder Azure ML-arbetsstationen på Windows 10 med [Docker-motorn](https://www.docker.com/) har installerats lokalt. Om du använder macOS är instruktionerna i stort sett desamma.
- Azure Machine Learning Operationalization som installerats med en lokal distributionsmiljö ställer in och ett konto för hantering av modellen skapats enligt beskrivningen i det här [guiden](./model-management-configuration.md).
- Det här exemplet kräver att du uppdaterar Pandas installationen till version 0.20.3 eller senare och installera matplotlib. Klicka på *öppnar du kommandotolken* från den *filen* -menyn i arbetsstationen och kör följande kommandon för att installera dessa beroenden:

    ```
    conda install "pandas>=0.21.1"
    ```
    
## <a name="create-a-new-workbench-project"></a>Skapa ett nytt projekt arbetsstationen

Skapa ett nytt projekt med det här exemplet som mall:
1.  Öppna Azure Machine Learning-arbetsstationen
2.  På den **projekt** klickar du på den **+** och markera **nytt projekt**
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet
4.  I den **Sök projektmallar** sökrutan, Skriv ”energi begäran tid serien prognoser” och välj mallen
5.  Klicka på **Skapa**


## <a name="data-description"></a>Beskrivning av data

Två datauppsättningar som tillhandahålls med det här exemplet och hämtas med hjälp av den `1-data-preparation.ipynb` anteckningsboken: `nyc_demand.csv` och `nyc_weather.csv`.

**nyc_demand.csv** innehåller varje timme energi begäran värden för New York City år 2012 2017. Data har följande enkel struktur:

| Tidsstämpel | begäran |
| --- | --- |
| 2012-01-01 00:00:00 | 4937.5 |
| 2012-01-01 01:00:00 | 4752.1 |
| 2012-01-01 02:00:00 | 4542.6 |
| 2012-01-01 03:00:00 | 4357.7 |

Begäran värdena i megawattimmar (MWh). Nedan visas ett diagram över energiförbrukning under en period för 7 dagar i juli 2017:

![Energiförbrukning](./media/scenario-time-series-forecasting/energy_demand.png  "energiförbrukning")

**nyc_weather.csv** innehåller varje timme väder värden för New York City års 2012 2017:

| Tidsstämpel | precip | Temp
| --- | --- | --- |
| 2012-01-01 00:00:00 | 0.0 | 46.13 |
| 2012-01-01 01:00:00 | 0.01 | 45.89 |
| 2012-01-01 02:00:00 | 0.05 | 45.04 |
| 2012-01-01 03:00:00 | 0.02 | 45.03 |

*precip* är ett procentmått av utfällning. *Temp* har varit skala (temperatur) värden så att alla värden ligger inom intervallet [0, 100].

## <a name="scenario-structure"></a>Scenario-struktur

När du öppnar det här projektet för första gången i Azure Machine Learning arbetsstationen, navigera till den *filer* fönstret till vänster. Följande kodfiler tillhandahålls med det här exemplet:
- `1-data-preparation.ipynb` -Jupyter-anteckningsbok hämtar och bearbetar data för att förbereda för förutsägelsemodellering. Det här är den första bärbara dator du vill köra.
- `2-linear-regression.ipynb` -anteckningsboken tränar en linjär regressionsmodell för utbildning-data.
- `3-ridge.ipynb` -tränar en upphöjning regressionsmodell.
- `4-ridge-poly2.ipynb` -tränar en upphöjning regressionsmodell polynom funktionerna i vilken grad 2.
- `5-mlp.ipynb` -tränar en flera lager perceptron neurala nätverket.
- `6-dtree.ipynb` -tränar en modell för beslut trädet.
- `7-gbm.ipynb` -tränar en modell för toning förstärkta datorn.
- `8-evaluate-model.py` -skriptet läser in en tränad modell och används för att göra förutsägelser på en hålls out testdata. Den ger mått för utvärdering av modellen så att prestanda för olika modeller kan jämföras.
- `9-forecast-output-exploration.ipynb` -anteckningsboken producerar visualiseringar av prognoser som genererats av modeller för maskininlärning.
- `10-deploy-model.ipynb` -anteckningsboken visar hur du kan operationalized utbildade prognosmodellen i en webbtjänst i realtid.
- `evaluate-all-models.py` -Det här skriptet utvärderar alla tränade modeller. Det ger ett alternativ till att köra `8-evaluate-model.py` för varje tränat modellen individuellt.

### <a name="1-data-preparation-and-cleaning"></a>1. Förberedelse av data och rensning

Om du vill börja köra exemplet, klicka först på `1-data-preparation.ipynb` vill öppna anteckningsboken i förhandsgranskningsläge. Klicka på ***starta anteckningsboken Server*** att starta en Jupyter-anteckningsbok server och Python kernel på din dator. Du kan antingen köra anteckningsböcker från arbetsstationen eller använda webbläsaren genom att gå till [ http://localhost:8888 ](http://localhost:8888). Observera att du måste ändra kerneln och *projekt_namn lokala*. Du kan göra detta från den *Kernel* -menyn i den bärbara datorn under *ändra kernel*. Tryck på ***SKIFT + RETUR*** kör kod i enskilda anteckningsboken celler, eller klicka på *kör alla* i den *Cell* menyn för att köra anteckningsboken.

Anteckningsboken först hämtar data från en behållare för blob storage finns i Azure. Data lagras på din dator i den `AZUREML_NATIVE_SHARE_DIRECTORY`. Den här platsen är tillgänglig från alla anteckningsböcker eller skript som du kör från arbetsstationen så är en bra plats att lagra data och tränade modeller.

När data har hämtats, rensar anteckningsboken data genom att fylla luckor i tidsserien och fylla värden som saknas genom interpolerade. Rensa tid series-data på det här sättet maximerar mängden data som är tillgängliga för att träna modeller.

Flera modell-funktioner har skapats från rensade rådata. Dessa funktioner kan kategoriseras i två grupper:

- **Tid som drivs funktioner** härleds från den *tidsstämpel* variabeln t.ex. *månad*, *dayofweek* och *timme*.
- **Lagged funktioner** är flyttat tidsvärden av de faktiska begäran eller temperatur värdena. Dessa funktioner syftar till att avbilda villkorlig beroenden mellan på varandra följande tidsperioder i modellen.

Resultatet av funktionen kan sedan användas när du utvecklar prognosmodeller.

### <a name="2-model-development"></a>2. Modell-utveckling

En första sätt att modellera kan vara en enkel linjär regressionsmodell. Den `2-linear-regression.ipynb` anteckningsboken visar hur du tränar en linjär regression prognosmodell för framtida energiförbrukning. I synnerhet modellen kommer utbildas att förutsäga energiförbrukning en timme (*t + 1*) i den aktuella tidsperioden (*t*). Vi kan dock använda den här 'steg' modellen rekursivt vid test för att generera prognoser för framtida tidsperioder, *t + n*.

För att träna en modell, skapas en förutsägbar pipeline som omfattar tre steg:

- **En Dataomvandling**: format som krävs för indata kan variera beroende på maskininlärningsalgoritmen. I det här fallet kräver linjär regressionsmodell kategoriska variabler som ska vara ett hot kodad.
- **En mellan validering rutinen**: ofta en maskininlärningsmodell har en eller flera justeringsmodeller som behöver justeras genom försök. Mellan verifiering kan användas för att hitta den optimala uppsättningen av parametervärden. Modellen är flera gånger tränas och utvärderas på olika vikningar för dataset. De lämpligaste parametrarna är de som uppnår modellen bästa prestanda när ett genomsnitt över mellan validering vikningar. Den här processen beskrivs i detalj i `2-linear-regression.ipynb`.
- **Den slutliga modell**: modellen har tränats på hela datauppsättningen med hjälp av den bästa för justeringsmodeller.

Det finns ett antal 6 olika modeller i anteckningsböcker numrerade 2-7. Varje anteckningsbok tränar en annan modell och lagrar den i den `AZUREML_NATIVE_SHARE_DIRECTORY` plats. När du har tränat alla modeller som utvecklats för det här scenariot, vi utvärdera och jämför dem som beskrivs i nästa avsnitt.

### <a name="3-model-evaluation-and-comparison"></a>3. Modellen utvärdering och jämförelse

Vi kan använda en tränad modell för att göra prognoser för framtida tidsperioder. Det är bäst att utvärdera dessa modeller på en hålls out testdata. Genom att utvärdera olika modeller på samma överblivna datamängd vi ganska jämför deras prestanda och identifiera bästa modellen. I det här scenariot gäller vi den tränade modell rekursivt prognos flera tid steg i framtiden. I synnerhet vi generera prognoser för upp till sex timmar *t + 6* i den aktuella timman *t*. Dessa förutsägelser utvärderas mot den faktiska efterfrågan observerats för varje tidsperiod.

Om du vill utvärdera en modell, öppna den `8-evaluate-model.py` skript från den *filer* rutan i arbetsstationen. Kontrollera att *kör konfiguration* är inställd på **lokala** och skriv sedan modellnamnet i den *argument* fältet. Modellnamnet måste matcha exakt den *modellnamn* variabeln har angetts i början av den bärbara datorn där modellen har tränats. Ange till exempel ”linear_regression” om du vill utvärdera utbildade linjär regressionsmodell. Du kan också när alla modeller har tränats, du kan utvärdera dem med ett kommando genom att köra `evaluate-all-models.py`. Öppna en kommandotolk från arbetsstationen för att köra skriptet och kör följande kommando:

```
python evaluate-all-models.py
```
Den `8-evaluate-model.py` skriptet utför följande åtgärder:

- Läser in en tränad modell pipeline från disken
- Gör förutsägelser mot testdatauppsättningen
- Beräknar modellen prestandamått och loggar dem
- Sparar testet dataset förutsägelser till `AZUREML_NATIVE_SHARE_DIRECTORY` senare kontroll och analys
- Sparar den tränade modellen pipelinen till den *matar ut* mapp.

> [!Note]
> Spara modellen till den *matar ut* mappen kopieras automatiskt model-objektet i Azure Blob Storage-konto som är associerade med kontot experiment. Det innebär att du alltid kommer att kunna hämta sparade model-objektet från blob, även om du ändrar datorn eller compute-kontexten.

Navigera till den *Körningshistorik* rutan och klicka på `8-evaluate-model.py`. Diagram som visar flera prestandamått för modellen visas:

- **MIG**: innebär fel prognosens. Detta kan tolkas som genomsnittliga bias prognosens.
- **Största tillåtna**: [innebär procentandel fel](https://en.wikipedia.org/wiki/Mean_percentage_error) (ME uttryckt i procent av den faktiska begäran)
- **MUS**: [medelvärdet av kvadratfel](https://en.wikipedia.org/wiki/Mean_squared_error)
- **RMSE**: roten medelvärdet kvadrat fel (kvadratroten av mus)
- **MAPE**: [innebär absolut procentandel fel](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
- **sMAPE**: [symmetriska medelvärde absolut procentandel fel](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)
- **MAPE_base**: MAPE för en baslinje prognos där förutsägelser är lika med det senaste kända begärd-värdet.
- **MdRAE**: Median relativa absoluta fel. Fel vid en prognos median förhållandet mellan prognosen felet till i baslinjen. Ett värde mindre än 1 innebär prognosen presterar bättre än baslinjen.

Alla mätvärden ovan avser den *t + 1* prognos. Förutom mätvärdena som ovan visas också en uppsättning motsvarande mått med den *_horizon* suffix. Detta är måttet som ett genomsnitt över alla perioder prognosen mellan period *t + 1* period *t + 6*.

Om mått visas i diagramområdet, klickar du på kugghjulet symbolen i det övre högra hörnet. Kontrollera att modellen prestandamått som du är intresserad av kontrolleras. Mätvärdena visas också i en tabell under diagrammen. Igen och den här tabellen är anpassningsbara klickar på växeln symbolen. Sortera tabellen efter ett prestanda mått för att identifiera bästa modellen. Om du vill se hur prognosen prestandan varierar från period *t + 1* till *t + 6*, klickar du på posten för modellen i tabellen. Diagram som visar MAPE, största tillåtna fel och MdRAE mått över prognosen perioden visas *visualiseringar*.

När du utvärderar prognosmodellen modeller, kan det vara användbart att visualisera förutsägelser för utdata. Detta hjälper data forskare att avgöra om prognosen producerade visas realistiska. Det kan också hjälpa för att identifiera problem i prognosen om till exempel prognosen utför dåligt i vissa tidsperioder. Den `9-forecast-output-exploration.ipynb` anteckningsboken genererar visualiseringar av prognoser genereras för testdatauppsättningen.

### <a name="4-deployment"></a>4. Distribution

Vara kan operationalized bästa modellen genom att distribuera den som en webbtjänst i realtid. Den här webbtjänsten kan sedan anropas för att generera prognoser på begäran när nya data blir tillgänglig. I det här scenariot behöver en ny prognos skapas varje timme för att förutsäga efterfrågan på energi under den kommande timmen. Om du vill utföra den här uppgiften en webbtjänst kan distribueras som en matris av funktionerna för en given timme tidsperiod som indata och returnerar den förväntade efterfrågan som utdata.

I det här exemplet har en webbtjänst distribuerats till en Windows 10-dator. Se till att du har slutfört nödvändiga steg för lokala distribution som anges i detta [Kom igång med](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md) för Operationalization CLI. När du har konfigurerat din lokala miljö och modellen hanteringskontot kör den `10-deploy-model.ipynb` anteckningsboken för att distribuera webbtjänsten.

## <a name="conclusion"></a>Sammanfattning

Det här exemplet visar hur du skapar en slutpunkt till slutpunkt tidsserier prognoser lösning för Prognosticering energiförbrukning. Många av de principer som utforskade i det här exemplet kan utökas till andra prognosmodellen scenarier och branscher.

Det här scenariot visar hur Azure Machine Learning arbetsstationen kan hjälpa en data-forskare få verkliga lösningar med användbara funktioner, till exempel Jupyter-anteckningsbok miljö och mått loggningsfunktioner. Exemplet visar även hur läsaren på hur en modell kan operationalized och distribueras via Azure Machine Learning Operationalization CLI. Distribution kan kan web service API utvecklare eller data tekniker att integrera prognosmodellen i en bredare data pipeline.
