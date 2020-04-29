---
title: Skapa en Cognitive Services Textanalys resurs
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Cognitive Services Textanalys-resurs.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6cd653909e26dc5e0484ca289a1d2ab47e20457f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876470"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Skapa en Cognitive Services Textanalys resurs

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **skapa en resurs**och gå sedan till **AI + Machine Learning** > **textanalys**.
   Eller gå till [skapa textanalys](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Ange alla nödvändiga inställningar:

    |Inställning|Värde|
    |--|--|
    |Namn|Ange ett namn (2-64 tecken).|
    |Prenumeration|Välj lämplig prenumeration.|
    |Plats|Välj en närliggande plats.|
    |Prisnivå| Ange **S**, standard pris nivån.|
    |Resursgrupp|Välj en tillgänglig resurs grupp.|

1. Välj **skapa**och vänta tills resursen har skapats. Webbläsaren omdirigeras automatiskt till den nyligen skapade resurs sidan.
1. Samla in den `endpoint` konfigurerade och en API-nyckel:

    |Fliken resurs i portalen|Inställning|Värde|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slut punkten. Det ser ut ungefär `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`som.|
    |**Nycklar**|API-nyckel|Kopiera en av de två nycklarna. Det är en alfanumerisk sträng på 32 tecken utan blank steg eller bindestreck: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
