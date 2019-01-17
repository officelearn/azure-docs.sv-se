---
title: Kända problem och felsökning
titleSuffix: Azure Machine Learning service
description: Hämta en lista över kända problem, lösningar, och felsökning för Azure Machine Learning-tjänsten.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: e143c0c8ef09af49aed656d479bcad4dd35e2211
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351806"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Kända problem och felsökning Azure Machine Learning-tjänsten
 
Den här artikeln hjälper dig att hitta och korrigera fel eller fel som uppstod när du använder Azure Machine Learning-tjänsten. 

## <a name="sdk-installation-issues"></a>SDK-installationsproblem

**Felmeddelande: Det går inte att avinstallera 'PyYAML'** 

Azure Machine Learning-SDK för Python: PyYAML är ett distutils installerade projekt. Vi kan inte därför korrekt fastställa vilka filer som hör till den i händelse av en partiell avinstallation. Om du vill fortsätta installerar denna SDK när du ignorera det här felet, använder du:
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problem med att skapa beräkning av Azure Machine Learning
Det finns en ovanligt risk att vissa användare som skapade sin Azure Machine Learning-arbetsyta från Azure-portalen innan GA-versionen kan inte skapa beräkning av Azure Machine Learning på arbetsytan. Du kan generera en supportförfrågan mot tjänsten, eller så kan du skapa en ny arbetsyta via portalen eller SDK, för att avblockera själv omedelbart. 

## <a name="image-building-failure"></a>Bild byggnad fel

Bild för att skapa fel när du distribuerar webbtjänsten. Lösningen är att lägga till ”pynacl == 1.2.1” som ett pip beroende till Conda-fil för konfiguration av avbildningen.  

## <a name="fpgas"></a>FPGA
Du kommer inte att kunna distribuera modeller på FPGA förrän du har begärt och godkänts för FPGA kvot. För att begära åtkomst, fyller du i formuläret för begäran av kvot: https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Databricks och Azure Machine Learning-problem.
 
1. Misslyckad installation på Databricks av AML SDK när flera paket installeras.

   Vissa paket, till exempel `psutil`, kan orsaka konflikter. Installera paket genom att du låser lib-version för att undvika installationsfel. Det här problemet är relaterat till Databricks och inte rör Azure ML SDK – du kan stöta på det med andra libs för. Exempel:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
   ```
   Du kan också använda init skript om du hålla får installera problem med med Python-bibliotek. Den här metoden är inte en metod som stöds. Du kan referera till [det här dokumentet](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

2. När du använder automatisk Machine Learning på Databricks, om du vill avbryta en körning och starta ett nytt experiment, köra, starta om ditt Azure Databricks-kluster.

3. I inställningarna för automatisk ml, när du har > 10 iterationer Ställ in show_output som False när du skickar in din körning.


## <a name="azure-portal"></a>Azure Portal
Om du går direkt för att visa din arbetsyta från en delningslänk från SDK: N eller portalen kan du inte visa normala översikt översiktssidan med prenumerationsinformation i tillägget. Du kommer inte heller att kunna växla till en annan arbetsyta. Om du vill visa en annan arbetsyta lösningen är att gå direkt till den [Azure-portalen](https://portal.azure.com) och Sök efter namnet på arbetsytan.

## <a name="diagnostic-logs"></a>Diagnostikloggar
Ibland kan det vara bra om du kan ange diagnostisk information när du frågar om du behöver hjälp. Här är där loggfilerna live:

## <a name="resource-quotas"></a>Resurskvoter

Lär dig mer om den [resurskvoter](how-to-manage-quotas.md) som kan uppstå när du arbetar med Azure Machine Learning.

## <a name="get-more-support"></a>Få mer support

Du kan skicka begäran om support och få hjälp från teknisk support och forum. [Lära sig mer...](support-for-aml-services.md)
