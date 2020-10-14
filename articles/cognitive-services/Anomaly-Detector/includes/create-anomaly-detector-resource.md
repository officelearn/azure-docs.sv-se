---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: e896ac5f4625d36060d713d66fa885f8b24756f1
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014755"
---
## <a name="create-an-anomaly-detector-resource"></a>Skapa en resurs för avvikelse detektor

1. Logga in på [Azure Portal](https://portal.azure.com)
1. Klicka på [skapa **avvikelse detektor** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) resurs
1. Ange alla nödvändiga inställningar:

    |Inställningen|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Plats|Välj valfri närliggande och tillgänglig plats|
    |Prisnivå|`F0` – den minimala pris nivån|
    |Resource Group|Välj en tillgänglig resurs grupp|

1. Klicka på **skapa** och vänta tills resursen har skapats. När den har skapats går du till resurs Sidan
1. Samla in konfigurerade `endpoint` och en API-nyckel:

    |Fliken resurs i portalen|Inställningen|Värde|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slut punkten. Det ser ut ungefär så här `https://westus2.api.cognitive.microsoft.com/`|
    |**Nycklar**|API-nyckel|Kopia 1 av de två nycklarna. Det är en sträng med 32 alfanumeriska tecken utan blank steg eller bindestreck `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



