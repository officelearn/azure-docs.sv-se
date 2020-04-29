---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8dbe36693e551bd03022f4a00044b75b912c834a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80875149"
---
## <a name="create-an-anomaly-detector-resource"></a>Skapa en resurs för avvikelse detektor

1. Logga in på [Azure Portal](https://portal.azure.com)
1. Klicka på [skapa **avvikelse detektor** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) resurs
1. Ange alla nödvändiga inställningar:

    |Inställning|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Plats|Välj valfri närliggande och tillgänglig plats|
    |Prisnivå|`F0`– den minimala pris nivån|
    |Resursgrupp|Välj en tillgänglig resurs grupp|
    |Kryss rutan Bekräfta för hands version (krävs)|Om du har läst meddelandet för **förhands granskning**|

1. Klicka på **skapa** och vänta tills resursen har skapats. När den har skapats går du till resurs Sidan
1. Samla in `endpoint` konfigurerade och en API-nyckel:

    |Fliken resurs i portalen|Inställning|Värde|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slut punkten. Det ser ut ungefär så här`https://westus2.api.cognitive.microsoft.com/`|
    |**Nycklar**|API-nyckel|Kopia 1 av de två nycklarna. Det är en sträng med 32 alfanumeriska tecken utan blank steg eller bindestreck `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|



