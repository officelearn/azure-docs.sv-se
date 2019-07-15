---
title: Skapa en resurs för Cognitive Services Text Analytics
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en resurs för Cognitive Services Text Analytics.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 7e8b4480911f00afa8524ef4b81d697bb8ee5bcc
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877451"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Skapa en resurs för Cognitive Services Text Analytics

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **skapa en resurs**, och gå till **AI + Maskininlärning** > **textanalys**.
   Eller gå till [skapa textanalys](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Ange inställningarna som krävs:

    |Inställning|Värde|
    |--|--|
    |Namn|Ange ett namn (2-64 tecken)|
    |Subscription|Välj lämplig prenumeration|
    |Location|Välj en närliggande plats|
    |Prisnivå| Ange **S**, standardprisnivån|
    |Resource group|Välj en tillgänglig resursgrupp|

1. Välj **skapa** och vänta tills resursen som ska skapas. Din webbläsare omdirigerar automatiskt till sidan nyligen skapade resursen.
1. Samla in den konfigurerade `endpoint` och en API-nyckel:

    |Resurs-fliken i portalen|Inställning|Värde|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slutpunkten. Den ser ut `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**nycklar**|API-nyckel|Kopiera en av två nycklar. Det är en alfanumerisk sträng med 32 tecken utan mellanslag eller tankstreck: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
