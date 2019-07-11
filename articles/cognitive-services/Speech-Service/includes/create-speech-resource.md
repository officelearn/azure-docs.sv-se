---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3c42bf2b2acc2472741bd603ea9d653a314ecc40
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717256"
---
## <a name="create-a-speech-resource"></a>Skapa en tal-resurs

1. Logga in på den [Azure-portalen](https://portal.azure.com)
1. Klicka på [skapa **tal** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) resurs
1. Ange alla nödvändiga inställningar:

    |Inställning|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Subscription|Välj lämplig prenumeration|
    |Location|Välj valfri plats i närheten och tillgänglig|
    |Prisnivå|`F0` -minimal prisnivån|
    |Resursgrupp|Välj en tillgänglig resursgrupp|

1. Klicka på **skapa** och vänta tills resursen som ska skapas. När den har skapats, gå till resurssidan
1. Samla in konfigurerats `endpoint` och en API-nyckel:

    |Resurs-fliken i portalen|Inställning|Värde|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slutpunkten. Den liknar `https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**nycklar**|API-nyckel|Kopiera 1 av två nycklar. Det är en 32 alfanumeriska tecken lång sträng utan mellanslag eller tankstreck, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
