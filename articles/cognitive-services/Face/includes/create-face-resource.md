---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: aahi
ms.openlocfilehash: 0a330c2401412e1e3d5e2e49bf8121a681342808
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "80878381"
---
## <a name="create-an-face-resource"></a>Skapa en ansikts resurs

1. Logga in på [Azure Portal](https://portal.azure.com)
1. Klicka på [skapa **ansikts** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace) resurs
1. Ange alla nödvändiga inställningar:

    |Inställningen|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Location|Välj valfri närliggande och tillgänglig plats|
    |Prisnivå|`F0` – den minimala pris nivån|
    |Resursgrupp|Välj en tillgänglig resurs grupp|

1. Klicka på **skapa** och vänta tills resursen har skapats. När den har skapats går du till resurs Sidan
1. Samla in konfigurerade `endpoint` och en API-nyckel:

    |Fliken resurs i portalen|Inställningen|Värde|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slut punkten. Det ser ut ungefär så här `https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Nycklar**|API-nyckel|Kopia 1 av de två nycklarna. Det är en sträng med 32 alfanumeriska tecken utan blank steg eller bindestreck `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|
