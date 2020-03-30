---
title: Om Azure Machine Learning-miljöer
titleSuffix: Azure Machine Learning
description: I den här artikeln kan du lära dig fördelarna med maskininlärningsmiljöer som möjliggör reproducerbara, granskningsbara och bärbara maskininlärningsberoendedefinitioner över en mängd olika beräkningsmål.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064197"
---
# <a name="what-are-azure-machine-learning-environments"></a>Vad är Azure Machine Learning-miljöer?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning-miljöer anger Python-paket, miljövariabler och programvaruinställningar runt dina tränings- och bedömningsskript. De anger också körtider (Python, Spark eller Docker). Miljöerna hanteras och versionerade entiteter inom arbetsytan Machine Learning som möjliggör reproducerbara, granskningsbara och bärbara maskininlärningsarbetsflöden i en mängd olika beräkningsmål.

Du kan `Environment` använda ett objekt på din lokala beräkning för att:
* Utveckla din utbildning skript.
* Återanvänd samma miljö på Azure Machine Learning Compute för modellutbildning i stor skala.
* Distribuera din modell med samma miljö.

Följande diagram illustrerar hur du kan `Environment` använda ett enda objekt i både din körningskonfiguration, för utbildning och din inferens- och distributionskonfiguration för webbtjänstdistributioner.

![Diagram över en miljö i arbetsflödet för maskininlärning](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Typer av miljöer

Miljöer kan i stort sett delas in i tre kategorier: *kurerade,* *användarhanterade*och *systemhanterade*.

Kurerade miljöer tillhandahålls av Azure Machine Learning och är tillgängliga på din arbetsyta som standard. De innehåller samlingar av Python-paket och inställningar som hjälper dig att komma igång med olika ramverk för maskininlärning. 

I användarhanterade miljöer är du ansvarig för att konfigurera din miljö och installera alla paket som träningsskriptet behöver på beräkningsmålet. Conda kontrollerar inte din miljö eller installerar något åt dig. Om du definierar din egen miljö `azureml-defaults` måste `>= 1.0.45` du lista med version som pipberoende. Det här paketet innehåller de funktioner som behövs för att vara värd för modellen som en webbtjänst.

Du använder systemhanterade miljöer när du vill att [Conda](https://conda.io/docs/) ska hantera Python-miljön och skriptberoendena åt dig. Tjänsten förutsätter den här typen av miljö som standard, på grund av dess användbarhet för fjärrberäkningsmål som inte kan konfigureras manuellt.

## <a name="create-and-manage-environments"></a>Skapa och hantera miljöer

Du kan skapa miljöer genom att:

* Definiera nya `Environment` objekt, antingen med hjälp av en kurerad miljö eller genom att definiera dina egna beroenden.
* Använda `Environment` befintliga objekt från arbetsytan. Den här metoden möjliggör konsekvens och reproducerbarhet med dina beroenden.
* Importera från en befintlig Anaconda-miljödefinition.
* Använda AZURE Machine Learning CLI

Specifika kodexempel finns i avsnittet "Skapa en miljö" [i Återanvändningsmiljöer för utbildning och distribution](how-to-use-environments.md#create-an-environment). Miljöer hanteras också enkelt via arbetsytan. De innehåller följande funktioner:

* Miljöer registreras automatiskt på din arbetsyta när du skickar in ett experiment. De kan också registreras manuellt.
* Du kan hämta miljöer från arbetsytan som du kan använda för utbildning eller distribution eller för att göra ändringar i miljödefinitionen.
* Med versionshantering kan du se ändringar i dina miljöer över tid, vilket säkerställer reproducerbarhet.
* Du kan skapa Docker-avbildningar automatiskt från dina miljöer.

Kodexempel finns i avsnittet "Hantera miljöer" [i Återanvändningsmiljöer för utbildning och distribution](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Miljöbyggnad, cachelagring och återanvändning

Azure Machine Learning-tjänsten bygger miljödefinitioner i Docker-avbildningar och conda-miljöer. Den cachelagrar också miljöerna så att de kan återanvändas i efterföljande utbildningskörningar och tjänstslutpunktsdistributioner.

### <a name="building-environments-as-docker-images"></a>Bygga miljöer som Docker-avbildningar

När du skickar en körning med en miljö anropar azure Machine Learning-tjänsten vanligtvis en [ACR Build-uppgift](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) i Azure Container Registry (ACR) som är associerad med arbetsytan. Den inbyggda Docker-avbildningen cachelagras sedan på ACR på arbetsytan. I början av körningen hämtas avbildningen av beräkningsmålet.

Bildversionen består av två steg:

 1. Hämta en basavbildning och köra alla Docker-steg
 2. Bygga en conda-miljö enligt conda-beroenden som anges i miljödefinitionen.

Det andra steget utelämnas om du anger [användarhanterade beroenden](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py). I det här fallet är du ansvarig för att installera python-paket, genom att inkludera dem i basavbildningen, eller ange anpassade Docker-steg i det första steget. Du är också ansvarig för att ange rätt plats för Python körbar.

### <a name="image-caching-and-reuse"></a>Bildcache och återanvändning

Om du använder samma miljödefinition för en annan körning återanvänder Azure Machine Learning-tjänsten den cachelagrade avbildningen från ACR för arbetsytan. 

Om du vill visa information om en cachelagrad avbildning använder du [Environment.get_image_details-metoden.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-)

För att avgöra om du vill återanvända en cachelagd avbildning eller skapa en ny beräknar tjänsten [ett hash-värde](https://en.wikipedia.org/wiki/Hash_table) från miljödefinitionen och jämför det med hashar för befintliga miljöer. Hash är baserad på:
 
 * Egenskapsvärde för basbild
 * Egenskapsvärde för anpassad dockersteg
 * Lista över Python-paket i Conda-definitionen
 * Lista över paket i Spark-definition 

Hash beror inte på miljönamn eller version. Miljödefinitionsändringar, till exempel lägga till eller ta bort ett Python-paket eller ändra paketversionen, gör att hash-värdet ändras och utlöser en bildreaning. Men om du helt enkelt byter namn på din miljö eller skapar en ny miljö med exakta egenskaper och paket för en befintlig, förblir hash-värdet detsamma och den cachelagrade avbildningen används.

Se följande diagram som visar tre miljödefinitioner. Två av dem har olika namn och version, men identisk basavbildning och Python-paket. De har samma hash och motsvarar därför samma cachelagrade bild. Den tredje miljön har olika Python-paket och versioner och motsvarar därför en annan cachelagrad avbildning.

![Diagram över miljöcachelagring som Docker-bilder](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Om du skapar en miljö med ett oinstallerat paketberoende, till exempel, ```numpy```kommer den miljön att fortsätta att använda paketversionen som installerades när miljön _skapas_. Dessutom kommer alla framtida miljöer med matchande definition att fortsätta använda den gamla versionen. 

Om du vill uppdatera paketet anger du ett ```numpy==1.18.1```versionsnummer för att tvinga fram ombyggnad av avbildningar, till exempel . Observera att nya beroenden, inklusive kapslade, installeras som kan bryta ett tidigare arbetsscenario.

> [!WARNING]
>  Metoden [Environment.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) återskapar den cachelagrade avbildningen, med möjlig bieffekt av uppdatering av oinrättade paket och bryta reproducerbarhet för alla miljödefinitioner som motsvarar den cachelagrade avbildningen.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar och använder miljöer](how-to-use-environments.md) i Azure Machine Learning.
* Se Python SDK-referensdokumentationen för [miljöklassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Se referensdokumentationen för R SDK för [miljöer](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
