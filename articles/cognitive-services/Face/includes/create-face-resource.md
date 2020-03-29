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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73523986"
---
## <a name="create-an-face-resource"></a>Skapa en ansiktsresurs

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Klicka på [Skapa **ansiktsresurs** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
1. Ange alla nödvändiga inställningar:

    |Inställning|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Location|Välj en plats i närheten och tillgänglig|
    |Prisnivå|`F0`- den minimala prisnivån|
    |Resursgrupp|Välj en tillgänglig resursgrupp|

1. Klicka på **Skapa** och vänta tills resursen har skapats. När den har skapats navigerar du till resurssidan
1. Samla in `endpoint` konfigurerad och en API-nyckel:

    |Fliken Resurs i portalen|Inställning|Värde|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slutpunkten. Det liknar`https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Nycklar**|API-nyckel|Kopiera 1 av de två nycklarna. Det är en 32 alfanumerisk teckensträng utan `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`blanksteg eller streck.|
