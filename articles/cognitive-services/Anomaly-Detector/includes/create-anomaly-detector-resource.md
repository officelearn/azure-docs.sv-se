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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875149"
---
## <a name="create-an-anomaly-detector-resource"></a>Skapa en avvikelsedetektorresurs

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Klicka på [Skapa **avvikelsedetektorresurs** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)
1. Ange alla nödvändiga inställningar:

    |Inställning|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Location|Välj en plats i närheten och tillgänglig|
    |Prisnivå|`F0`- den minimala prisnivån|
    |Resursgrupp|Välj en tillgänglig resursgrupp|
    |Kryssrutan Förhandsgranska bekräftelse (obligatoriskt)|Om du har läst meddelandet **om förhandsgranskning** eller inte|

1. Klicka på **Skapa** och vänta tills resursen har skapats. När den har skapats navigerar du till resurssidan
1. Samla in `endpoint` konfigurerad och en API-nyckel:

    |Fliken Resurs i portalen|Inställning|Värde|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slutpunkten. Det liknar`https://westus2.api.cognitive.microsoft.com/`|
    |**Nycklar**|API-nyckel|Kopiera 1 av de två nycklarna. Det är en 32 alfanumerisk teckensträng utan `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`blanksteg eller streck.|



