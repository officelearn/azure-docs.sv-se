---
title: Skapa en Cognitive Services Textanalys resurs
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Cognitive Services Textanalys-resurs.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383462"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Skapa en Cognitive Services Textanalys resurs

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **skapa en resurs**och gå sedan till **AI + Machine Learning** > **textanalys**.
   Eller gå till [skapa textanalys](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Ange alla nödvändiga inställningar:

    |Inställning|Value|
    |--|--|
    |Name|Ange ett namn (2-64 tecken).|
    |Prenumeration|Välj lämplig prenumeration.|
    |Plats|Välj en närliggande plats.|
    |Prisnivå| Ange **S**, standard pris nivån.|
    |Resursgrupp|Välj en tillgänglig resurs grupp.|

1. Välj **skapa**och vänta tills resursen har skapats. Webbläsaren omdirigeras automatiskt till den nyligen skapade resurs sidan.
1. Samla in den konfigurerade `endpoint` och en API-nyckel:

    |Fliken resurs i portalen|Inställning|Value|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slut punkten. Det ser ut ungefär som `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`.|
    |**Nyckel**|API-nyckel|Kopiera en av de två nycklarna. Det är en alfanumerisk sträng på 32 tecken utan blank steg eller bindestreck: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
