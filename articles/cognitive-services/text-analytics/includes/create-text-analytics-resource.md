---
title: Skapa en Cognitive Services Textanalys-resurs
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Cognitive Services Textanalys-resurs.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: daafea59b96cc8da6b78f0733c9f54e0e4d3a8c2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009942"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Skapa en Cognitive Services Textanalys-resurs

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Skapa en resurs** och gå sedan till **AI + maskininlärning** > **Textanalys**.
   Eller gå till [skapa textanalys](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Ange alla nödvändiga inställningar:

    |Inställning|Värde|
    |--|--|
    |Namn|Ange ett namn (2–64 tecken).|
    |Prenumeration|Välj lämplig prenumeration.|
    |Plats|Välj en närliggande plats.|
    |Prisnivå| Ange **S**, standardprisnivån.|
    |Resursgrupp|Välj en tillgänglig resursgrupp.|

1. Klicka på **Skapa** och vänta tills resursen har skapats. Webbläsaren omdirigeras automatiskt till den nyligen skapade resurssidan.
1. Samla in den konfigurerade `endpoint` och en API-nyckel:

    |Fliken resurs i portalen|Inställning|Värde|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slut punkten. Det ser ut ungefär som `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0` .|
    |**Nycklar**|API-nyckel|Kopiera en av de två nycklarna. Det är en alfanumerisk sträng på 32 tecken utan blank steg eller bindestreck: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
