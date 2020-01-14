---
title: Vad är ML-miljöer?
titleSuffix: Azure Machine Learning
description: I den här artikeln får du lära dig fördelarna med Machine Learning-miljöer, som möjliggör återare, gransknings bara och bärbara dator inlärnings beroende definitioner över olika beräknings mål.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: 3216248943ccc0dba788816cdba38732f9e43e14
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930753"
---
# <a name="what-are-azure-machine-learning-environments"></a>Vad är Azure Machine Learning miljöer?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Miljöer anger python-paket, miljövariabler och program varu inställningar kring dina utbildnings-och Poäng skript och kör tider (python, Spark eller Docker). De är hanterade och versions bara entiteter inom din Azure Machine Learning arbets yta som möjliggör återskapande, gransknings bara och bärbara Machine Learning-arbetsflöden över olika beräknings mål.

Du kan använda ett miljö objekt i din lokala beräkning för att utveckla ditt utbildnings skript, återanvända samma miljö på Azure Machine Learning beräkning för modell utbildning i stor skala och till och med distribuera din modell med samma miljö.

I följande avsnitt visas hur du kan använda samma miljö objekt i både din körnings konfiguration för utbildning och i konfigurationen för din konfiguration för distribution av webb tjänster.

![Diagram över miljö i Machine Learning-arbetsflöde](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Typer av miljöer

Miljöer kan i stort sett delas in i tre kategorier: **granskade**, **användar hanterade** och **systemhanterade**.

Granskade miljöer tillhandahålls av Azure Machine Learning och är tillgängliga i arbets ytan som standard. De innehåller samlingar med python-paket och inställningar som hjälper dig att komma igång med olika ramverk för maskin inlärning. 

För en användar hanterad miljö ansvarar du för att konfigurera din miljö och installera varje paket som utbildnings skriptet behöver på beräknings målet. Conda kommer inte att kontrol lera din miljö eller installera något åt dig. Observera att om du definierar en egen miljö måste du ange `azureml-defaults` med version `>= 1.0.45` som ett pip-beroende. Det här paketet innehåller de funktioner som krävs för att vara värd för modellen som en webb tjänst.

System-hanterade miljöer används när du vill att [Conda](https://conda.io/docs/) ska hantera python-miljön och skript beroenden åt dig. Tjänsten antar den här typen av miljö som standard, på grund av dess användbarhet på fjärranslutna beräknings mål som inte kan konfigureras manuellt.

## <a name="creating-and-managing-environments"></a>Skapa och hantera miljöer

Miljöer kan skapas av:

* Definiera nya `Environment` objekt, antingen med hjälp av en granskad miljö eller genom att definiera egna beroenden
* Använda befintliga `Environment` objekt från din arbets yta. Detta ger konsekvens och reproducerbarhet med dina beroenden
* Importera från en befintlig Anaconda-miljö definition.
* Använda Azure Machine Learning CLI

Se [anvisningar](how-to-use-environments.md#create-an-environment) för vissa kod exempel. Miljöer hanteras också enkelt genom din arbets yta och innehåller följande funktioner:

* Miljöer registreras automatiskt på arbets ytan när du skickar ett experiment. De kan också registreras manuellt
* Hämta miljöer från din arbets yta och Använd dem för utbildning, distribution eller gör ändringar i miljö definitionen
* Med versions hantering kan du se ändringar i dina miljöer över tid och säkerställa reproducerbarhet
* Bygg Docker-avbildningar automatiskt från dina miljöer

Se avsnittet [hantera miljöer](how-to-use-environments.md#manage-environments) i instruktionen How-to för kod exempel.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar och använder miljöer](how-to-use-environments.md) i Azure Machine Learning
* Se referens dokument för python SDK för [miljö klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Se referens dokument för R SDK för [miljöer](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).