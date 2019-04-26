---
title: Azure Machine Learning-Avvikelseidentifiering API - Team Data Science Process
description: 'API: T för avvikelseidentifiering är ett exempel som skapats med Microsoft Azure Machine Learning som identifierar avvikelser i tidsseriedata med numeriska värden som är jämnt fördelade i tid.'
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 06/05/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: b67028562a2c377e1dd99635bdf04cad14782341
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60326588"
---
# <a name="machine-learning-anomaly-detection-api"></a>Machine Learning-Avvikelseidentifiering API

> [!NOTE]
> Den här artikeln är under underhåll. Vi rekommenderar att du kan använda den [Avvikelseidentifiering detektor API-tjänsten](https://azure.microsoft.com/en-us/services/cognitive-services/anomaly-detector/) drivs av ett galleri för Machine Learning-algoritmer under Azure Cognitive Services för att identifiera avvikelser från företag, drift, och IoT-mått.

## <a name="overview"></a>Översikt
[API: T för avvikelseidentifiering](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) är ett exempel som skapats med Azure Machine Learning som identifierar avvikelser i tidsseriedata med numeriska värden som är jämnt fördelade i tid.

Detta API kan identifiera följande typer av avvikande mönster i tidsseriedata:

* **Positiva och negativa trender**: Till exempel när du övervakar minnesanvändningen vid databehandling trenden kan vara av intresse som det kan tyda på en minnesläcka
* **Ändringar i dynamiska värdeintervall**: Till exempel när du övervakar undantag som utlöses av en tjänst i molnet, ändringar i dynamiska värdeintervall kan tyda på instabilitet i hälsotillståndet för tjänsten, och
* **Toppar och dalar**: Till exempel när du övervakar antalet misslyckade inloggningar i en tjänst eller antalet utcheckningar på en webbplats för e-handel, kan toppar och dalar tyda på onormalt beteende.

Dessa machine learning detektorerna spåra ändringar i värden över tid och rapporten löpande ändringar i deras värden som avvikelseidentifiering resultat. De behöver inte ad hoc tröskelvärdet justering och deras resultat kan användas till att styra andel falska positiva identifieringar. Avvikelseidentifiering API är användbart i flera scenarier som tjänstövervakning genom att spåra KPI: er med tiden, övervakning av programanvändning via mått, till exempel antal sökningar, antal klick, prestandaövervakning via räknare t.ex. minne, CPU, filen läser osv. med tiden.

Avvikelseidentifiering erbjudandet kommer med bra verktyg för att komma igång.

* Den [webbprogram](https://anomalydetection-aml.azurewebsites.net/) hjälper dig att utvärdera och visualiserar resultatet av API: er för avvikelseidentifiering på dina data.

> [!NOTE]
> Försök **insikter för IT-avvikelser lösning** drivs av [detta API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>API-distribution
Om du vill använda API: et, måste du distribuera den till din Azure-prenumeration där det ska finnas som en Azure Machine Learning-webbtjänst.  Du kan göra detta från den [Azure AI-galleriet](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Detta distribuerar två Azure Machine Learning studio-webbtjänster (och deras relaterade resurser) till din Azure-prenumeration – en för avvikelseidentifiering med säsongsberoende identifiering och en utan säsongsberoende identifiering.  När distributionen är klar kommer du att kunna hantera dina API: er från den [Azure Machine Learning studio-webbtjänster](https://services.azureml.net/webservices/) sidan.  Från den här sidan kommer du att kunna hitta dina slutpunkter, API-nycklar, samt exempelkod för att anropa API: et.  Mer detaljerade instruktioner finns [här](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Skalning av API: et
Som standard har distributionen en kostnadsfri Dev/Test faktureringsplan som innehåller 1 000 transaktioner per månad och 2 beräkning timmar/månad.  Du kan uppgradera till en annan plan enligt dina behov.  Information om priser för olika planerna finns [här](https://azure.microsoft.com/pricing/details/machine-learning/) under ”Web API för produktion prissättningen”.

## <a name="managing-aml-plans"></a>Hantera AML-planer
Du kan hantera din faktureringsplan [här](https://services.azureml.net/plans/).  Plannamnet baseras på resursgruppens namn du valde när du distribuerar API: et plus en sträng som är unik för din prenumeration.  Instruktioner om hur du uppgraderar din plan finns [här](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) under avsnittet ”Hantera faktureringsplaner”.

## <a name="api-definition"></a>API-definition
Webbtjänsten innehåller ett REST-baserad API via HTTPS som kan användas på olika sätt, inklusive ett webbprogram eller mobila program, R, Python, Excel, osv.  Du skickar din time series-data till den här tjänsten via ett REST API-anrop och den körs en kombination av de tre avvikelseidentifiering typer som beskrivs nedan.

## <a name="calling-the-api"></a>Anropa API
För att anropa API behöver veta slutpunktens plats och API-nyckel.  Båda dessa tillsammans med exempelkod för att anropa API: et, är tillgängliga från den [Azure Machine Learning studio-webbtjänster](https://services.azureml.net/webservices/) sidan.  Navigera till önskade API och klicka sedan på fliken ”förbruka” för att hitta dem.  Observera att du kan anropa API: et som Swagger-API (dvs. med URL-parameter `format=swagger`) eller som en icke - Swagger API (d.v.s. utan den `format` URL-parameter).  Exempelkoden använder Swagger-format.  Nedan visas ett exempel begäranden och svar i icke-Swagger-format.  Dessa exempel är till slutpunkten säsongsberoende.  Icke-säsongsberoende slutpunkten är liknande.

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
Observera att om du vill se den `ColumnNames` fält, måste du inkludera `details=true` som en URL-parameter i din begäran.  Finns i tabellen nedan för betydelse bakom respektive fält.

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
Bilden nedan visar ett exempel på avvikelser som poäng API: et kan identifiera. Den här gången-serien har 2 distinkta ändras och 3 toppar. De röda punkterna visar den tid då nivån ändring identifieras, medan de svarta punkterna visa de identifierade toppar.
![Poäng API][1]

### <a name="detectors"></a>Detektorerna
Avvikelseidentifiering API stöder detektorerna i 3 olika kategorier. Information om specifika indataparametrar och utdata för varje detektor finns i följande tabell.

| Detektor kategori | Detektor | Beskrivning | Indataparametrar | Utdata |
| --- | --- | --- | --- | --- |
| Topp detektorerna |TSpike detektor |Identifiera toppar och dalar baserat på mycket värdena kommer från första och tredje Kvartiler |*tspikedetector.sensitivity:* tar heltal i intervallet 1-10 standard: 3. Högre värden ska fånga upp mer extrema värden, vilket gör det mindre känsliga |TSpike: binära värden – '1' om en topp/dip upptäcks, '0' annars |
| Topp detektorerna | ZSpike detektor |Identifiera toppar och dalar baserat på hur långt datapoints är medelvärdet för |*zspikedetector.sensitivity:* ta heltal i intervallet 1-10 standard: 3. Högre värden ska fånga upp mer extrema värden, vilket gör det mindre känsliga |ZSpike: binära värden – '1' om en topp/dip upptäcks, '0' annars |
| Långsam Trend detektor |Långsam Trend detektor |Identifiera långsamma positivt trend enligt set-känslighet |*trenddetector.sensitivity:* tröskelvärdet på detektor poäng (standard: 3,25, 3,25 – 5 är ett rimligt intervall att välja det från; Ju högre det mindre känsligt) |tscore: flytande tal som representerar avvikelsepoäng på trend |
| Nivåändring detektorerna | Dubbelriktad nivå ändringen detektor |Identifiera både uppåt och nedåt nivåändring enligt set-känslighet |*bileveldetector.sensitivity:* tröskelvärdet på detektor poäng (standard: 3,25, 3,25 – 5 är ett rimligt intervall att välja det från; Ju högre det mindre känsligt) |rpscore: flytande tal som representerar avvikelsepoäng på uppåt och nedåt nivåändring |

### <a name="parameters"></a>Parametrar
Mer detaljerad information om dessa indataparametrar visas i tabellen nedan:

| Indataparametrar | Beskrivning | Standardinställningen | Typ | Giltigt intervall | Föreslagna intervall |
| --- | --- | --- | --- | --- | --- |
| detectors.historywindow |Historik (i antal datapunkter) som används för avvikelseidentifiering poäng beräkning |500 |heltal |10-2000 |Time series-beroende |
| detectors.spikesdips | Om du vill kontrollera endast toppar, dalar eller båda |Båda |Räkna upp |Båda, toppar, dalar |Båda |
| bileveldetector.sensitivity |Känslighet för dubbelriktad nivåändring detektor. |3.25 |double |Ingen |3,25-5 (färre värden innebära känsligare) |
| trenddetector.sensitivity |Känslighet för positivt trend detektor. |3.25 |double |Ingen |3,25-5 (färre värden innebära känsligare) |
| tspikedetector.sensitivity |Känslighet för TSpike detektor |3 |heltal |1-10 |3-5 (färre värden innebära känsligare) |
| zspikedetector.sensitivity |Känslighet för ZSpike detektor |3 |heltal |1-10 |3-5 (färre värden innebära känsligare) |
| postprocess.tailRows |Antal senaste datapunkter som ska behållas i utdataresultat |0 |heltal |0 (Håll alla datapunkter), eller ange många saker att resultat |Gäller inte |

### <a name="output"></a>Resultat
API: et körs alla detektorerna på dina tidsseriedata och returnerar poängen för avvikelseidentifiering och binära topp indikatorer för varje punkt i tiden. I tabellen nedan visas utdata från API: et.

| Utdata | Beskrivning |
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
Följande bild visar ett exempel på avvikelser som har identifierats i en säsongens tidsserie. Tidsserien har en topp (i 1 svart punkt), två dalar (2 svart punkt och en i slutet) och en nivåändring (röd punkt). Observera att båda dip mitt i tidsserien och nivån ändringen endast går när säsongens komponenter har tagits bort från serien.
![Säsongsberoende API][2]

### <a name="detectors"></a>Detektorerna
Detektorerna i säsongsberoende slutpunkten liknar som i icke-säsongsberoende slutpunkten, men med något annorlunda parameternamn (listas nedan).

### <a name="parameters"></a>Parametrar

Mer detaljerad information om dessa indataparametrar visas i tabellen nedan:

| Indataparametrar | Beskrivning | Standardinställningen | Typ | Giltigt intervall | Föreslagna intervall |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Sammanställningsintervall i sekunder för sammanställning av indata tidsserier |0 (Ingen aggregering utförs) |heltal |0: hoppa annars över aggregering, > 0 |5 minuter till 1 dag ställs time series-beroende |
| preprocess.aggregationFunc |Funktion som används för att sammanställa data i den angivna AggregationInterval |medelvärde |Räkna upp |Mean, sum, längd |Gäller inte |
| preprocess.replaceMissing |Värden som används för att sedan imputera data som saknas |lkv (senaste kända värdet) |Räkna upp |noll, lkv, medelvärde |Gäller inte |
| detectors.historywindow |Historik (i antal datapunkter) som används för avvikelseidentifiering poäng beräkning |500 |heltal |10-2000 |Time series-beroende |
| detectors.spikesdips | Om du vill kontrollera endast toppar, dalar eller båda |Båda |Räkna upp |Båda, toppar, dalar |Båda |
| bileveldetector.sensitivity |Känslighet för dubbelriktad nivåändring detektor. |3.25 |double |Ingen |3,25-5 (färre värden innebära känsligare) |
| postrenddetector.sensitivity |Känslighet för positivt trend detektor. |3.25 |double |Ingen |3,25-5 (färre värden innebära känsligare) |
| negtrenddetector.sensitivity |Känslighet för försämringen detektor. |3.25 |double |Ingen |3,25-5 (färre värden innebära känsligare) |
| tspikedetector.sensitivity |Känslighet för TSpike detektor |3 |heltal |1-10 |3-5 (färre värden innebära känsligare) |
| zspikedetector.sensitivity |Känslighet för ZSpike detektor |3 |heltal |1-10 |3-5 (färre värden innebära känsligare) |
| seasonality.enable |Om säsongsberoende analys är som ska utföras |true |boolesk |SANT, FALSKT |Time series-beroende |
| seasonality.numSeasonality |Maximalt antal periodiska cykler ska identifieras |1 |heltal |1, 2 |1-2 |
| seasonality.transform |Om säsongsbaserade (och) trend komponenter bör tas bort innan du tillämpar avvikelseidentifiering |deseason |Räkna upp |Ingen, deseason deseasontrend |Gäller inte |
| postprocess.tailRows |Antal senaste datapunkter som ska behållas i utdataresultat |0 |heltal |0 (Håll alla datapunkter), eller ange många saker att resultat |Gäller inte |

### <a name="output"></a>Resultat
API: et körs alla detektorerna på dina tidsseriedata och returnerar poängen för avvikelseidentifiering och binära topp indikatorer för varje punkt i tiden. I tabellen nedan visas utdata från API: et.

| Utdata | Beskrivning |
| --- | --- |
| Tid |Tidsstämplar från rådata och sammanställda (och/eller) beräknade data om aggregering (och/eller) saknas data uppräkning tillämpas |
| OriginalData |Värden från rådata och sammanställda (och/eller) beräknade data om aggregering (och/eller) saknas data uppräkning tillämpas |
| ProcessedData |Något av följande: <ul><li>Säsongsvis justeras tidsserier om betydande säsongsberoende har identifierats och deseason alternativ som valts.</li><li>säsongsvis justeras och detrended tidsserier om betydande säsongsberoende har identifierats och deseasontrend alternativet</li><li>i annat fall är detta samma som OriginalData</li> |
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

