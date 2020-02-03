---
title: Azure Machine Learning-Avvikelseidentifiering API - Team Data Science Process
description: 'API: T för avvikelseidentifiering är ett exempel som skapats med Microsoft Azure Machine Learning som identifierar avvikelser i tidsseriedata med numeriska värden som är jämnt fördelade i tid.'
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721888"
---
# <a name="machine-learning-anomaly-detection-api"></a>Machine Learning-Avvikelseidentifiering API

> [!NOTE]
> Det här objektet är under underhåll. Vi rekommenderar att du använder [API-tjänsten för avvikelse detektor](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) som drivs av ett galleri med Machine Learning algoritmer under Azure Cognitive Services för att identifiera avvikelser från affärs-, drift-och IoT-mått.

## <a name="overview"></a>Översikt
[API för avvikelse identifiering](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) är ett exempel som skapats med Azure Machine Learning som identifierar avvikelser i Time Series-data med numeriska värden som är jämnt fördelade i tid.

Detta API kan identifiera följande typer av avvikande mönster i tidsseriedata:

* **Positiva och negativa trender**: om du till exempel övervakar minnes användningen i data behandling kan en uppåtgående trend vara intressant eftersom det kan vara en indikation på en minnes läcka.
* **Ändringar i det dynamiska värde intervallet**: om du till exempel övervakar undantagen som har utlösts av en moln tjänst kan alla ändringar i det dynamiska värde intervallet vara instabilitet i tjänstens hälso tillstånd.
* **Toppar och DIP**: om du till exempel övervakar antalet inloggnings försök i en tjänst eller antalet utcheckningar på en e-handelsplats kan toppar eller dip tyda på onormalt beteende.

Dessa machine learning detektorerna spåra ändringar i värden över tid och rapporten löpande ändringar i deras värden som avvikelseidentifiering resultat. De behöver inte ad hoc tröskelvärdet justering och deras resultat kan användas till att styra andel falska positiva identifieringar. Avvikelseidentifiering API är användbart i flera scenarier som tjänstövervakning genom att spåra KPI: er med tiden, övervakning av programanvändning via mått, till exempel antal sökningar, antal klick, prestandaövervakning via räknare t.ex. minne, CPU, filen läser osv. med tiden.

Avvikelseidentifiering erbjudandet kommer med bra verktyg för att komma igång.

* [Webb programmet](https://anomalydetection-aml.azurewebsites.net/) hjälper dig att utvärdera och visualisera resultaten av API: er för avvikelse identifiering på dina data.

> [!NOTE]
> Testa **den avvikande Insights-lösning** som drivs av [detta API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>API-distribution
Om du vill använda API: et, måste du distribuera den till din Azure-prenumeration där det ska finnas som en Azure Machine Learning-webbtjänst.  Du kan göra detta från [Azure AI Gallery](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Detta distribuerar två Azure Machine Learning Studio (klassiska) webb tjänster (och deras relaterade resurser) till din Azure-prenumeration – en för avvikelse identifiering med säsongs beroende-identifiering och en utan säsongs beroende identifiering.  När distributionen har slutförts kommer du att kunna hantera dina API: er från sidan [Azure Machine Learning Studio (klassiska) webb tjänster](https://services.azureml.net/webservices/) .  Från den här sidan kommer du att kunna hitta dina slutpunkter, API-nycklar, samt exempelkod för att anropa API: et.  Mer detaljerade instruktioner finns [här](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Skalning av API: et
Som standard har din distribution en kostnads fri fakturerings plan för utveckling/testning som innehåller 1 000 transaktioner/månad och 2 beräknings timmar/månad.  Du kan uppgradera till en annan plan enligt dina behov.  Information om prissättningen för olika planer finns [här](https://azure.microsoft.com/pricing/details/machine-learning/) under "Production Web API-prissättning".

## <a name="managing-aml-plans"></a>Hantera AML-planer
Du kan hantera din fakturerings plan [här](https://services.azureml.net/plans/).  Plannamnet baseras på resursgruppens namn du valde när du distribuerar API: et plus en sträng som är unik för din prenumeration.  Anvisningar om hur du uppgraderar planen finns [här](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) under avsnittet "hantera fakturerings planer".

## <a name="api-definition"></a>API-definition
Webb tjänsten tillhandahåller ett REST-baserat API över HTTPS som kan användas på olika sätt, till exempel ett webb-eller mobil program, R, python, Excel osv.  Du skickar dina Time Series-data till den här tjänsten via ett REST API samtal och kör en kombination av de tre avvikelse typerna som beskrivs nedan.

## <a name="calling-the-api"></a>Anropa API
För att anropa API behöver veta slutpunktens plats och API-nyckel.  Dessa två krav, tillsammans med exempel kod för att anropa API: n, är tillgängliga på sidan [Azure Machine Learning Studio (klassiska) webb tjänster](https://services.azureml.net/webservices/) .  Navigera till önskade API och klicka sedan på fliken ”förbruka” för att hitta dem.  Du kan anropa API: et som ett Swagger-API (det vill säga med URL-parametern `format=swagger`) eller som ett icke-Swagger-API (det vill säga utan parametern `format` URL).  Exempelkoden använder Swagger-format.  Nedan visas ett exempel begäranden och svar i icke-Swagger-format.  Dessa exempel är till slutpunkten säsongsberoende.  Icke-säsongsberoende slutpunkten är liknande.

### <a name="sample-request-body"></a>Exemplet Begärandetexten
Begäran innehåller två objekt: `Inputs` och `GlobalParameters`.  I exemplet begäran nedan, vissa parametrar skickas uttryckligen medan andra inte (Rulla ned för en fullständig lista över parametrar för varje slutpunkt).  Parametrar som inte skickas uttryckligen i begäran ska använda standardvärdena som anges nedan.

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
Du måste ta med `details=true` som en URL-parameter i din begäran för att kunna se `ColumnNames` fältet.  Finns i tabellen nedan för betydelse bakom respektive fält.

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
Poäng-API används för att köra identifiering av avvikelser på icke säsongsbundna time series-data. API: et kör ett antal avvikelseidentifiering detektorerna på data och returnerar sina avvikelseidentifiering poäng.
Bilden nedan visar ett exempel på avvikelser som poäng API: et kan identifiera. Den här tids serien har två distinkta nivå ändringar och tre toppar. De röda punkterna visar den tid då nivån ändring identifieras, medan de svarta punkterna visa de identifierade toppar.
![score API][1]

### <a name="detectors"></a>Detektorerna
API: t för avvikelse identifiering stöder identifierare i tre breda kategorier. Information om specifika indataparametrar och utdata för varje detektor finns i följande tabell.

| Detektor kategori | Detektor | Description | Indataparametrar | Utdata |
| --- | --- | --- | --- | --- |
| Topp detektorerna |TSpike detektor |Identifiera toppar och dalar baserat på mycket värdena kommer från första och tredje Kvartiler |*tspikedetector. känslighet:* tar heltals värde i intervallet 1-10, standard: 3; Högre värden kommer att fånga mer extrema värden, vilket gör det mindre känsligt |TSpike: binära värden – '1' om en topp/dip upptäcks, '0' annars |
| Topp detektorerna | ZSpike detektor |Identifiera toppar och dalar baserat på hur långt datapoints är medelvärdet för |*zspikedetector. känslighet:* ta heltals värde i intervallet 1-10, standard: 3; Högre värden kommer att fånga fler extrema värden vilket gör det mindre känsligt |ZSpike: binära värden – '1' om en topp/dip upptäcks, '0' annars |
| Långsam Trend detektor |Långsam Trend detektor |Identifiera långsamma positivt trend enligt set-känslighet |*trenddetector. känslighet:* tröskelvärde för detektorernas Poäng (standard: 3,25, 3,25 – 5 är ett rimligt intervall att välja bland. Ju högre mindre känsligt) |tscore: flytande tal som representerar avvikelsepoäng på trend |
| Nivåändring detektorerna | Dubbelriktad nivå ändringen detektor |Identifiera både uppåt och nedåt nivåändring enligt set-känslighet |*bileveldetector. känslighet:* tröskelvärde för detektorernas Poäng (standard: 3,25, 3,25 – 5 är ett rimligt intervall att välja bland. Ju högre mindre känsligt) |rpscore: flytande tal som representerar avvikelsepoäng på uppåt och nedåt nivåändring |

### <a name="parameters"></a>Parametrar
Mer detaljerad information om dessa indataparametrar visas i tabellen nedan:

| Indataparametrar | Description | Standardinställningen | Typ | Giltigt intervall | Föreslagna intervall |
| --- | --- | --- | --- | --- | --- |
| detectors.historywindow |Historik (i antal datapunkter) som används för avvikelseidentifiering poäng beräkning |500 |integer |10-2000 |Time series-beroende |
| detectors.spikesdips | Om du vill kontrollera endast toppar, dalar eller båda |Båda |Räkna upp |Båda, toppar, dalar |Båda |
| bileveldetector.sensitivity |Känslighet för dubbelriktad nivåändring detektor. |3.25 |double |Inga |3,25-5 (färre värden innebära känsligare) |
| trenddetector.sensitivity |Känslighet för positivt trend detektor. |3.25 |double |Inga |3,25-5 (färre värden innebära känsligare) |
| tspikedetector.sensitivity |Känslighet för TSpike detektor |3 |integer |1-10 |3-5 (färre värden innebära känsligare) |
| zspikedetector.sensitivity |Känslighet för ZSpike detektor |3 |integer |1-10 |3-5 (färre värden innebära känsligare) |
| postprocess.tailRows |Antal senaste datapunkter som ska behållas i utdataresultat |0 |integer |0 (Håll alla datapunkter), eller ange många saker att resultat |E.t. |

### <a name="output"></a>Resultat
API: et körs alla detektorerna på dina tidsseriedata och returnerar poängen för avvikelseidentifiering och binära topp indikatorer för varje punkt i tiden. I tabellen nedan visas utdata från API: et.

| Utdata | Description |
| --- | --- |
| Tid |Tidsstämplar från rådata och sammanställda (och/eller) beräknade data om aggregering (och/eller) saknas data uppräkning tillämpas |
| Data |Värden från rådata och sammanställda (och/eller) beräknade data om aggregering (och/eller) saknas data uppräkning tillämpas |
| TSpike |Binärt värde som anger om en topp identifieras av TSpike detektor |
| ZSpike |Binärt värde som anger om en topp identifieras av ZSpike detektor |
| rpscore |En flytande tal som representerar avvikelser poäng på dubbelriktad nivåändring |
| rpalert |1/0-värde som anger det finns en dubbelriktad nivå ändra avvikelseidentifiering baserat på inkommande känslighet |
| tscore |En flytande tal som representerar avvikelser poäng på positivt trend |
| talert |1/0-värde som anger det är ett positivt trend avvikelseidentifiering baserat på inkommande känslighet |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
ScoreWithSeasonality-API används för att köra identifiering av avvikelser på tidsserier som har säsongens mönster. Detta API är användbar för att identifiera avvikelser på säsongens mönster.
Följande bild visar ett exempel på avvikelser som har identifierats i en säsongens tidsserie. Tids serien har en topp (den första svarta punkten), två DIP (den andra svarta punkten och en i slutet) och en förändring på nivån (röd punkt). Både DIP i mitten av tids serien och nivå ändringen är bara går när säsongs komponenterna har tagits bort från serien.
![säsongs beroende-API][2]

### <a name="detectors"></a>Detektorerna
Detektorerna i säsongsberoende slutpunkten liknar som i icke-säsongsberoende slutpunkten, men med något annorlunda parameternamn (listas nedan).

### <a name="parameters"></a>Parametrar

Mer detaljerad information om dessa indataparametrar visas i tabellen nedan:

| Indataparametrar | Description | Standardinställningen | Typ | Giltigt intervall | Föreslagna intervall |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Sammanställningsintervall i sekunder för sammanställning av indata tidsserier |0 (Ingen aggregering utförs) |integer |0: hoppa annars över aggregering, > 0 |5 minuter till 1 dag ställs time series-beroende |
| preprocess.aggregationFunc |Funktion som används för att sammanställa data i den angivna AggregationInterval |medelvärde |Räkna upp |Mean, sum, längd |E.t. |
| preprocess.replaceMissing |Värden som används för att sedan imputera data som saknas |lkv (senaste kända värdet) |Räkna upp |noll, lkv, medelvärde |E.t. |
| detectors.historywindow |Historik (i antal datapunkter) som används för avvikelseidentifiering poäng beräkning |500 |integer |10-2000 |Time series-beroende |
| detectors.spikesdips | Om du vill kontrollera endast toppar, dalar eller båda |Båda |Räkna upp |Båda, toppar, dalar |Båda |
| bileveldetector.sensitivity |Känslighet för dubbelriktad nivåändring detektor. |3.25 |double |Inga |3,25-5 (färre värden innebära känsligare) |
| postrenddetector.sensitivity |Känslighet för positivt trend detektor. |3.25 |double |Inga |3,25-5 (färre värden innebära känsligare) |
| negtrenddetector.sensitivity |Känslighet för försämringen detektor. |3.25 |double |Inga |3,25-5 (färre värden innebära känsligare) |
| tspikedetector.sensitivity |Känslighet för TSpike detektor |3 |integer |1-10 |3-5 (färre värden innebära känsligare) |
| zspikedetector.sensitivity |Känslighet för ZSpike detektor |3 |integer |1-10 |3-5 (färre värden innebära känsligare) |
| seasonality.enable |Om säsongsberoende analys är som ska utföras |true |Booleskt värde |true, false |Time series-beroende |
| seasonality.numSeasonality |Maximalt antal periodiska cykler ska identifieras |1 |integer |1, 2 |1-2 |
| seasonality.transform |Om säsongsbaserade (och) trend komponenter bör tas bort innan du tillämpar avvikelseidentifiering |deseason |Räkna upp |Ingen, deseason deseasontrend |E.t. |
| postprocess.tailRows |Antal senaste datapunkter som ska behållas i utdataresultat |0 |integer |0 (Håll alla datapunkter), eller ange många saker att resultat |E.t. |

### <a name="output"></a>Resultat
API: et körs alla detektorerna på dina tidsseriedata och returnerar poängen för avvikelseidentifiering och binära topp indikatorer för varje punkt i tiden. I tabellen nedan visas utdata från API: et.

| Utdata | Description |
| --- | --- |
| Tid |Tidsstämplar från rådata och sammanställda (och/eller) beräknade data om aggregering (och/eller) saknas data uppräkning tillämpas |
| OriginalData |Värden från rådata och sammanställda (och/eller) beräknade data om aggregering (och/eller) saknas data uppräkning tillämpas |
| ProcessedData |Något av följande alternativ: <ul><li>Säsongsvis justeras tidsserier om betydande säsongsberoende har identifierats och deseason alternativ som valts.</li><li>säsongsvis justeras och detrended tidsserier om betydande säsongsberoende har identifierats och deseasontrend alternativet</li><li>i annat fall är det här alternativet detsamma som OriginalData</li> |
| TSpike |Binärt värde som anger om en topp identifieras av TSpike detektor |
| ZSpike |Binärt värde som anger om en topp identifieras av ZSpike detektor |
| BiLevelChangeScore |En flytande tal som representerar avvikelser poäng på nivåändring |
| BiLevelChangeAlert |1/0 värde som anger det är en nivåändring avvikelseidentifiering baserat på inkommande känslighet |
| PosTrendScore |En flytande tal som representerar avvikelser poäng på positivt trend |
| PosTrendAlert |1/0-värde som anger det är ett positivt trend avvikelseidentifiering baserat på inkommande känslighet |
| NegTrendScore |En flytande tal som representerar avvikelser poäng på negativa utvecklingen |
| NegTrendAlert |1/0-värde som anger det är ett negativt trend avvikelseidentifiering baserat på inkommande känslighet |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

