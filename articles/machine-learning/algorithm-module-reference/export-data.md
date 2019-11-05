---
title: 'Exportera data: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen exportera data i Azure Machine Learning för att spara resultat, mellanliggande data och arbeta data från dina pipelines till moln lagrings mål utanför Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 5badcbe2a76c41bdc1431abae617e35b7555eb88
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497894"
---
# <a name="export-data-module"></a>Exportera datamodul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd den här modulen för att spara resultat, mellanliggande data och arbeta data från dina pipelines till moln lagrings mål utanför Azure Machine Learning. 

Den här modulen stöder export av data till följande moln data tjänster:

- Azure Blob-behållare
- Azure-filresurs
- Azure Data Lake
- Azure Data Lake Gen2

Innan du exporterar dina data måste du först registrera ett data lager i din Azure Machine Learning-arbetsyta. Mer information finns i [så här kommer du åt data](../service/how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Så här konfigurerar du export data

1. Lägg till modulen **Exportera data** till din pipeline i designern. Du hittar den här modulen i kategorin för **indata och utdata** .

1. Anslut **Exportera data** till modulen som innehåller de data som du vill exportera.

1. Välj **Exportera data** för att öppna fönstret **Egenskaper** .

1. För **data lager**väljer du ett befintligt data lager i list rutan. Du kan också skapa ett nytt data lager. Se hur [du kan gå till instruktioner för att komma åt data](../service/how-to-access-data.md)

1. Definiera sökvägen i data lagret som data ska skrivas till. 


1. I **fil format**väljer du det format som data ska lagras i.
 
1. Köra en pipeline.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 