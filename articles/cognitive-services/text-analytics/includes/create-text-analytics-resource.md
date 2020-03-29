---
title: Skapa en Textanalysresurs för Cognitive Services
titleSuffix: Azure Cognitive Services
description: Läs om hur du skapar en Cognitive Services Text Analytics-resurs.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383462"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Skapa en Textanalysresurs för Cognitive Services

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Skapa en resurs**och gå sedan till AI + Machine **Learning** > Text**Analytics**.
   Eller gå till [Skapa textanalys](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Ange alla nödvändiga inställningar:

    |Inställning|Värde|
    |--|--|
    |Namn|Ange ett namn (2-64 tecken).|
    |Prenumeration|Välj lämplig prenumeration.|
    |Location|Välj en plats i närheten.|
    |Prisnivå| Ange **S**, standardprisnivån.|
    |Resursgrupp|Välj en tillgänglig resursgrupp.|

1. Välj **Skapa**och vänta på att resursen har skapats. Webbläsaren omdirigeras automatiskt till den nyskapade resurssidan.
1. Samla in `endpoint` den konfigurerade och en API-nyckel:

    |Fliken Resurs i portalen|Inställning|Värde|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slutpunkten. Det verkar `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`liknar .|
    |**Nycklar**|API-nyckel|Kopiera en av de två nycklarna. Det är en alfanumerisk sträng med 32 tecken utan blanksteg eller streck: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
