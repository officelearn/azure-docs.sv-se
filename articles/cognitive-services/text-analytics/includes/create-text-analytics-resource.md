---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en resurs för cognitive services text analytics.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 022ffcd806d4d4f89f8a8cf256a541518ea12602
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455112"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Skapa en resurs för Cognitive Services Text Analytics

1. Logga in på den [Azure-portalen](https://portal.azure.com)
1. Välj **+ skapa en resurs**, gå till **AI + Maskininlärning > textanalys**, eller klicka på [skapa **textanalys**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)
1. Ange alla nödvändiga inställningar:

    |Inställning|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Location|Välj en närliggande plats|
    |Prisnivå|`S` -prisnivån standard|
    |Resursgrupp|Välj en tillgänglig resursgrupp|

1. Klicka på **skapa** och vänta tills resursen som ska skapas. Din webbläsare som automatiskt omdirigerar till sidan nyligen skapade resursen när den har skapats
1. Samla in konfigurerats `endpoint` och en API-nyckel:

    |Resurs-fliken i portalen|Inställning|Värde|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slutpunkten. Den liknar `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**nycklar**|API-nyckel|Kopiera 1 av två nycklar. Det är en 32 alfanumeriska tecken lång sträng utan mellanslag eller tankstreck, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|