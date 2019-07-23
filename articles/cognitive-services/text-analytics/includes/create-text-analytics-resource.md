---
title: Skapa en Cognitive Services Textanalys resurs
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Cognitive Services Textanalys-resurs.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 6a9363472e1d5770232ca9a0b151fb309d9c4c75
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377413"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Skapa en Cognitive Services Textanalys resurs

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **skapa en resurs**och gå sedan till **AI + Machine Learning** > **textanalys**.
   Eller gå till [skapa textanalys](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Ange alla nödvändiga inställningar:

    |Inställning|Värde|
    |--|--|
    |Namn|Ange ett namn (2-64 tecken).|
    |Subscription|Välj lämplig prenumeration.|
    |Location|Välj en närliggande plats.|
    |Prisnivå| Ange **S**, standard pris nivån.|
    |Resource group|Välj en tillgänglig resurs grupp.|

1. Välj **skapa**och vänta tills resursen har skapats. Webbläsaren omdirigeras automatiskt till den nyligen skapade resurs sidan.
1. Samla in den `endpoint` konfigurerade och en API-nyckel:

    |Fliken resurs i portalen|Inställning|Värde|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slut punkten. Det ser ut ungefär `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`som.|
    |**Nyckel**|API-nyckel|Kopiera en av de två nycklarna. Det är en alfanumerisk sträng på 32 tecken utan blank steg eller bindestreck: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
