---
title: Integrera Azure Stream Analytics med Azure Machine Learning
description: I den här artikeln beskrivs hur du integrerar ett Azure Stream Analytics-jobb med Azure Machine Learning-modeller.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 354b0eae1620c49006b350371aae38f8867c18b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124662"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Integrera Azure Stream Analytics med Azure Machine Learning (förhandsversion)

Du kan implementera maskininlärningsmodeller som en användardefinierad funktion (UDF) i dina Azure Stream Analytics-jobb för att göra bedömning i realtid och förutsägelser på dina strömmande indata. [Med Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) kan du använda alla populära verktyg med öppen källkod, till exempel Tensorflow, scikit-learn eller PyTorch, för att förbereda, träna och distribuera modeller.

> [!NOTE]
> Den här funktionen är i offentlig förhandsversion. Du kan bara komma åt den här funktionen på Azure-portalen med hjälp av [förhandslänken för Stream Analytics-portalen](https://aka.ms/asaportalpreview). Den här funktionen är också tillgänglig i den senaste versionen av [Stream Analytics-verktyg för Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install).

## <a name="prerequisites"></a>Krav

Gör följande innan du lägger till en maskininlärningsmodell som en funktion i ditt Stream Analytics-jobb:

1. Använd Azure Machine Learning för att [distribuera din modell som en webbtjänst](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where).

2. Ditt bedömningsskript bör ha [exempelindata och utdata](../machine-learning/how-to-deploy-and-where.md#example-entry-script) som används av Azure Machine Learning för att generera en schemaspecifikation. Stream Analytics använder schemat för att förstå webbtjänstens funktionssignatur.

3. Kontrollera att webbtjänsten accepterar och returnerar JSON-serialiserade data.

4. Distribuera din modell på [Azure Kubernetes Service](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) för storskaliga produktionsdistributioner. Om webbtjänsten inte kan hantera antalet begäranden som kommer från jobbet försämras resultatet för ditt Stream Analytics-jobb, vilket påverkar svarstiden.

## <a name="add-a-machine-learning-model-to-your-job"></a>Lägga till en maskininlärningsmodell i ditt jobb

Du kan lägga till Azure Machine Learning-funktioner i ditt Stream Analytics-jobb direkt från Azure-portalen.

1. Navigera till ditt Stream Analytics-jobb i Azure-portalen och välj **Funktioner** under **Jobbtopologi**. Välj sedan **Azure ML-tjänst** på menyn **+ Lägg till.**

   ![Lägg till Azure ML UDF](./media/machine-learning-udf/add-azureml-udf.png)

2. Fyll i funktionsformuläret **Azure Machine Learning Service** med följande egenskapsvärden:

   ![Konfigurera Azure ML UDF](./media/machine-learning-udf/configure-azureml-udf.png)

I följande tabell beskrivs varje egenskap för Azure ML-tjänstfunktioner i Stream Analytics.

|Egenskap|Beskrivning|
|--------|-----------|
|Funktionsalias|Ange ett namn som ska anropa funktionen i frågan.|
|Prenumeration|Din Azure-prenumeration..|
|Azure ML-arbetsyta|Arbetsytan Azure Machine Learning som du använde för att distribuera din modell som en webbtjänst.|
|Distributioner|Webbtjänsten som är värd för din modell.|
|Funktionssignatur|Webbtjänstens signatur härleds från API:ets schemaspecifikation. Om signaturen inte läses in kontrollerar du att du har angett exempelindata och utdata i bedömningsskriptet för att automatiskt generera schemat.|
|Antal parallella begäranden per partition|Detta är en avancerad konfiguration för att optimera högskalig dataflöde. Det här numret representerar de samtidiga begäranden som skickas från varje partition i jobbet till webbtjänsten. Jobb med sex strömningsenheter (SU) och lägre har en partition. Jobb med 12 SUs har två partitioner, 18 SUs har tre partitioner och så vidare.<br><br> Om jobbet till exempel har två partitioner och du anger den här parametern till fyra, kommer det att finnas åtta samtidiga begäranden från jobbet till webbtjänsten. Vid denna tid av offentlig förhandsversion, det här värdet standardvärdet till 20 och kan inte uppdateras.|
|Maximalt antal batchar|Detta är en avancerad konfiguration för att optimera högskalig dataflöde. Det här numret representerar det maximala antalet händelser som batchas tillsammans i en enda begäran som skickas till webbtjänsten.|

## <a name="supported-input-parameters"></a>Indataparametrar som stöds

När din Stream Analytics-fråga anropar en Azure Machine Learning UDF skapar jobbet en JSON-serialiserad begäran till webbtjänsten. Begäran baseras på ett modellspecifikt schema. Du måste ange ett exempel inmatning och utdata i ditt bedömningsskript för att [automatiskt generera ett schema](../machine-learning/how-to-deploy-and-where.md). Schemat tillåter Stream Analytics att konstruera JSON serialiserad begäran för någon av de datatyper som stöds, till exempel numpy, pandor och PySpark. Flera indatahändelser kan batchas tillsammans i en enda begäran.

Följande Stream Analytics-fråga är ett exempel på hur du anropar en Azure Machine Learning UDF:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Stream Analytics stöder bara att skicka en parameter för Azure Machine Learning-funktioner. Du kan behöva förbereda dina data innan du skickar dem som en indata till machine learning UDF.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Skicka flera indataparametrar till UDF

De vanligaste exemplen på indata till maskininlärningsmodeller är numpy arrayer och DataFrames. Du kan skapa en matris med hjälp av en JavaScript UDF `WITH` och skapa en JSON-serialiserad DataFrame med satsen.

### <a name="create-an-input-array"></a>Skapa en indatamatris

Du kan skapa en JavaScript UDF som accepterar *N* antal indata och skapar en matris som kan användas som indata till din Azure Machine Learning UDF.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

När du har lagt till JavaScript UDF i jobbet kan du anropa din Azure Machine Learning UDF med följande fråga:

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

Följande JSON är en exempelbegäran:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Skapa en Pandas- eller PySpark-dataram

Du kan `WITH` använda satsen för att skapa en JSON serialiserad DataFrame som kan skickas som indata till din Azure Machine Learning UDF som visas nedan.

Följande fråga skapar en DataFrame genom att välja nödvändiga fält och använder DataFrame som indata till Azure Machine Learning UDF.

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM input
```

Följande JSON är en exempelbegäran från föregående fråga:

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Optimera prestanda för Azure Machine Learning UDFs

När du distribuerar din modell till Azure Kubernetes Service kan du [profilera din modell för att fastställa resursutnyttjande](../machine-learning/how-to-deploy-and-where.md#profilemodel). Du kan också [aktivera App Insights för dina distributioner för](../machine-learning/how-to-enable-app-insights.md) att förstå begärandens frekvens, svarstider och felfrekvens.

Om du har ett scenario med högt händelsedataflöde kan du behöva ändra följande parametrar i Stream Analytics för att uppnå optimal prestanda med svarstider från slutna till slut:

1. Max batchantal.
2. Antal parallella begäranden per partition.

### <a name="determine-the-right-batch-size"></a>Bestäm rätt batchstorlek

När du har distribuerat webbtjänsten skickar du exempelbegäran med varierande batchstorlekar från 50 och ökar den i hundratals. Till exempel 200, 500, 1000, 2000 och så vidare. Du kommer att märka att efter en viss batchstorlek ökar svarstiden för svaret. Den punkt efter vilken svarstiden för svar ökar bör vara max batchantalet för ditt jobb.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Fastställa antalet parallella begäranden per partition

Vid optimal skalning bör ditt Stream Analytics-jobb kunna skicka flera parallella förfrågningar till din webbtjänst och få svar inom några millisekunder. Svarstiden för webbtjänstens svar kan direkt påverka svarstiden och prestanda för ditt Stream Analytics-jobb. Om samtalet från ditt jobb till webbtjänsten tar lång tid, kommer du förmodligen se en ökning av vattenstämpel fördröjning och kan också se en ökning av antalet eftersläpande indatahändelser.

För att förhindra sådan svarstid, se till att ditt AKS-kluster (Azure Kubernetes Service) har etablerats med [rätt antal noder och repliker](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli). Det är viktigt att webbtjänsten är mycket tillgänglig och returnerar lyckade svar. Om ditt jobb får ett svar som inte är tillgänglig för tjänsten (503) från webbtjänsten försöker det kontinuerligt igen med exponentiellt tillbaka. Alla andra svar än framgång (200) och tjänsten är inte tillgänglig (503) gör att jobbet går till ett misslyckat tillstånd.

## <a name="next-steps"></a>Nästa steg

* [Självstudier: Användardefinierade funktioner i Azure Stream Analytics JavaScript](stream-analytics-javascript-user-defined-functions.md)
* [Skala ditt Stream Analytics-jobb med Azure Machine Learning Studio (klassisk) funktion](stream-analytics-scale-with-machine-learning-functions.md)

