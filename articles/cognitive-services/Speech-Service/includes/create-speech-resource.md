---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: trbye
ms.openlocfilehash: 8abd520a7bb80f1f9a2d8ebcbe46b90fe59a96ec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421793"
---
## <a name="create-a-speech-resource"></a>Skapa en talresurs

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Klicka på [Skapa **talresurs** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)
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
    |**Översikt**|Slutpunkt|Kopiera slutpunkten. Det liknar`https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Nycklar**|API-nyckel|Kopiera 1 av de två nycklarna. Det är en 32 alfanumerisk teckensträng utan `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`blanksteg eller streck.|
