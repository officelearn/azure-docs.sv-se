---
title: "Detaljerad vägledning om hur du använder Azure Machine Learning Data förberedelser Operationalization | Microsoft Docs"
description: "Det här dokumentet ger information om körning tidigare datakällor och Data förberedelser paket"
services: machine-learning
author: hughz
ms.author: cforbe
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/13/2018
ms.openlocfilehash: 76bf850df5c8f4a93268dd06f7b9254bbd2cfce6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="data-preparation-operationalization"></a>Operationalization för förberedelse av data 

## <a name="operationalization-scenario"></a>Operationalization Scenario

Följande är de olika scenarier för Operationalization för förberedelse av Data som en användare kan stöta på.

### <a name="develop-your-model-based-on-training-data"></a>Utveckla din modell utifrån träningsdata

Anta att du vill skapa och distribuera en realtid bedömningsprofil API-tjänsten. Det första steget är att utveckla en modell för resultatfunktioner baserat på vissa uppsättning övningsdata. Data Prep importerar ett urval av utbildningsdata som en ny datakälla. När data är förberedd innehåller DataPrep paketet de förberedande stegen. Användaren kan med ett konto för AzureML-experiment, iterera på en modell för maskininlärning för att generera etiketter för varje indata i utbildning data.

Användaren återgår till DataPrep paketet att förbereda data i ett nytt sätt och spara de här stegen som funktionerna i data behöver uppdateras. Iteration processen för att generera nya funktioner och modifiera sin maskininlärningsmodell fortsätter tills deras testdata poäng korrekt i modellen. 

### <a name="deploy-your-model-to-the-azure-model-management-service"></a>Distribuera din modell Azure modellen Management Service

Nu har du kunddata som du skulle vilja poängsätta i realtid. Med Azure modellen Management-tjänsten kan distribuera du den här modellen från AzureML Python CLI som en tjänst. Den distribuerade tjänsten Exponerar en REST-slutpunkt för att ta emot kundinformation i realtid och returnerar motsvarande utgående etiketterna från din tränad modell.

Lätt att använda accepterar modellen slutpunkten data i JSON-format. Indata ska vara en JSON-sträng som representerar en 2-endimensionell matris av data som skickas via ReadJSONLiteral transformeringen och konverteras till en datakälla för DataPrep. Förberedelseverktyg datapaketet skapas under experiment fasen kan sedan köras mot direktuppspelat JSON-data. Den resulterande dataframe kan sedan skickas till den tränade modellen för resultatfunktioner.

## <a name="read-json-literal-transformation"></a>Läsa JSON-Literal omvandling

### <a name="description"></a>Beskrivning

För operationalization är Data Prep innehåller en **ReadJsonLiteral** transformering som ska köra en aktivitet och generera en Pandas eller Spark Dataframe. Den här omvandlingen unikt tar som indata en befintlig data Förbered dig paket och en JSON-datakälla. Den här transformeringen är åtkomlig via DataPrep Python CLI.

### <a name="instructions"></a>Instruktioner

#### <a name="pythoncli"></a>PythonCLI

Azure Machine Learning-arbetsstationen öppna kommandoradsgränssnittet (Arkiv > Öppna Kommandotolken).

I det här exemplet används TrainingPreparationSteps-datapaketet prep förbereda data och lägga till funktionen volymen i varje indata.

```
>>> import azureml.dataprep.package as azdp

>>> azdp.run_on_data.__doc__
"Generate a dataframe based on a selected dataflow in a package using in-memory data sources.
'user_config' is expected as a dictionary that maps the absolute path of a dsource file to an in-memory data source represented as a list of lists."

>>> real_time_customer_data = [[1.0, 1.5, 2.5], [2.5, 1.5, 3]]
>>> azdp.run_on_data({ "C:\\TrainingSampleData.dsource": real_time_customer_data}, "C:\\TrainingPreparationSteps.dprep")
    Height   Width   Depth  Volumne
0   1.0       1.5    2.5    3.75
1   2.5       1.5    3.0    11.25
```

`run_on_data()` har följande valfria parametrar
 - `dataflow_idx`: anger du indexet för den önskade dataflöde i paketet för att köra
 - `secrets`: hemliga store ordlista (nyckel: secretId, värde: lagrade secret)
 - `spark`: Ange en spark-session för spark körning

#### <a name="input"></a>Indata

2-endimensionell matris-indata (”jagged-matris”). I Python, ska indata vara en lista med listor. Eftersom JSON inte har en inbyggd datatyp, kommer Python datetime.datetime objekt att konverteras till att ISO-formaterade datumsträngar. Indata ska vara en konstant JSON-sträng som representerar en 2D-JSON-matris för REST-slutpunkter.

#### <a name="output"></a>Resultat

En Pandas eller Väck DataFrame. Typ av DataFrame bestäms av framework som valts i kör konfigurationen (`.runconfig`). Den resulterande dataframe kan skickas som indata till den tränade modellen för resultatfunktioner.
