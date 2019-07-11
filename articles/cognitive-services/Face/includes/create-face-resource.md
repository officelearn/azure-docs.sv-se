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
ms.openlocfilehash: 3886777b283af35e84683480a59097584b537fea
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717146"
---
## <a name="create-an-face-resource"></a>Skapa en Ansikts-resurs

1. Logga in på den [Azure-portalen](https://portal.azure.com)
1. Klicka på [skapa **ansikte** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace) resurs
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

    |Resurs-fliken i portalen|Inställning|Value|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slutpunkten. Den liknar `https://face.cognitiveservices.azure.com/face/v1.0`|
    |**nycklar**|API-nyckel|Kopiera 1 av två nycklar. Det är en 32 alfanumeriska tecken lång sträng utan mellanslag eller tankstreck, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
