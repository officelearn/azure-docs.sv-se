---
title: Hur använder Kör som historik och modellen mått i Azure Maskininlärning arbetsstationen | Microsoft Docs
description: Guide för att använda funktioner i Körningshistorik och modellen mått Azure Machine Learning-arbetsstationen
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: df29117235e890a9b20619744df6320f298a73b2
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831884"
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>Använd Körningshistorik och modellen mätvärden i Azure Machine Learning-arbetsstationen

Azure Machine Learning arbetsstationen stöder datavetenskap experiment via dess **Körningshistorik** och **modellen mått** funktioner.
**Kör historik** ett sätt att spåra utdata för din maskininlärning experiment och möjliggör filtrering och jämförelse av resultaten.
**Modellen mått** kan loggas från helst skripten, spåra värdena som är viktigast i dina datavetenskap experiment.
Den här artikeln beskriver hur du gör effektiv användning av dessa funktioner för att öka hastigheten och kvaliteten på dina datavetenskap experiment.

## <a name="prerequisites"></a>Förutsättningar
Om du vill gå igenom den här instruktioner, måste du:
* [Skapa och installera Azure Machine Learning](../service/quickstart-installation.md)
- [Skapa ett projekt](../service/quickstart-installation.md)


## <a name="azure-ml-logging-api-overview"></a>Översikt över Azure ML loggning API
Den [Azure ML loggning API](reference-logging-api.md) är tillgänglig via den **azureml.logging** modul i Python (som har installerats med Azure ML-arbetsstationen.) När du importerar den här modulen kan du använda den **get_azureml_logger** metod för att skapa en instans av en **loggaren** objekt.
Du kan sedan använda loggaren **loggen** metod för att lagra nyckel/värde-par som produceras av Python-skript.
För närvarande stöds loggning modellen mätvärden för skalära och listtyper som visas.

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)


# log list
logger.log("all values", [5, 6, 7])
```
Det är enkelt att använda loggning i din Azure ML-arbetsstationen projekt och den här artikeln visar hur du gör.

## <a name="create-a-project-in-azure-ml-workbench"></a>Skapa ett projekt i Azure ML-arbetsstationen
Om du inte redan har ett projekt, kan du skapa en från den [skapar och installerar Quickstart](../service/quickstart-installation.md) från den **instrumentpanelen för projektet**, kan du öppna den **iris_sklearn.py** skript () som visas.)

![åtkomst till ett skript från fliken filer](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

Du kan använda det här skriptet som en vägledning för förväntade implementering av modellen mått i Azure ML-loggning.

## <a name="parameterize-and-log-model-metrics-from-script"></a>Parameterstyra och logga modellen mått från skript
I den **iris_sklearn.py** skript förbrukas ska importeras och konstruktion loggaren i Python kan minskas till följande rader med kod.

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

När du skapat kan du anropa den **loggen** metod med något namn/värde-par.

När utveckling är klar, är det ofta användbart att parameterstyra skript så att värden kan överföras via kommandoraden.
Exemplet nedan visar hur du godkänner kommandoradsparametrar (i förekommande fall) med hjälp av Python-standardbibliotek.
Det här skriptet tar en parameter för Regularization hastighet (*reg*) används för att anpassa en modell för klassificering för att öka *noggrannhet* utan overfitting.
Dessa variabler loggas sedan som *Regularization hastighet* och *noggrannhet* så att modellen med optimala resultat lätt kan identifieras.

```Python
# change regularization rate and you will likely get a different accuracy.
reg = 0.01
# load regularization rate from argument if present
if len(sys.argv) > 1:
    reg = float(sys.argv[1])

print("Regularization rate is {}".format(reg))

# log the regularization rate
run_logger.log("Regularization Rate", reg)

# train a logistic regression model on the training set
clf1 = LogisticRegression(C=1/reg).fit(X_train, Y_train)
print (clf1)

# evaluate the test set
accuracy = clf1.score(X_test, Y_test)
print ("Accuracy is {}".format(accuracy))

# log accuracy
run_logger.log("Accuracy", accuracy)
```

Vidtar dessa åtgärder i skript ska kunna se optimal användning av **Körningshistorik**.

## <a name="launch-runs-from-project-dashboard"></a>Starta körs från instrumentpanelen för projektet
Gå tillbaka till den **instrumentpanelen för projektet**, kan du starta en **spårade kör** genom att välja den **iris_sklearn.py** skript och ange den **regularization hastighet**  parametern i den **argument** redigeringsrutan.

![Ange parametrar och starta körs](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

Sedan startas spårade körs inte blockerar Azure ML-arbetsstationen, startas flera parallellt.
Status för varje spårade kör visas i den **jobb panelen** som visas.

![spårning körs i panelen jobb](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

Detta gör optimal Resursanvändning utan varje jobb ska köras i serie.

## <a name="view-results-in-run-history"></a>Visa resultat i Körningshistorik
Förlopp och resultat för spårade körs är tillgängliga för analys i Azure ML arbetsstationen **Körningshistorik**.
**Kör historik** innehåller tre olika vyer:
- Instrumentpanel
- Information
- Jämförelse

Den **instrumentpanelen** visas data över alla körningar av ett givet skript återges i både grafiskt och tabular formulär.
Den **information** vyn visas alla data som genereras från en specifik körning av ett givet skript, inklusive loggade mått och utgående filer (som renderas ritas.) Den **jämförelse** view gör det möjligt för resultatet av två eller tre körs ska visas sida vid sida, inklusive också loggas mått och utgående filer.

Över åtta spåras körningar av **iris_sklearn.py**, värden för den **regularization hastighet** parameter och **noggrannhet** resultatet har loggats som illustrerar hur du använder kör Historik vyer.

### <a name="run-history-dashboard"></a>Kör historik instrumentpanelen
Resultatet av alla åtta körs visas i den **kör historik instrumentpanelen**.
Som **iris_sklearn.py** loggar *Regularization hastighet* och *noggrannhet*, **kör historik instrumentpanelen** visar diagram för dessa värden av som standard.

![Kör historik instrumentpanelen](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

Den **kör historik instrumentpanelen** kan anpassas så att loggade värden visas också i rutnätet.  Klicka på den **anpassa** ikon visas den **lista anpassning** dialog som visas.

![Anpassa körningshistorik instrumentpanelen rutnätet](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

Alla värden som loggats under spårade körs är tillgängliga för visning och markera **Regularization hastighet** och **noggrannhet** lägger till dem i rutnätet.

![loggade värden i anpassade rutnätet](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

Det är enkelt att hitta intressanta körs av användaren håller muspekaren över diagrammen tidpunkter.  I det här fallet gav kör 7 en god Precision tillsammans med en låg varaktighet.

![söka efter en intressanta kör](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

Klicka på en plats som är associerad med kör 7 i diagram eller länka till kör 7 i rutnätet visar den **kör historikinformation**.

### <a name="run-history-details"></a>Kör historikinformation
Från den här vyn visas alla resultat kör-7 tillsammans med eventuella artefakter som produceras av köras 7.

![Kör historikinformation](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

Den **kör historikinformation** visa ger också möjlighet att **hämta** filer som skrivs till den **. / matar ut** mapp (filerna backas upp av Azure ML arbetsstationen lagringsutrymmet i molnet för Körningshistorik, som omfattas av en annan artikel.)

Slutligen **kör historikinformation** ger dig möjlighet att återställa ditt projekt dess tillstånd vid tidpunkten för det här körs.
Klicka på den **återställa** knappen visas en bekräftelse dialog som visas.

![Bekräfta återställning körs](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

Om bekräftat, kan skrivas över filer eller tas bort, så Använd denna funktion för noggrant.

### <a name="run-history-comparison"></a>Kör historik jämförelse
Markera två eller tre körs i den **kör historik instrumentpanelen** och klicka på **jämför** ger dig den **kör historik jämförelse** vyn.
Du kan också klicka på **jämför** och välja en körning inom den **kör historikinformation** Visa också ger dig den **kör historik jämförelse** vyn.
I båda fallen den **kör historik jämförelse** Visa innebär att se loggade resultat och artefakter av två eller tre körs sida vid sida.

![Kör historik jämförelse](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

Den här vyn är särskilt användbar för jämförelse av områden, men i allmänhet några egenskaper för körs kan jämföras här.

### <a name="command-line-interface"></a>Kommandoradsgränssnitt
Azure Machine Learning arbetsstationen ger också tillgång till Körningshistorik via dess **kommandot rad gränssnittet**.
Att få åtkomst till den **kommandot Line Interface**, klickar du på den **öppnar du kommandotolken** menyn som visas.

![Öppna Kommandotolken](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

Kommandon som är tillgängliga för Körningshistorik kan nås `az ml history`, med direkthjälpen genom att lägga till den `-h` flaggan.
```
$ az ml history -h

Group
    az ml history: View run history of machine learning experiments.

Commands:
    compare : Compare two runs.
    download: Download all the artifacts from a run into the destination path.
    info    : Details about one run.
    last    : Get detail about most recent run.
    list    : List runs.
    promote : Promote Artifacts.
```
Dessa kommandon har samma funktioner och returnera samma data som visas i **kör historik vyer**.
Resultatet av den senaste körningen kan till exempel visas som ett JSON-objekt.
```
$ az ml history last
{
  "Accuracy": 0.6792452830188679,
  "Regularization Rate": 0.078125,
  "attachments": "control_log, control_log.txt, driver_log, driver_log.txt, pid.txt, dataprep/backgroundProcess.log, dataprep/backgroundProcess_Engine.log, dataprep/backgroundProcess_EngineHost.log, dataprep/backgroundProcess_ProjectProvider.log, dataprep/backgroundProcess_Sampling.log, dataprep/backgroundProcess_Telemetry.log, outputs/cm.png, outputs/model.pkl, outputs/sdk_debug.txt, outputs/roc.png",
  "created_utc": "2017-09-08T00:58:01.611105+00:00",
  "description": null,
  "duration": "0:00:04.892389",
  "end_time_utc": "2017-09-08T00:58:07.951403+00:00",
  "experiment_id": "ce92d0a9-3e2c-4d51-85de-93ef22249ce1",
  "heartbeat_enabled": true,
  "hidden": false,
  "name": null,
  "parent_run_id": null,
  "properties": {
    "CommitId": "e77a5f0df2af1a482bbe39b70bfbb16b62228cb3"
  },
  "run_id": "IrisDemo_1504832281116",
  "run_number": 8,
  "script_name": "iris_sklearn.py",
  "start_time_utc": "2017-09-08T00:58:03.059014+00:00",
  "status": "Completed",
  "target": "local",
  "user_id": "e9fafe06-b0e4-4154-8374-aae34f9977b2"
}
```
Listan över alla körningar kan även visas i tabellformat.
```
$ az ml history list -o table
  Accuracy    Regularization Rate  Duration        Run_id                  Script_name      Start_time_utc                    Status
----------  ---------------------  --------------  ----------------------  ---------------  --------------------------------  ---------
  0.679245               0.078125  0:00:04.892389  IrisDemo_1504832281116  iris_sklearn.py  2017-09-08T00:58:03.059014+00:00  Completed
  0.679245               0.15625   0:00:04.734956  IrisDemo_1504832255198  iris_sklearn.py  2017-09-08T00:57:38.507196+00:00  Completed
  0.660377               0.3125    0:00:04.913762  IrisDemo_1504832234904  iris_sklearn.py  2017-09-08T00:57:16.849881+00:00  Completed
  0.660377               0.625     0:00:06.107764  IrisDemo_1504832210767  iris_sklearn.py  2017-09-08T00:56:54.602813+00:00  Completed
  0.641509               1.25      0:00:04.742727  IrisDemo_1504832171373  iris_sklearn.py  2017-09-08T00:56:13.879280+00:00  Completed
  0.660377               2.5       0:00:04.915401  IrisDemo_1504832148526  iris_sklearn.py  2017-09-08T00:55:52.937083+00:00  Completed
  0.641509               5         0:00:04.730627  IrisDemo_1504832127172  iris_sklearn.py  2017-09-08T00:55:29.612382+00:00  Completed
  0.641509              10         0:00:06.059082  IrisDemo_1504832109906  iris_sklearn.py  2017-09-08T00:55:14.739806+00:00  Completed

```
Den **kommandot Line Interface** är en alternativa sätt att komma åt kraften i Azure Machine Learning-arbetsstationen.

## <a name="next-steps"></a>Nästa steg
Dessa funktioner är tillgängliga för att underlätta processen med att datavetenskap experiment.
Vi hoppas att du hittar dem ska vara användbar och avsevärt tacksamt emot din feedback.
Detta är bara vårt första implementeringen och vi har en massa förbättringar som planerat.
Vi hoppas att kontinuerligt levereras till Azure Machine Learning-arbetsstationen. 
