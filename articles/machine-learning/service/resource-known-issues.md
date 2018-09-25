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
ms.date: 09/24/2018
ms.openlocfilehash: 27e73bc75c5f04190bad3dab49c1d46782982a18
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034152"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Kända problem och felsökning Azure Machine Learning-tjänsten
 
Den här artikeln hjälper dig att hitta och korrigera fel eller fel som uppstod när du använder Azure Machine Learning-tjänsten. 


## <a name="databricks-and-azure-machine-learning"></a>Databricks och Azure Machine Learning

**Rekommendation för Databricks-klustret:** 

Skapa ditt Azure Databricks-kluster som v4.x med Python 3. Vi rekommenderar ett kluster med hög samtidighet.
 
**Misslyckad installation AML SDK på Databricks när flera paket installeras** vissa paket, till exempel `psutil upgrade libs`, kan orsaka konflikter. Installera paket genom att du låser lib-version för att undvika installationsfel. Det här problemet är relaterat till Databricks och inte relaterade till AML-SDK. Exempel:
```
pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
```


## <a name="gather-diagnostics-information"></a>Samla in diagnostikinformation
Ibland kan det vara bra om du kan ange diagnostisk information när du frågar om du behöver hjälp. Här är där loggfilerna live:

## <a name="resource-quotas"></a>Resurskvoter

Lär dig mer om den [resurskvoter](how-to-manage-quotas.md) som kan uppstå när du arbetar med Azure Machine Learning.

## <a name="get-more-support"></a>Få mer support

Du kan skicka begäran om support och få hjälp från teknisk support och forum. [Lära sig mer...](support-for-aml-services.md)
