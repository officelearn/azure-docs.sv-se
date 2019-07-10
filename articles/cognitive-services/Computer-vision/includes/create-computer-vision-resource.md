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
ms.openlocfilehash: cbf11c13bfb5c90739ea67fab92df08796a88e50
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711608"
---
## <a name="create-an-computer-vision-resource"></a>Skapa en resurs för visuellt innehåll

1. Logga in på den [Azure-portalen](https://portal.azure.com)
1. Klicka på [skapa **visuellt** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) resurs
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
    |**Översikt**|Slutpunkt|Kopiera slutpunkten. Den liknar `https://computer-vision.cognitiveservices.azure.com/`|
    |**nycklar**|API-nyckel|Kopiera 1 av två nycklar. Det är en 32 alfanumeriska tecken lång sträng utan mellanslag eller tankstreck, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
