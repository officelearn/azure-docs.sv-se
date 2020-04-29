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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876108"
---
## <a name="create-an-computer-vision-resource"></a>Skapa en Visuellt innehåll resurs

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Klicka på [skapa **visuellt innehåll** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) resurs.
1. Ange alla nödvändiga inställningar:

    |Inställning|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Plats|Välj valfri närliggande och tillgänglig plats|
    |Prisnivå|`F0`– den minimala pris nivån|
    |Resursgrupp|Välj en tillgänglig resurs grupp|

1. Klicka på **skapa** och vänta tills resursen har skapats. När den har skapats går du till resurs sidan.
1. Samla in `{ENDPOINT_URI}` konfigurerade `{API_KEY}`och, se [samla in nödvändiga parametrar](../computer-vision-how-to-install-containers.md#gathering-required-parameters) för mer information.
