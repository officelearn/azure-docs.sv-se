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
ms.date: 01/06/2020
ms.openlocfilehash: 8906299cc9e2c000dab2ac9d2a345d9aaf238260
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045856"
---
# <a name="what-are-azure-machine-learning-environments"></a>Vad är Azure Machine Learning miljöer?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning miljöer anger python-paket, miljövariabler och program varu inställningar kring dina utbildnings-och bedömnings skript. De anger också kör tider (python, Spark eller Docker). De är hanterade och versioner av entiteter inom din Machine Learning arbets yta som möjliggör återskapande, gransknings bara och bärbara Machine Learning-arbetsflöden över flera olika beräknings mål.

Du kan använda ett `Environment`-objekt i din lokala beräkning för att:
* Utveckla ditt utbildnings skript.
* Återanvänd samma miljö på Azure Machine Learning beräkning för modell träning i stor skala.
* Distribuera din modell med samma miljö.

Följande diagram illustrerar hur du kan använda ett enda `Environment`-objekt i både din körnings konfiguration, för utbildning och din konfiguration för distribution av webb tjänster.

![Diagram över en miljö i Machine Learning-arbetsflöde](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Typer av miljöer

Miljöer kan i stort sett delas in i tre kategorier: *granskade*, *hanterade*och *systemhanterade*.

Granskade miljöer tillhandahålls av Azure Machine Learning och är tillgängliga i arbets ytan som standard. De innehåller samlingar med python-paket och inställningar som hjälper dig att komma igång med olika ramverk för maskin inlärning. 

I användar hanterade miljöer ansvarar du för att konfigurera din miljö och installera varje paket som utbildnings skriptet behöver på beräknings målet. Conda kontrollerar inte din miljö eller installerar något åt dig. Om du definierar en egen miljö måste du ange `azureml-defaults` med version `>= 1.0.45` som ett pip-beroende. Det här paketet innehåller de funktioner som krävs för att vara värd för modellen som en webb tjänst.

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

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar och använder miljöer](how-to-use-environments.md) i Azure Machine Learning.
* Se referens dokumentation för python SDK för [miljö klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Se referens dokumentationen för R SDK för [miljöer](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
