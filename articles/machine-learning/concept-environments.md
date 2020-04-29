---
title: Om Azure Machine Learning miljöer
titleSuffix: Azure Machine Learning
description: I den här artikeln får du lära dig fördelarna med Machine Learning-miljöer, som möjliggör återare, gransknings bara och bärbara dator inlärnings beroende definitioner över flera olika beräknings mål.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064197"
---
# <a name="what-are-azure-machine-learning-environments"></a>Vad är Azure Machine Learning miljöer?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning miljöer anger python-paket, miljövariabler och program varu inställningar kring dina utbildnings-och bedömnings skript. De anger också kör tider (python, Spark eller Docker). Miljöerna är hanterade och versioner av entiteter inom din Machine Learning arbets yta som möjliggör återskapande, gransknings bara och bärbara Machine Learning-arbetsflöden över flera olika beräknings mål.

Du kan använda ett `Environment` objekt i din lokala beräkning för att:
* Utveckla ditt utbildnings skript.
* Återanvänd samma miljö på Azure Machine Learning beräkning för modell träning i stor skala.
* Distribuera din modell med samma miljö.

Följande diagram illustrerar hur du kan använda ett enda `Environment` objekt i både din körnings konfiguration, för utbildning och din konfiguration för distribution av webb tjänster.

![Diagram över en miljö i Machine Learning-arbetsflöde](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Typer av miljöer

Miljöer kan i stort sett delas in i tre kategorier: *granskade*, *hanterade*och *systemhanterade*.

Granskade miljöer tillhandahålls av Azure Machine Learning och är tillgängliga i arbets ytan som standard. De innehåller samlingar med python-paket och inställningar som hjälper dig att komma igång med olika ramverk för maskin inlärning. 

I användar hanterade miljöer ansvarar du för att konfigurera din miljö och installera varje paket som utbildnings skriptet behöver på beräknings målet. Conda kontrollerar inte din miljö eller installerar något åt dig. Om du definierar en egen miljö måste du lista `azureml-defaults` med version `>= 1.0.45` som ett pip-beroende. Det här paketet innehåller de funktioner som krävs för att vara värd för modellen som en webb tjänst.

Du använder systemhanterade miljöer när du vill att [Conda](https://conda.io/docs/) ska hantera python-miljön och skript beroenden åt dig. Tjänsten förutsätter den här typen av miljö som standard, på grund av dess användbarhet för fjärranslutna beräknings mål som inte kan konfigureras manuellt.

## <a name="create-and-manage-environments"></a>Skapa och hantera miljöer

Du kan skapa miljöer genom att:

* Definiera nya `Environment` objekt, antingen genom att använda en granskad miljö eller genom att definiera egna beroenden.
* Använda befintliga `Environment` objekt från din arbets yta. Den här metoden möjliggör konsekvens och reproducerbarhet med dina beroenden.
* Importera från en befintlig Anaconda-miljö definition.
* Använda Azure Machine Learning CLI

Vissa kod exempel finns i avsnittet "skapa en miljö" i [åter användnings miljöer för utbildning och distribution](how-to-use-environments.md#create-an-environment). Miljöer hanteras också enkelt genom din arbets yta. De innehåller följande funktioner:

* Miljöer registreras automatiskt på arbets ytan när du skickar ett experiment. De kan också registreras manuellt.
* Du kan hämta miljöer från din arbets yta för att använda utbildning eller distribution, eller för att göra ändringar i miljö definitionen.
* Med versions hantering kan du se ändringar i dina miljöer över tid, vilket säkerställer reproducerbarhet.
* Du kan bygga Docker-avbildningar automatiskt från dina miljöer.

Kod exempel finns i avsnittet "hantera miljöer" i [Återanvänd miljöer för utbildning och distribution](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Miljö uppbyggnad, cachelagring och åter användning

Azure Machine Learnings tjänsten bygger miljö definitioner i Docker-avbildningar och Conda-miljöer. Den cachelagrar också miljöerna så att de kan återanvändas i efterföljande utbildningar och distributioner av tjänst slut punkter.

### <a name="building-environments-as-docker-images"></a>Skapa miljöer som Docker-avbildningar

När du först skickar en körning med en miljö anropar Azure Machine Learnings tjänsten en [ACR-build-uppgift](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) på den Azure Container Registry (ACR) som är kopplad till arbets ytan. Den inbyggda Docker-avbildningen cachelagras sedan på arbets ytans ACR. I början av körnings körningen hämtas avbildningen av beräknings målet.

Avbildnings versionen består av två steg:

 1. Hämta en bas avbildning och köra eventuella Docker-steg
 2. Skapa en Conda-miljö enligt Conda-beroenden som anges i miljö definitionen.

Det andra steget utelämnas om du anger [användar hanterade beroenden](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py). I det här fallet är du ansvarig för att installera python-paket, genom att inkludera dem i bas avbildningen eller ange anpassade Docker-steg i det första steget. Du är också ansvarig för att ange rätt plats för den körbara python-filen.

### <a name="image-caching-and-reuse"></a>Cachelagring av bilder och åter användning

Om du använder samma miljö definition för en annan körning återanvänder Azure Machine Learnings tjänsten den cachelagrade avbildningen från arbets ytans ACR. 

Om du vill visa information om en cachelagrad avbildning använder du [Environment. get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-) -metoden.

För att avgöra om du ska återanvända en cachelagrad avbildning eller skapa en ny, beräknar tjänsten [ett hash-värde](https://en.wikipedia.org/wiki/Hash_table) från miljö definitionen och jämför den med hasharna i befintliga miljöer. Hashen baseras på:
 
 * Egenskaps värde för bas avbildning
 * Egenskaps värde för anpassade Docker-steg
 * Lista över python-paket i Conda-definitionen
 * Lista över paket i Spark-definition 

Hashen är inte beroende av miljö namnet eller versionen. Ändringar i miljö definitionen, till exempel att lägga till eller ta bort ett python-paket eller ändra paket versionen, gör att hash-värdet ändras och utlöser en avbildnings återskapning. Men om du helt enkelt byter namn på din miljö eller skapar en ny miljö med de exakta egenskaperna och paketen för en befintlig, kommer hashvärdet att vara detsamma och den cachelagrade avbildningen används.

Se följande diagram som visar tre miljö definitioner. Två av dem har olika namn och version, men samma bas avbildning och python-paket. De har samma hash-värde och motsvarar därför samma cachelagrade avbildning. Den tredje miljön har olika python-paket och-versioner och motsvarar därför en annan cachelagrad avbildning.

![Diagram över cachelagring av miljöer som Docker-avbildningar](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Om du skapar en miljö med ett ej fixerat paket beroende kan du till ```numpy```exempel använda paket versionen som är installerad _när miljön skapades_. Dessutom fortsätter all framtida miljö med matchnings definition att använda den gamla versionen. 

Om du vill uppdatera paketet anger du ett versions nummer som tvingar avbildnings återskapning, ```numpy==1.18.1```till exempel. Observera att nya beroenden, inklusive kapslade, kommer att installeras som kan bryta ett tidigare arbets scenario.

> [!WARNING]
>  [Miljön. Build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) -metoden kommer att återskapa den cachelagrade avbildningen med möjlig sido effekt på att uppdatera icke-fästa paket och bryta reproducerbarhet för alla miljö definitioner som motsvarar den cachelagrade avbildningen.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar och använder miljöer](how-to-use-environments.md) i Azure Machine Learning.
* Se referens dokumentation för python SDK för [miljö klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Se referens dokumentationen för R SDK för [miljöer](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
