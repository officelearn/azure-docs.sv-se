---
title: Kända problem och felsökning för Azure Machine Learning-tjänsten
description: Hämta en lista över kända problem, lösningar, och felsökning
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: d4910eb3dfacb46efe3f85aea3a441bdaaeb1392
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48236420"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Kända problem och felsökning Azure Machine Learning-tjänsten
 
Den här artikeln hjälper dig att hitta och korrigera fel eller fel som uppstod när du använder Azure Machine Learning-tjänsten. 

## <a name="image-building-failure"></a>Bild byggnad fel

Bild för att skapa fel när du distribuerar webbtjänsten. Lösningen är att lägga till ”pynacl == 1.2.1” som ett pip beroende till Conda-fil för konfiguration av avbildningen.  

## <a name="pipelines"></a>Pipelines
Ett fel uppstår när du anropar PythonScriptStep flera gånger i rad utan att ändra skriptet eller parametrar. Lösningen är att återskapa PipelineData-objektet.

## <a name="fpgas"></a>FPGA
Du kommer inte att kunna distribuera modeller på FPGA förrän du har begärt och godkänts för FPGA kvot. För att begära åtkomst, fyller du i formuläret för begäran av kvot: https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Databricks och Azure Machine Learning-problem.

1. Rekommendation för Databricks-klustret:
   
   Skapa ditt Azure Databricks-kluster som v4.x med Python 3. Vi rekommenderar ett kluster med hög samtidighet.
 
1. Misslyckad installation på Databricks av AML SDK när flera paket installeras.

   Vissa paket, till exempel `psutil upgrade libs`, kan orsaka konflikter. Installera paket genom att du låser lib-version för att undvika installationsfel. Det här problemet är relaterat till Databricks och inte relaterade till AML-SDK. Exempel:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="gather-diagnostics-information"></a>Samla in diagnostikinformation
Ibland kan det vara bra om du kan ange diagnostisk information när du frågar om du behöver hjälp. Här är där loggfilerna live:

## <a name="resource-quotas"></a>Resurskvoter

Lär dig mer om den [resurskvoter](how-to-manage-quotas.md) som kan uppstå när du arbetar med Azure Machine Learning.

## <a name="get-more-support"></a>Få mer support

Du kan skicka begäran om support och få hjälp från teknisk support och forum. [Lära sig mer...](support-for-aml-services.md)
