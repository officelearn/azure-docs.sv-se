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
ms.openlocfilehash: 2647c604bebc2885f5c8516eb997792b65751e88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "80876108"
---
## <a name="create-an-computer-vision-resource"></a>Skapa en Visuellt innehåll resurs

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Klicka på [skapa **visuellt innehåll** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) resurs.
1. Ange alla nödvändiga inställningar:

    |Inställningen|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Location|Välj valfri närliggande och tillgänglig plats|
    |Prisnivå|`F0` – den minimala pris nivån|
    |Resursgrupp|Välj en tillgänglig resurs grupp|

1. Klicka på **skapa** och vänta tills resursen har skapats. När den har skapats går du till resurs sidan.
1. Samla in konfigurerade `{ENDPOINT_URI}` och `{API_KEY}` , se [samla in nödvändiga parametrar](../computer-vision-how-to-install-containers.md#gathering-required-parameters) för mer information.
