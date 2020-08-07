---
title: Felsöka & Felsök ML-pipelines
titleSuffix: Azure Machine Learning
description: Felsöka Azure Machine Learning pipelines i python. Lär dig vanliga fall GRO par för utveckling av pipelines och tips som hjälper dig att felsöka skript före och under fjärrkörning. Lär dig hur du använder Visual Studio Code för att interaktivt felsöka dina pipelines i Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.topic: conceptual
ms.custom: troubleshooting, devx-track-python
ms.openlocfilehash: ac8896bae4b3bf36ee6e943581bbf6791401c821
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904657"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Felsöka pipelines för maskininlärning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig att felsöka och felsöka [maskin inlärnings pipeliner](concept-ml-pipelines.md) i [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) och [Azure Machine Learning designer (för hands version)](https://docs.microsoft.com/azure/machine-learning/concept-designer). Information finns i How to:

* Felsöka med hjälp av Azure Machine Learning SDK
* Felsöka med hjälp av Azure Machine Learning designer
* Felsöka med hjälp av Application Insights
* Felsöka interaktivt med Visual Studio Code (VS Code) och Python Tools for Visual Studio (PTVSD)

## <a name="azure-machine-learning-sdk"></a>Azure Machine Learning SDK
I följande avsnitt får du en översikt över vanliga fall GRO par när du skapar pipelines och olika strategier för fel sökning av kod som körs i en pipeline. Använd följande tips när du har problem med att få en pipeline att köras som förväntat.

### <a name="testing-scripts-locally"></a>Testa skript lokalt

Ett av de vanligaste felen i en pipeline är att ett kopplat skript (data rengörings skript, bedömnings skript osv.) inte körs som avsett eller innehåller körnings fel i fjärrberäknings kontexten som är svåra att felsöka i arbets ytan i Azure Machine Learning Studio. 

Det går inte att köra pipeliner lokalt, men om du kör skripten i isolering på din lokala dator kan du felsöka snabbare eftersom du inte behöver vänta på bearbetningen av beräknings-och miljö versionen. Det krävs en del utvecklings arbete för att göra detta:

* Om dina data finns i ett moln data lager måste du hämta data och göra dem tillgängliga för ditt skript. Att använda ett litet exempel på dina data är ett bra sätt att skära ned i körningen och snabbt få feedback om skript beteende
* Om du försöker simulera ett mellanliggande pipeline-steg kan du behöva bygga de objekt typer som det aktuella skriptet förväntar sig i föregående steg
* Du måste också definiera en egen miljö och replikera de beroenden som definierats i fjärrberäknings miljön

När du har en skript konfiguration som ska köras i din lokala miljö är det mycket enklare att utföra fel sökning av uppgifter, t. ex.:

* Bifoga en anpassad fel söknings konfiguration
* Pausa körning och inspektera objekt tillstånd
* Fångst typ eller logiska fel som inte ska exponeras förrän runtime

> [!TIP] 
> När du kan kontrol lera att skriptet körs som förväntat kör du ett lämpligt nästa steg i en pipeline för ett enda steg innan du försöker köra det i en pipeline med flera steg.

### <a name="debugging-scripts-from-remote-context"></a>Felsöka skript från fjärr kontext

Att testa skript lokalt är ett bra sätt att felsöka större kodfragment och komplex logik innan du börjar skapa en pipeline, men ibland behöver du felsöka skript under själva pipeline-körningen, särskilt när du diagnostiserar beteende som inträffar under interaktionen mellan pipeline-steg. Vi rekommenderar att du använder `print()` instruktioner i dina steg skript så att du kan se objekt status och förväntade värden under fjärrkörning, på samma sätt som du skulle felsöka JavaScript-kod.

Logg filen `70_driver_log.txt` innehåller: 

* Alla uttryckta instruktioner under körningen av skriptet
* Stack spårning för skriptet 

Om du vill hitta den här och andra loggfiler i portalen börjar du med att klicka på pipeline-körningen i din arbets yta.

![Sidan körnings lista för pipeline](./media/how-to-debug-pipelines/pipelinerun-01.png)

Gå till sidan körnings information för pipeline.

![Sidan körnings information för pipeline](./media/how-to-debug-pipelines/pipelinerun-02.png)

Klicka på modulen för det aktuella steget. Gå till fliken **loggar** . Andra loggar innehåller information om bygg processen för miljö avbildning och steg förberedelse skript.

![Fliken logg för körning av informations sida](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Körningar för *publicerade pipelines* finns på fliken **slut punkter** i din arbets yta. Körningar för *icke-publicerade pipelines* kan hittas i **experiment** eller **pipeliner**.

### <a name="troubleshooting-tips"></a>Felsökningstips

Följande tabell innehåller vanliga problem under utveckling av pipeline, med möjliga lösningar.

| Problem | Möjlig lösning |
|--|--|
| Det gick inte att skicka data till `PipelineData` katalogen | Se till att du har skapat en katalog i skriptet som motsvarar var din pipeline förväntar dig utdata från steget. I de flesta fall definierar ett indataargument utdata-katalogen och du skapar katalogen explicit. Använd `os.makedirs(args.output_dir, exist_ok=True)` för att skapa utdatakatalogen. Se [självstudien](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) för ett bedömnings skript exempel som visar det här design mönstret. |
| Beroende buggar | Om du har utvecklat och testat skript lokalt men hittar beroende problem när du kör på en fjärrberäkning i pipelinen bör du se till att dina beräknings miljö beroenden och versioner matchar din test miljö. (Se [miljö utveckling, cachelagring och åter användning](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Tvetydiga fel med beräknings mål | Att ta bort och återskapa beräknings mål kan lösa vissa problem med beräknings mål. |
| Pipeline återanvändar inte steg | Steg åter användning är aktiverat som standard, men se till att du inte har inaktiverat det i ett steg i pipeline. Om åter användning är inaktive rad `allow_reuse` kommer parametern i steget att ställas in på `False` . |
| Pipelinen körs inte nödvändigt vis | För att se till att stegen bara körs igen när deras underliggande data eller skript ändras, kan du koppla ihop dina kataloger för varje steg. Om du använder samma käll katalog för flera steg kan du få onödig omkörning. Använd `source_directory` parametern i ett pipeline-steg-objekt för att peka på den isolerade katalogen för det steget och se till att du inte använder samma `source_directory` sökväg för flera steg. |

### <a name="logging-options-and-behavior"></a>Loggnings alternativ och beteende

Tabellen nedan innehåller information om olika fel söknings alternativ för pipelines. Det är inte en fullständig lista, som andra alternativ finns förutom bara de Azure Machine Learning-, python-och Open-räkningar som visas här.

| Bibliotek                    | Typ   | Exempel                                                          | Mål                                  | Resurser                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Mått | `run.log(name, val)`                                             | Azure Machine Learning portalens användar gränssnitt             | [Spåra experiment](how-to-track-experiments.md)<br>[azureml. Core. Run-klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python-utskrift/-loggning    | Loggas    | `print(val)`<br>`logging.info(message)`                          | Driv rutins loggar, Azure Machine Learning designer | [Spåra experiment](how-to-track-experiments.md)<br><br>[Python-loggning](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| Python-räkningar          | Loggas    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights-spår                | [Felsöka pipelines i Application Insights.](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor-exportörer](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Cookbook för python-loggning](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Exempel på loggnings alternativ

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="azure-machine-learning-designer-preview"></a>Azure Machine Learning designer (för hands version)

Det här avsnittet innehåller en översikt över hur du felsöker pipelines i designern. För pipeliner som skapats i designern kan du hitta **70_driver_log** -filen på antingen sidan redigering eller på sidan körnings information för pipelinen.

### <a name="enable-logging-for-real-time-endpoints"></a>Aktivera loggning för slut punkter i real tid

För att felsöka och felsöka real tids slut punkter i designern måste du aktivera program insikts loggning med hjälp av SDK. Med loggning kan du felsöka och felsöka problem med distribution och användning av modeller. Mer information finns i [Logga in för distribuerade modeller](how-to-enable-logging.md#logging-for-deployed-models). 

### <a name="get-logs-from-the-authoring-page"></a>Hämta loggar från sidan redigering

När du skickar en pipeline-körning och stannar på sidan redigering kan du hitta de loggfiler som genereras för varje modul när varje modul har slutförts.

1. Välj en modul som har körts klart på redigerings arbets ytan.
1. I den högra rutan i modulen går du till fliken **utdata + loggar** .
1. Expandera den högra rutan och välj **70_driver_log.txt** för att visa filen i webbläsaren. Du kan också hämta loggar lokalt.

    ![Fönster för utökad utdata i designern](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Hämta loggar från pipeline-körningar

Du kan också hitta loggfilerna för vissa körningar på sidan körnings information för pipeline, som du hittar i avsnittet **pipelines** eller **experiment** i Studio.

1. Välj en pipeline-körning som skapats i designern.

    ![Sidan pipeline-körning](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Välj en modul i förhands gransknings fönstret.
1. I den högra rutan i modulen går du till fliken **utdata + loggar** .
1. Expandera den högra rutan om du vill visa **70_driver_log.txt** filen i webbläsaren eller välj filen för att ladda ned loggarna lokalt.

> [!IMPORTANT]
> Om du vill uppdatera en pipeline från sidan körnings information för pipelinen måste du **klona** pipeline-körningen till ett nytt pipeline-utkast. En pipeline-körning är en ögonblicks bild av pipelinen. Det liknar en loggfil och kan inte ändras. 

## <a name="application-insights"></a>Application Insights
Mer information om hur du använder python-biblioteket för openräkning på det här sättet finns i den här guiden: [Felsöka och Felsök maskin inlärnings pipeliner i Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="visual-studio-code"></a>Visual Studio-koden

I vissa fall kan du behöva interaktivt felsöka python-koden som används i ML-pipeline. Genom att använda Visual Studio Code (VS Code) och debugpy kan du ansluta till koden när den körs i tränings miljön. Mer information finns [i interaktiv fel sökning i vs Code guide](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="next-steps"></a>Nästa steg

* Se SDK-referensen för hjälp med [azureml-pipeline – Core-](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) paketet och [azureml-pipeline-steg-](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) paketet.

* Se listan över [designers undantag och felkoder](algorithm-module-reference/designer-error-codes.md).
