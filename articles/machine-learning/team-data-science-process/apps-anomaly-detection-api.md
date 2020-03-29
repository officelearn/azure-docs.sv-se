---
title: Api för identifiering av azure machine learning-avvikelse – teamdatavetenskapsprocess
description: Api för avvikelseidentifiering är ett exempel som skapats med Microsoft Azure Machine Learning och som identifierar avvikelser i tidsseriedata med numeriska värden som är jämnt fördelade i tiden.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: a09094cf0d1bd3c2e299e968d7de8410dcd9c3cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721888"
---
# <a name="machine-learning-anomaly-detection-api"></a>API för identifiering av avvikelseidentifiering av maskininlärning

> [!NOTE]
> Denna artikel är under underhåll. Vi rekommenderar att du använder [API-tjänsten Anomaly Detector](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) som drivs av ett galleri med Machine Learning-algoritmer under Azure Cognitive Services för att identifiera avvikelser från affärs-, drifts- och IoT-mått.

## <a name="overview"></a>Översikt
[Anomaly Detection API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) är ett exempel som skapats med Azure Machine Learning som identifierar avvikelser i tidsseriedata med numeriska värden som är jämnt fördelade i tiden.

Det här API:et kan identifiera följande typer av avvikande mönster i tidsseriedata:

* **Positiva och negativa trender:** När man till exempel övervakar minnesanvändningen vid beräkning av en uppåtgående trend kan det vara av intresse eftersom det kan tyda på en minnesläcka.
* **Ändringar i det dynamiska värdeområdet:** Till exempel när de undantag som genereras av en molntjänst övervakas kan eventuella ändringar i det dynamiska värdeområdet tyda på instabilitet i tjänstens hälsa, och
* **Toppar och dips:** Till exempel, när du övervakar antalet inloggningsfel i en tjänst eller antal kassor i en e-handelsplats, spikar eller dips kan tyda på onormalt beteende.

Dessa maskininlärningsdetektorer spårar sådana värdeändringar över tid och rapporterar pågående förändringar i sina värden som avvikelsepoäng. De kräver inte adhoc tröskeljustering och deras poäng kan användas för att kontrollera falska positiva takt. Anomaliidentifiering API är användbart i flera scenarier som tjänst övervakning genom att spåra KPI:er över tiden, användning övervakning genom mått såsom antal sökningar, antal klick, prestandaövervakning genom räknare som minne, CPU, filläsningar, etc. över tid.

Erbjudandet om avvikelseidentifiering levereras med användbara verktyg för att komma igång.

* [Webbprogrammet](https://anomalydetection-aml.azurewebsites.net/) hjälper dig att utvärdera och visualisera resultaten av API:er för avvikelseidentifiering på dina data.

> [!NOTE]
> Prova **IT Anomaly Insights-lösning** som drivs av [det här API:et](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>API-distribution
För att kunna använda API:et måste du distribuera det till din Azure-prenumeration där den kommer att vara värd för en Azure Machine Learning-webbtjänst.  Du kan göra detta från [Azure AI Gallery](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Detta distribuerar två Azure Machine Learning Studio (klassiska) Webbtjänster (och deras relaterade resurser) till din Azure-prenumeration - en för avvikelseidentifiering med säsongsidentifiering och en utan säsongsidentifiering.  När distributionen har slutförts kan du hantera dina API:er från sidan [Azure Machine Learning Studio (klassisk) webbtjänster.](https://services.azureml.net/webservices/)  Från den här sidan kan du hitta dina slutpunktsplatser, API-nycklar samt exempelkod för att anropa API:et.  Mer detaljerade instruktioner finns [här](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Skala API:et
Som standard har distributionen en kostnadsfri faktureringsplan för utveckling/test som innehåller 1 000 transaktioner/månad och 2 beräkningstimmar/månad.  Du kan uppgradera till ett annat plan enligt dina behov.  Information om prissättningen av olika planer finns [här](https://azure.microsoft.com/pricing/details/machine-learning/) under "Production Web API prissättning".

## <a name="managing-aml-plans"></a>Hantera AML-planer
Du kan hantera din faktureringsplan [här](https://services.azureml.net/plans/).  Plannamnet baseras på det resursgruppsnamn du valde när du distribuerade API:et, plus en sträng som är unik för din prenumeration.  Instruktioner om hur du uppgraderar din plan finns [här](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) under avsnittet "Hantera faktureringsplaner".

## <a name="api-definition"></a>API-definition
Webbtjänsten tillhandahåller ett REST-baserat API via HTTPS som kan förbrukas på olika sätt, inklusive ett webb- eller mobilprogram, R, Python, Excel, etc.  Du skickar dina tidsseriedata till den här tjänsten via ett REST API-anrop och kör en kombination av de tre avvikelsetyperna som beskrivs nedan.

## <a name="calling-the-api"></a>Anropa API:et
För att kunna anropa API:et måste du känna till slutpunktsplatsen och API-nyckeln.  Dessa två krav, tillsammans med exempelkod för att anropa API: et, är tillgängliga från sidan [Azure Machine Learning Studio (klassisk) webbtjänster.](https://services.azureml.net/webservices/)  Navigera till önskat API och klicka sedan på fliken "Förbruka" för att hitta dem.  Du kan anropa API:et som ett Swagger `format=swagger`API (det vill säga med URL-parametern) `format` eller som ett ICKE-Swagger-API (det vill säga utan URL-parametern).  Exempelkoden använder Swagger-formatet.  Nedan visas en exempelbegäran och ett svar i icke-Swagger-format.  Dessa exempel är till säsongsvariationslutpunkten.  Slutpunkten för icke-säsongsvariationer är likartad.

### <a name="sample-request-body"></a>Exempel på begärantext
Begäran innehåller två `Inputs` objekt: `GlobalParameters`och .  I exempelbegäran nedan skickas vissa parametrar uttryckligen medan andra inte är det (rulla nedåt för en fullständig lista med parametrar för varje slutpunkt).  Parametrar som inte skickas uttryckligen i begäran använder standardvärdena nedan.

    {
                "Inputs": {
                        "input1": {
                                "ColumnNames": ["Time", "Data"],
                                "Values": [
                                        ["5/30/2010 18:07:00", "1"],
                                        ["5/30/2010 18:08:00", "1.4"],
                                        ["5/30/2010 18:09:00", "1.1"]
                                ]
                        }
                },
        "GlobalParameters": {
            "tspikedetector.sensitivity": "3",
            "zspikedetector.sensitivity": "3",
            "bileveldetector.sensitivity": "3.25",
            "detectors.spikesdips": "Both"
        }
    }

### <a name="sample-response"></a>Exempelsvar
För att kunna `ColumnNames` se fältet `details=true` måste du inkludera som url-parameter i din begäran.  Se tabellerna nedan för innebörden bakom vart och ett av dessa fält.

    {
        "Results": {
            "output1": {
                "type": "table",
                "value": {
                    "Values": [
                        ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                        ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                        ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                    ],
                    "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                    "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
                }
            }
        }
    }


## <a name="score-api"></a>Poäng API
Poäng-API:et används för att köra avvikelseidentifiering på icke-säsongsbaserade tidsseriedata. API:et kör ett antal avvikelsedetektorer på data och returnerar deras avvikelsepoäng.
Bilden nedan visar ett exempel på avvikelser som poäng-API:et kan identifiera. Den här tidsserien har två distinkta nivåändringar och tre spikar. De röda punkterna visar den tid då nivåändringen identifieras, medan de svarta punkterna visar de upptäckta spikarna.
![Poäng API][1]

### <a name="detectors"></a>Detektorer
Api:et för avvikelseidentifiering stöder detektorer i tre breda kategorier. Information om specifika indataparametrar och utgångar för varje detektor finns i följande tabell.

| Detektor kategori | Detektor | Beskrivning | Indataparametrar | Utdata |
| --- | --- | --- | --- | --- |
| Spike Detektorer |TSpike Detektor |Upptäck spikar och dips baserat på långt värdena är från första och tredje kvartilen |*tspikedetector.sensitivity:* tar heltalsvärde i intervallet 1-10, standard: 3; Högre värden kommer att fånga mer extrema värden vilket gör det mindre känsligt |TSpike: binära värden – "1" om en spik / dip upptäcks, "0" annars |
| Spike Detektorer | ZSpike Detektor |Identifiera toppar och dips baserat på hur långt datapunkterna är från deras medelvärde |*zspikedetector.sensitivity:* ta heltalsvärde i intervallet 1-10, standard: 3; Högre värden kommer att fånga mer extrema värden som gör det mindre känsligt |ZSpike: binära värden – "1" om en spik / dip upptäcks, "0" annars |
| Långsam trend detektor |Långsam trend detektor |Identifiera långsam positiv trend enligt den inställda känsligheten |*trenddetector.sensitivity:* tröskel på detektor poäng (standard: 3,25, 3,25 - 5 är ett rimligt intervall att välja mellan; Ju högre desto mindre känsliga) |tscore: flytande tal som representerar anomalipoäng på trend |
| Nivåförändringsdetektorer | Dubbelriktad nivåförändringsdetektor |Känne av både förändring uppåt och nedåt enligt den inställda känsligheten |*bileveldetector.sensitivity:* tröskel på detektor poäng (standard: 3,25, 3,25 - 5 är ett rimligt intervall att välja mellan; Ju högre desto mindre känsliga) |rpscore: flytande tal som representerar avvikelsepoäng på uppåt- och nedåtnivåförändring |

### <a name="parameters"></a>Parametrar
Mer detaljerad information om dessa indataparametrar finns i tabellen nedan:

| Indataparametrar | Beskrivning | Standardinställningen | Typ | Giltigt intervall | Föreslaget intervall |
| --- | --- | --- | --- | --- | --- |
| detectors.historywindow |Historik (i # för datapunkter) som används för beräkning av avvikelsepoäng |500 |heltal |10-2000 |Beroende av tidsserier |
| detectors.spikesdips | Om du bara vill upptäcka spikar, bara dips eller båda |Båda |Uppräknade |Båda, Spikes, Dips |Båda |
| bileveldetector.sensitivity |Känslighet för dubbelriktad nivåförändringsdetektor. |3.25 |double |Inget |3.25-5 (Mindre värden betyder känsligare) |
| trenddetector.sensitivitet |Känslighet för positiv trenddetektor. |3.25 |double |Inget |3.25-5 (Mindre värden betyder känsligare) |
| tspikedetector.sensitivity |Känslighet för TSpike Detector |3 |heltal |1-10 |3-5 (Mindre värden betyder känsligare) |
| zspikedetector.sensitivity |Känslighet för ZSpike Detector |3 |heltal |1-10 |3-5 (Mindre värden betyder känsligare) |
| postprocess.tailRows |Antal av de senaste datapunkterna som ska hållas i utdataresultaten |0 |heltal |0 (behåll alla datapunkter) eller ange antal punkter att hålla i resultat |Ej tillämpligt |

### <a name="output"></a>Resultat
API:et kör alla detektorer på dina tidsseriedata och returnerar avvikelsepoäng och binära spikindikatorer för varje tidpunkt. I tabellen nedan visas utdata från API:et.

| Utdata | Beskrivning |
| --- | --- |
| Tid |Tidsstämplar från rådata, eller aggregerade (och/eller) imputerade data om aggregering (och/eller) dataimputering av data tillämpas |
| Data |Värden från rådata, eller aggregerade (och/eller) imputerade data om aggregering (och/eller) dataimputering av data tillämpas |
| TSpike (på väg mot människor) |Binär indikator för att ange om en spik detekteras av TSpike Detector |
| ZSpike (på väg mot honom) |Binär indikator för att ange om en spik detekteras av ZSpike Detector |
| rpscore (rpscore) |Ett flytande tal som representerar avvikelsepoäng på dubbelriktad nivåändring |
| rpalert (rpalert) |1/0-värde som anger att det finns en dubbelriktad nivåförändringsavvikelse baserat på indatakänsligheten |
| tscore (tscore) |Ett flytande tal som representerar avvikelsepoäng på positiv trend |
| talert (talert) |1/0-värde som indikerar att det finns en positiv trendavvikelse baserad på ingångskänsligheten |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
ScoreWithSeasonality API används för att köra avvikelseidentifiering på tidsserier som har säsongsmönster. Det här API:et är användbart för att identifiera avvikelser i säsongsmönster.
Följande bild visar ett exempel på avvikelser som upptäckts i en säsongstidsserie. Tidsserien har en spik (den första svarta punkten), två dopp (den andra svarta punkten och en i slutet) och en nivåändring (röd punkt). Både dippen i mitten av tidsserien och nivåförändringen kan endast urskiljas efter att säsongskomponenter tagits bort från serien.
![API för säsongsvariationer][2]

### <a name="detectors"></a>Detektorer
Detektorerna i säsongsmåttet liknar de i slutpunkten för icke-säsongsvariationer, men med något olika parameternamn (listade nedan).

### <a name="parameters"></a>Parametrar

Mer detaljerad information om dessa indataparametrar finns i tabellen nedan:

| Indataparametrar | Beskrivning | Standardinställningen | Typ | Giltigt intervall | Föreslaget intervall |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Aggregeringsintervall i sekunder för aggregering av indatatidsserier |0 (ingen aggregering utförs) |heltal |0: hoppa över aggregering, > 0 annars |5 minuter till 1 dag, tidsserieberoende |
| preprocess.aggregationFunc |Funktion som används för att aggregera data i den angivna aggregeringIntervalen |medelvärde |Uppräknade |medelvärde, summa, längd |Ej tillämpligt |
| preprocess.replaceMissing |Värden som används för att tillskriva data som saknas |lkv (senast kända värde) |Uppräknade |noll, lkv, medelvärde |Ej tillämpligt |
| detectors.historywindow |Historik (i # för datapunkter) som används för beräkning av avvikelsepoäng |500 |heltal |10-2000 |Beroende av tidsserier |
| detectors.spikesdips | Om du bara vill upptäcka spikar, bara dips eller båda |Båda |Uppräknade |Båda, Spikes, Dips |Båda |
| bileveldetector.sensitivity |Känslighet för dubbelriktad nivåförändringsdetektor. |3.25 |double |Inget |3.25-5 (Mindre värden betyder känsligare) |
| postrenddetector.sensitivity |Känslighet för positiv trenddetektor. |3.25 |double |Inget |3.25-5 (Mindre värden betyder känsligare) |
| negtrenddetector.sensitivity |Känslighet för negativ trenddetektor. |3.25 |double |Inget |3.25-5 (Mindre värden betyder känsligare) |
| tspikedetector.sensitivity |Känslighet för TSpike Detector |3 |heltal |1-10 |3-5 (Mindre värden betyder känsligare) |
| zspikedetector.sensitivity |Känslighet för ZSpike Detector |3 |heltal |1-10 |3-5 (Mindre värden betyder känsligare) |
| säsongsvariationer.enable |Om säsongsanalys ska utföras |true |boolean |SANT, FALSKT |Beroende av tidsserier |
| säsongsvariationer.numSeasonalitet |Maximalt antal periodiska cykler som ska detekteras |1 |heltal |1, 2 |1-2 |
| säsongsvariationer.omvandla |Huruvida säsongskomponenter (och) trendkomponenter ska tas bort innan avvikelsedetektering |deseason (avseason) |Uppräknade |ingen, deseason, deseasontrend |Ej tillämpligt |
| postprocess.tailRows |Antal av de senaste datapunkterna som ska hållas i utdataresultaten |0 |heltal |0 (behåll alla datapunkter) eller ange antal punkter att hålla i resultat |Ej tillämpligt |

### <a name="output"></a>Resultat
API:et kör alla detektorer på dina tidsseriedata och returnerar avvikelsepoäng och binära spikindikatorer för varje tidpunkt. I tabellen nedan visas utdata från API:et.

| Utdata | Beskrivning |
| --- | --- |
| Tid |Tidsstämplar från rådata, eller aggregerade (och/eller) imputerade data om aggregering (och/eller) dataimputering av data tillämpas |
| OriginalData |Värden från rådata, eller aggregerade (och/eller) imputerade data om aggregering (och/eller) dataimputering av data tillämpas |
| BearbetadeData |Något av följande alternativ: <ul><li>Säsongrensade tidsserier om betydande säsongsvariationer har upptäckts och deseason alternativ valt;</li><li>säsongrensade och trendiga tidsserier om betydande säsongsvariationer har upptäckts och deseasontrend alternativ valt</li><li>Annars är det här alternativet detsamma som OriginalData</li> |
| TSpike (på väg mot människor) |Binär indikator för att ange om en spik detekteras av TSpike Detector |
| ZSpike (på väg mot honom) |Binär indikator för att ange om en spik detekteras av ZSpike Detector |
| BiLevelChangeScore |Ett flytande tal som representerar avvikelsepoäng på nivåändring |
| BiLevelChangeAlert |1/0-värde som anger att det finns en nivåförändringsavvikelse baserad på indatakänsligheten |
| PosTrendscore (PosTrendscore) |Ett flytande tal som representerar avvikelsepoäng på positiv trend |
| PosTrendAlert (10000) |1/0-värde som indikerar att det finns en positiv trendavvikelse baserad på ingångskänsligheten |
| NegTrendscore (Senaste) |Ett flytande tal som representerar avvikelsepoäng på negativ trend |
| NegTrendAlert (1)negtrendalert) |1/0-värde som anger att det finns en negativ trendavvikelse baserad på indatakänsligheten |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

