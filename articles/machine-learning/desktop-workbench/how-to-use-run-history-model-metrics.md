---
title: Hur du använder kör historik och modellen mått i Azure Machine Learning Workbench | Microsoft Docs
description: Guide för att använda funktionerna för Körningshistorik och modellen mått i Azure Machine Learning Workbench
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 34fe72087a3de133d65ea4a4737ab5dba45242f4
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647279"
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>Använd Körningshistorik och modellen mått i Azure Machine Learning Workbench

Azure Machine Learning Workbench har stöd för data science experimentering via dess **Körningshistorik** och **modellen mått** funktioner.
**Körningshistorik** ger ett sätt att spåra utdata för din machine learning-experiment och aktiverar sedan filtrering och jämförelse av resultaten.
**Modellera mått** kan loggas från valfri tidpunkt skript, spåra vilka värden som är viktigast i dina data science experiment.
Den här artikeln beskriver hur du gör effektiv användning av dessa funktioner för att öka frekvensen och kvaliteten på dina data science experimentering.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här guiden, måste du:
* [Skapa och installera Azure Machine Learning](../service/quickstart-installation.md)
- [Skapa ett projekt](../service/quickstart-installation.md)


## <a name="azure-ml-logging-api-overview"></a>Azure ML översikt över API-loggning
Den [Azure ML loggning API](reference-logging-api.md) är tillgängligt via den **azureml.logging** modul i Python (som har installerats med Azure ML Workbench.) När du importerar den här modulen kan du använda den **get_azureml_logger** metod för att skapa en instans av en **loggaren** objekt.
Du kan sedan använda loggaren **log** metod för att lagra nyckel/värde-par som produceras av Python-skript.
Stöds för närvarande loggning modellen mått för skalära och listtyper som visas.

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)


# log list
logger.log("all values", [5, 6, 7])
```
Det är enkelt att använda loggaren i dina Azure ML Workbench-projekt och den här artikeln visar hur du gör.

## <a name="create-a-project-in-azure-ml-workbench"></a>Skapa ett projekt i Azure ML Workbench
Om du inte redan har ett projekt kan du skapa en från den [skapa och installera snabbstarten](../service/quickstart-installation.md) från den **Projektinstrumentpanel**, kan du öppna den **iris_sklearn.py** skript () som visas.)

![åtkomst till ett skript från fliken filer](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

Du kan använda det här skriptet som en vägledning för förväntade implementering av modellen mått loggning i Azure ML.

## <a name="parameterize-and-log-model-metrics-from-script"></a>Parameterisera och logga modellen mått från skript
I den **iris_sklearn.py** skript och det förväntade mönstret ska importeras och konstruktion loggaren i Python begränsas till följande rader med kod.

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

När du skapat kan du anropa den **log** metoden med alla namn/värde-par.

När utveckling är klar kan vara det bra att Parameterisera skript så att värden kan skickas via kommandoraden.
Exemplet nedan visar hur du godkänner kommandoradsparametrar (i förekommande fall) med hjälp av standard Python-biblioteken.
Det här skriptet tar en parameter för Regulariseringshastigheten (*reg*) används för att anpassa en klassificeringsmodellen i syfte att öka *Precision* utan overfitting.
Dessa variabler loggas sedan som *Regulariseringshastigheten* och *Precision* så att modellen med optimala resultat lätt kan identifieras.

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

Att göra följande i dina skript att de kan göra optimal användning av **Körningshistorik**.

## <a name="launch-runs-from-project-dashboard"></a>Starta körs från instrumentpanelen för projektet
Tillbaka till den **Projektinstrumentpanel**, du kan starta en **spårade kör** genom att välja den **iris_sklearn.py** skript och ange den **regulariseringshastigheten**  parametern i den **argument** redigeringsruta.

![Ange parametrar och starta körningar](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

Eftersom starta spårade körningar inte blockerar Azure ML Workbench, kan flera köras parallellt.
Status för varje spårade körning visas i den **jobb panelen** som visas.

![spårning som körs i panelen jobb](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

På så sätt kan optimalt resursutnyttjande utan att varje jobbet ska köras i serie.

## <a name="view-results-in-run-history"></a>Visa resultat i Körningshistorik
Förlopp och resultat av spårade körningar är tillgängliga för analys i Azure ML Workbench **Körningshistorik**.
**Körningshistorik** tillhandahåller tre olika vyer:
- Instrumentpanel
- Information
- Jämförelse

Den **instrumentpanelen** visas data över alla körningar av ett givet skript, återges i både grafiskt och tabular formulär.
Den **information** vyn visas alla data som genereras från en viss körning av ett givet skript, inklusive loggade mått och utdatafiler (till exempel återges ritar.) Den **jämförelse** vyn kan resultatet av två eller tre körningar vara visade sida-vid-sida, även loggade mått och utgående filer.

I åtta spåras körningar av **iris_sklearn.py**, värden för den **regulariseringshastigheten** parametern och **Precision** resultatet loggats för att illustrera hur du använder du alternativet Kör Historik-vyer.

### <a name="run-history-dashboard"></a>Instrumentpanelen för Körningshistorik
Resultatet av alla åtta körningar syns i den **instrumentpanelen för körningar historik**.
Som **iris_sklearn.py** loggar *Regulariseringshastigheten* och *Precision*, **instrumentpanelen för körningar historik** visar diagram för dessa värden efter som standard.

![instrumentpanelen för körningshistorik](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

Den **instrumentpanelen för körningar historik** kan anpassas så att loggas värden visas också i rutnätet.  Klicka på den **anpassa** ikonen visar den **lista anpassning** dialog som visas.

![Anpassa körningshistoriken instrumentpanelen rutnät](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

Alla värden som loggats under spårade körningar är tillgängliga för visning och välja **Regulariseringshastigheten** och **Precision** lägger till dem i rutnätet.

![loggade värden i anpassade rutnät](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

Det är enkelt att hitta intressant körs genom att hovra över punkter i diagrammen.  I det här fallet gav kör 7 en bra Precision tillsammans med en låg varaktighet.

![hitta en intressant kör](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

Klicka på en plats som är associerade med kör 7 i ett diagram eller länka till kör 7 i rutnätet visar den **kör historikinformation**.

### <a name="run-history-details"></a>Kör historikinformation
Från den här vyn visas alla resultat av den kör 7 tillsammans med alla artefakter som produceras av kör 7.

![Kör historikinformation](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

Den **kör historikinformation** visa ger också möjlighet att **hämta** filer skrivs till den **. / matar ut** mapp (filerna backas upp av Azure ML Workbench molnlagring för Körningshistorik, som omfattas av en annan artikel.)

Slutligen **kör historikinformation** ger dig möjlighet att återställa ditt projekt dess tillstånd vid tidpunkten för den här körningen.
Klicka på den **återställa** knappen visas en bekräftelse-dialogruta som visas.

![Bekräfta återställning som kör](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

Om bekräftad, filer kan skrivas över eller tas bort, så Använd den här funktionen noggrant.

### <a name="run-history-comparison"></a>Kör historik jämförelse
Att markera två eller tre körs i den **instrumentpanelen för körningar historik** och klicka på **jämför** ger du den **kör historik jämförelse** vy.
Att du kan också klicka på **jämför** och välja en körning inom den **kör historikinformation** Visa också ger du den **kör historik jämförelse** vy.
I båda fallen den **kör historik jämförelse** vyn visar ett sätt att se loggade resultat och artefakter i två eller tre körningar sida vid sida.

![Kör historik jämförelse](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

Den här vyn är särskilt användbart för jämförelse av områden, men i allmänhet några egenskaper för körningar kan jämföras här.

### <a name="command-line-interface"></a>Kommandoradsgränssnitt
Azure Machine Learning Workbench ger också åtkomst till Körningshistorik via dess **kommandoradsgränssnitt**.
Åtkomst till den **kommandoradsgränssnitt**, klickar du på den **öppna Kommandotolken** menyn som visas.

![Öppna Kommandotolken](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

Kommandona som är tillgängliga för Körningshistorik kan nås `az ml history`, med direkthjälpen genom att lägga till den `-h` flaggan.
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
De här kommandona ger samma funktioner och returnera samma data som visas i **kör historik vyer**.
Resultat för senaste körning kan till exempel visas som ett JSON-objekt.
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
Dessutom kan listan över alla körningar visas i tabellformat.
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
Den **kommandoradsgränssnitt** är ett alternativt sätt att komma åt kraften i Azure Machine Learning Workbench.

## <a name="next-steps"></a>Nästa steg
Dessa funktioner är tillgängliga för att underlätta processen med att data science experimentering.
Vi hoppas att du hittar dem ska vara användbar och skulle avsevärt uppskattar din feedback.
Detta är bara vår första implementeringen och vi har många förbättringar som planerat.
Vi ser fram emot att kontinuerligt leverera dem till Azure Machine Learning Workbench. 
