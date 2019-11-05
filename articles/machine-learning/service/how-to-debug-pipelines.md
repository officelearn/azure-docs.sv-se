---
title: Felsöka och felsöka maskin inlärnings pipeliner
titleSuffix: Azure Machine Learning
description: Felsök och Felsök maskin inlärnings pipeliner i Azure Machine Learning SDK för python. Lär dig vanliga fall GRO par för utveckling av pipelines och tips som hjälper dig att felsöka skript före och under fjärrkörning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 10/03/2019
ms.openlocfilehash: fc19e864f00489d3ebc0162705af864785af0811
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497072"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Felsöka och felsöka maskin inlärnings pipeliner
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig att felsöka och felsöka [maskin inlärnings pipeliner](concept-ml-pipelines.md) i [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

I följande avsnitt får du en översikt över vanliga fall GRO par när du skapar pipelines och olika strategier för fel sökning av kod som körs i en pipeline. Använd följande tips när du har problem med att få en pipeline att köras som förväntat. 

## <a name="testing-scripts-locally"></a>Testa skript lokalt

Ett av de vanligaste felen i en pipeline är att ett kopplat skript (data rengörings skript, bedömnings skript osv.) inte körs som avsett eller innehåller körnings fel i fjärrberäknings kontexten som är svåra att felsöka i din arbets yta på Azure-datorn Learning Studio. 

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

## <a name="debugging-scripts-from-remote-context"></a>Felsöka skript från fjärr kontext

Att testa skript lokalt är ett bra sätt att felsöka större kodfragment och komplex logik innan du börjar skapa en pipeline, men ibland behöver du felsöka skript under den faktiska pipelinen, särskilt när du diagnostiserar problem som uppstår under interaktionen mellan pipeline-steg. Vi rekommenderar att du använder `print()`-instruktioner i dina steg skript så att du kan se objekt status och förväntade värden vid fjärrkörning, på samma sätt som du skulle kunna felsöka JavaScript-kod.

Logg filen `70_driver_log.txt` innehåller: 

* Alla uttryckta instruktioner under körningen av skriptet
* Stack spårning för skriptet 

Om du vill hitta den här och andra loggfiler i portalen klickar du först på pipelinen i din arbets yta.

![Sidan pipelines i portalen](./media/how-to-debug-pipelines/pipeline-1.png)

Navigera till den överordnade pipeline-körningen.

![Överordnad pipeline-körning](./media/how-to-debug-pipelines/pipeline-2.png)

Klicka på körnings-ID för det aktuella steget.

![Sidan pipelines i portalen](./media/how-to-debug-pipelines/pipeline-3.png)

Gå till fliken **loggar** . Andra loggar innehåller information om bygg processen för miljö avbildning och steg förberedelse skript.

![Sidan pipelines i portalen](./media/how-to-debug-pipelines/pipeline-4.png)

> [!TIP]
> Körningar för *publicerade pipelines* finns på fliken **pipelines** i din arbets yta i portalen. Det går att hitta körningar för *icke-publicerade pipelines* i **experiment**.

## <a name="troubleshooting-tips"></a>Felsökningstips

Följande tabell innehåller vanliga problem under utveckling av pipeline, med möjliga lösningar.

| Problem | Möjlig lösning |
|--|--|
| Det gick inte att skicka data till `PipelineData` Directory | Se till att du har skapat en katalog i skriptet som motsvarar var din pipeline förväntar dig utdata från steget. I de flesta fall definierar ett indataargument utdata-katalogen och du skapar katalogen explicit. Använd `os.makedirs(args.output_dir, exist_ok=True)` för att skapa utdatakatalogen. Se [självstudien](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) för ett bedömnings skript exempel som visar det här design mönstret. |
| Beroende buggar | Om du har utvecklat och testat skript lokalt men hittar beroende problem när du kör på en fjärrberäkning i pipelinen bör du se till att dina beräknings miljö beroenden och versioner matchar din test miljö. |
| Tvetydiga fel med beräknings mål | Att ta bort och återskapa beräknings mål kan lösa vissa problem med beräknings mål. |
| Pipeline återanvändar inte steg | Steg åter användning är aktiverat som standard, men se till att du inte har inaktiverat det i ett steg i pipeline. Om åter användning är inaktive rad kommer `allow_reuse`-parametern i steget att ställas in på `False`. |
| Pipelinen körs inte nödvändigt vis | För att se till att stegen bara körs igen när deras underliggande data eller skript ändras, kan du koppla ihop dina kataloger för varje steg. Om du använder samma käll katalog för flera steg kan du få onödig omkörning. Använd `source_directory`-parametern i ett pipeline-objekt för att peka på den isolerade katalogen för det steget och se till att du inte använder samma `source_directory` sökväg för flera steg. |

## <a name="next-steps"></a>Nästa steg

* Se SDK-referensen för hjälp med [azureml-pipeline – Core-](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) paketet och [azureml-pipeline-steg-](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) paketet.

* Följ den [avancerade självstudien](tutorial-pipeline-batch-scoring-classification.md) om hur du använder pipelines för batch-poäng.
