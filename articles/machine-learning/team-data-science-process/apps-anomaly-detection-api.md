---
title: Azure Machine Learning Avvikelseidentifiering API | Microsoft Docs
description: Avvikelseidentifiering identifiering API är ett exempel som skapats med Microsoft Azure Machine Learning som identifierar avvikelser i tid series-data med numeriska värden som jämnt fördelade i tid.
services: machine-learning
documentationcenter: ''
author: alokkirpal
manager: jhubbard
editor: cgronlun
ms.assetid: 52fafe1f-e93d-47df-a8ac-9a9a53b60824
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/05/2017
ms.author: alok
ms.openlocfilehash: 7633d2bd15e5bc4620a4980623f3883c162f4331
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="machine-learning-anomaly-detection-api"></a>Maskininlärning Avvikelseidentifiering API
## <a name="overview"></a>Översikt
[Avvikelseidentifiering identifiering API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) är ett exempel som skapats med Azure Machine Learning som identifierar avvikelser i tid series-data med numeriska värden som jämnt fördelade i tid.

Detta API kan identifiera följande typer av avvikande mönster i tid series-data:

* **Positiva och negativa trender**: till exempel när övervakning minnesanvändning i computing trenden kan vara av intresse eftersom den kan vara en indikation på en minnesläcka
* **Ändringar i dynamiskt värdeintervallet**: till exempel när du övervakar undantag som utlöses av en tjänst i molnet, ändringar i dynamiskt värdeintervallet kan tyda på instabilitet i hälsotillståndet för tjänsten, och
* **Ger spikar i diagrammet och sjunker**: till exempel när du övervakar antal misslyckade inloggningar i en tjänst eller antal utcheckningar i en e-handelsplats toppar eller korta kan tyda på onormalt beteende.

Dessa machine learning detektorerna spåra ändringarna i värden över tid och rapporten pågående ändringar i deras värden som avvikelseidentifiering resultat. De kräver inte ad hoc tröskelvärdet justera och deras resultat kan användas till att styra falska positiva satsen. Avvikelseidentifiering API är användbart i flera scenarier som övervakning genom att spåra KPI: er över tiden, övervakning av programanvändning via mätvärden, till exempel antal sökningar, antal klick, prestandaövervakning via räknare som minne, CPU, filen läser osv över tid.

Avvikelseidentifiering erbjudandet ingår användbara verktyg för att komma igång.

* Den [webbprogrammet](http://anomalydetection-aml.azurewebsites.net/) hjälper dig att utvärdera och visualisera resultat av avvikelseidentifiering API: er på dina data.

> [!NOTE]
> Försök **IT Avvikelseidentifiering Insights-lösningen** drivs av [detta API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
> 
> Att hämta den här slutpunkt till slutpunkt-lösningen distribueras till din Azure-prenumeration <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank"> **börja här >**</a>
> 
>

## <a name="api-deployment"></a>API-distribution
För att kunna använda API: et, måste du distribuera den till din Azure-prenumeration där den kommer att finnas som en Azure Machine Learning-webbtjänst.  Du kan göra detta från den [Azure AI-galleriet](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Detta distribuerar två AzureML-webbtjänster (och deras relaterade resurser) till Azure-prenumeration - en för avvikelseidentifiering med säsongsvärdet identifiering och utan säsongsvärdet identifiering.  När distributionen är klar, kommer du att kunna hantera dina API: er från den [AzureML-webbtjänster](https://services.azureml.net/webservices/) sidan.  Du kommer att kunna hitta din slutpunkt platser, API-nycklar, samt kodexempel för att anropa API: et för den här sidan.  Mer detaljerade instruktioner finns [här](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Skalning API: et
Som standard har distributionen ett ledigt utveckling och testning faktureringsavtal som innehåller 1 000 transaktioner per månad och 2 beräkning timmar per månad.  Du kan uppgradera till en annan plan enligt dina behov.  Information om priser av olika planer finns [här](https://azure.microsoft.com/pricing/details/machine-learning/) under ”priser för produktion webb-API”.

## <a name="managing-aml-plans"></a>Hantera AML-planer 
Du kan hantera faktureringsavtalet [här](https://services.azureml.net/plans/).  Namn för energischema kommer att baseras på resursgruppens namn du väljer när du distribuerar API: et plus en sträng som är unik för din prenumeration.  Instruktioner för hur du uppgraderar din plan finns [här](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) under avsnittet ”Hantera fakturering planer”.

## <a name="api-definition"></a>API-definition
Webbtjänsten innehåller ett REST-baserad API över HTTPS som kan användas på olika sätt, inklusive en webbplats eller ett mobilt program, R, Python, Excel, osv.  Tid seriens data skickas till den här tjänsten via ett REST API-anrop och den körs en kombination av tre avvikelseidentifiering typer som beskrivs nedan.

## <a name="calling-the-api"></a>Anropar API: et
För att anropa API: et, behöver du veta endpoint plats och API-nyckel.  Båda dessa tillsammans med exempelkod för att anropa API, är tillgängliga från den [AzureML-webbtjänster](https://services.azureml.net/webservices/) sidan.  Navigera till önskade API: et och klicka sedan på fliken ”förbruka” för att hitta dem.  Observera att du kan anropa API som Swagger API (dvs. med URL-parametern `format=swagger`) eller som en icke - Swagger API (dvs. utan den `format` URL-parametern).  Exempelkoden använder Swagger-format.  Nedan visas ett exempel förfrågan och svar i icke-Swagger-format.  Exemplen är att säsongsvärdet slutpunkten.  Ej säsongsvärdet slutpunkten är liknande.

### <a name="sample-request-body"></a>Exempel Begärandetexten
Begäran innehåller två objekt: `Inputs` och `GlobalParameters`.  I exemplet begäran nedan, vissa parametrar skickas uttryckligen medan andra inte (Rulla ned till en fullständig lista över parametrar för varje slutpunkt).  Parametrar som inte skickas explicit i förfrågan använder standardvärden anges nedan.

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
Observera att för att kunna se den `ColumnNames` fältet, måste du inkludera `details=true` som en URL-parametern i begäran.  Finns i tabellerna nedan för betydelse bakom var och en av dessa fält.

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
Poäng API används för att köra avvikelseidentifiering på icke-säsongsbaserade tid series-data. API: et kör ett antal avvikelseidentifiering detektorerna på data och returnerar sina avvikelseidentifiering poäng. Bilden nedan visar ett exempel på avvikelser som kan identifiera Score-API. Den här tidsserier har 2 distinkta ändras och 3 toppar. De röda punkterna visar den tid då den nivå ändringen identifieras när de svarta prickar visa identifierade toppar.
![Poäng API][1]

### <a name="detectors"></a>Detektorerna
Avvikelseidentifiering API stöder detektorerna i 3 kategorier. Information om specifika indataparametrar och utdata för varje detektor finns i följande tabell.

| Detektor kategori | Detektor | Beskrivning | Indataparametrar | Utdata |
| --- | --- | --- | --- | --- |
| Topp detektorerna |TSpike detektor |Identifiera toppar och korta baserat på långt värdena kommer från första och tredje Kvartiler |*tspikedetector.sensitivity:* tar heltal i intervallet 1-10 standard: 3; Högre värden ska fånga mer extrema värden, vilket gör det mindre känsliga |TSpike: binära värden – '1' om topp/dip upptäcks, '0' annars |
| Topp detektorerna | ZSpike detektor |Identifiera toppar och korta baserat på hur långt datapoints är medelvärdet för |*zspikedetector.sensitivity:* ta heltal i intervallet 1-10 standard: 3; Högre värden ska fånga mer extrema värden, vilket gör det mindre känsliga |ZSpike: binära värden – '1' om topp/dip upptäcks, '0' annars | |
| Långsam Trend detektor |Långsam Trend detektor |Identifiera långsamma positivt trend enligt set känslighet |*trenddetector.sensitivity:* tröskelvärdet för detektor poäng (standard: 3,25, 3,25 – 5 är ett rimligt intervall som du vill markera från; Ju högre det mindre känslig) |tscore: flytande tal som representerar avvikelseidentifiering poäng på trend |
| Förändring av detektorerna | Dubbelriktad nivå ändra detektor |Identifiera både uppåt och nedåt förändring enligt set känslighet |*bileveldetector.sensitivity:* tröskelvärdet för detektor poäng (standard: 3,25, 3,25 – 5 är ett rimligt intervall som du vill markera från; Ju högre det mindre känslig) |rpscore: flytande tal som representerar avvikelseidentifiering poäng på förändring av uppåt och nedåt | |

### <a name="parameters"></a>Parametrar
Mer detaljerad information om dessa indataparametrar anges i tabellen nedan:

| Indataparametrar | Beskrivning | Standardinställningen | Typ | Giltigt intervall | Föreslagna intervallet |
| --- | --- | --- | --- | --- | --- |
| detectors.historyWindow |Historik (i antal datapunkter) som används för avvikelseidentifiering poäng beräkning |500 |heltal |10-2000 |Tidsserie beroende |
| detectors.spikesdips | Om du vill identifiera endast ger spikar i diagrammet, endast korta eller båda |Både |Räkna upp |Båda toppar, korta |Både |
| bileveldetector.sensitivity |Känslighet för dubbelriktad nivå ändra detektor. |3.25 |double |Ingen |3,25 5 (färre värden innebär mer känslig) |
| trenddetector.sensitivity |Känslighet för positivt trend detektor. |3.25 |double |Ingen |3,25 5 (färre värden innebär mer känslig) |
| tspikedetector.sensitivity |Känslighet för TSpike detektor |3 |heltal |1-10 |3-5 (färre värden innebär mer känslig) |
| zspikedetector.sensitivity |Känslighet för ZSpike detektor |3 |heltal |1-10 |3-5 (färre värden innebär mer känslig) |
| postprocess.tailRows |Antal senaste datapunkter som ska behållas i resultatet |0 |heltal |0 (Håll alla datapunkter), eller ange antalet punkter som ska behållas i resultaten |Gäller inte |

### <a name="output"></a>Resultat
API: et körs alla detektorerna på gång series-data och returnerar avvikelseidentifiering resultat och binära topp indikatorer för varje punkt i tiden. I tabellen nedan visas utdata från API: et. 

| Utdata | Beskrivning |
| --- | --- |
| Tid |Tidsstämplar från rådata och aggregerade (eller) beräknade data om aggregering (eller) saknas data uppräkning tillämpas |
| Data |Värden från rådata och aggregerade (eller) beräknade data om aggregering (eller) saknas data uppräkning tillämpas |
| TSpike |Binärt värde som anger om en insamling har identifierats av TSpike detektor |
| ZSpike |Binärt värde som anger om en insamling har identifierats av ZSpike detektor |
| rpscore |En flytande tal som representerar avvikelseidentifiering poängsätta på dubbelriktad förändring |
| rpalert |1/0-värde som anger har en dubbelriktad ändra avvikelseidentifiering baserat på inkommande känslighet |
| tscore |En flytande tal som representerar avvikelseidentifiering poängsätta på positivt trend |
| talert |1/0-värde som anger det är en positiv trend avvikelseidentifiering baserat på inkommande känslighet |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
ScoreWithSeasonality-API används för att köra avvikelseidentifiering på tidsserier som har säsongsbaserade mönster. Detta API är användbar för att identifiera avvikelser i säsongsbaserade mönster.  
Följande bild visar ett exempel på avvikelser som identifierats i en tillfällig tidsserier. Tidsserier har en topp (1 svart punkten), två korta (2 svart punkt och en slutet) och en förändring (röd punkt). Observera att båda dip mitt i tidsserien och nivå ändringen endast discernable när när komponenterna har tagits bort från serien.
![Säsongsvärdet API][2]

### <a name="detectors"></a>Detektorerna
Detektorerna i säsongsvärdet slutpunkten liknar som i icke-säsongsvärdet slutpunkten, men med något annorlunda parameternamn (nedan).

### <a name="parameters"></a>Parametrar

Mer detaljerad information om dessa indataparametrar anges i tabellen nedan:

| Indataparametrar | Beskrivning | Standardinställningen | Typ | Giltigt intervall | Föreslagna intervallet |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Aggregeringsintervall i sekunder för sammanställningen indata tidsserier |0 (Ingen aggregering utförs) |heltal |0: annars hoppa över aggregering, > 0 |5 minuter till 1 dag, tidsserier beroende |
| preprocess.aggregationFunc |Funktionen som används för att sammanställa data i den angivna AggregationInterval |medelvärde |Räkna upp |medelvärde, sum, längd |Gäller inte |
| preprocess.replaceMissing |Värden som används för att sedan imputera data som saknas |lkv (senaste kända värdet) |Räkna upp |noll, lkv, medelvärde |Gäller inte |
| detectors.historyWindow |Historik (i antal datapunkter) som används för avvikelseidentifiering poäng beräkning |500 |heltal |10-2000 |Tidsserie beroende |
| detectors.spikesdips | Om du vill identifiera endast ger spikar i diagrammet, endast korta eller båda |Både |Räkna upp |Båda toppar, korta |Både |
| bileveldetector.sensitivity |Känslighet för dubbelriktad nivå ändra detektor. |3.25 |double |Ingen |3,25 5 (färre värden innebär mer känslig) |
| postrenddetector.sensitivity |Känslighet för positivt trend detektor. |3.25 |double |Ingen |3,25 5 (färre värden innebär mer känslig) |
| negtrenddetector.sensitivity |Känslighet för negativa utvecklingen detektor. |3.25 |double |Ingen |3,25 5 (färre värden innebär mer känslig) |
| tspikedetector.sensitivity |Känslighet för TSpike detektor |3 |heltal |1-10 |3-5 (färre värden innebär mer känslig) |
| zspikedetector.sensitivity |Känslighet för ZSpike detektor |3 |heltal |1-10 |3-5 (färre värden innebär mer känslig) |
| seasonality.enable |Om säsongsvärdet analys ska utföras |true |boolesk |SANT, FALSKT |Tidsserie beroende |
| seasonality.numSeasonality |Maximalt antal periodiska cykler ska identifieras |1 |heltal |1, 2 |1-2 |
| seasonality.transform |Om säsongsbaserade (och) trend komponenter bör tas bort innan du tillämpar avvikelseidentifiering |deseason |Räkna upp |Ingen, deseason deseasontrend |Gäller inte |
| postprocess.tailRows |Antal senaste datapunkter som ska behållas i resultatet |0 |heltal |0 (Håll alla datapunkter), eller ange antalet punkter som ska behållas i resultaten |Gäller inte |

### <a name="output"></a>Resultat
API: et körs alla detektorerna på gång series-data och returnerar avvikelseidentifiering resultat och binära topp indikatorer för varje punkt i tiden. I tabellen nedan visas utdata från API: et. 

| Utdata | Beskrivning |
| --- | --- |
| Tid |Tidsstämplar från rådata och aggregerade (eller) beräknade data om aggregering (eller) saknas data uppräkning tillämpas |
| OriginalData |Värden från rådata och aggregerade (eller) beräknade data om aggregering (eller) saknas data uppräkning tillämpas |
| ProcessedData |Något av följande: <ul><li>Säsongsvis justeras tidsserier om betydande säsongsvärdet har identifierats och deseason alternativet;</li><li>säsongsvis justeras och detrended tidsserier om betydande säsongsvärdet har upptäckts och deseasontrend alternativ som valts</li><li>i annat fall är detta samma OriginalData</li> |
| TSpike |Binärt värde som anger om en insamling har identifierats av TSpike detektor |
| ZSpike |Binärt värde som anger om en insamling har identifierats av ZSpike detektor |
| BiLevelChangeScore |En flytande tal som representerar avvikelseidentifiering poängsätta på nivån ändring |
| BiLevelChangeAlert |1/0 värde som anger det är en förändring av avvikelseidentifiering baserat på inkommande känslighet |
| PosTrendScore |En flytande tal som representerar avvikelseidentifiering poängsätta på positivt trend |
| PosTrendAlert |1/0-värde som anger det är en positiv trend avvikelseidentifiering baserat på inkommande känslighet |
| NegTrendScore |En flytande tal som representerar avvikelseidentifiering poängsätta på negativa utvecklingen |
| NegTrendAlert |1/0-värde som anger det är en negativ trend avvikelseidentifiering baserat på inkommande känslighet |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

