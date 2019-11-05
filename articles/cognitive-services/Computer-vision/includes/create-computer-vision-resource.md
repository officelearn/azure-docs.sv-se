---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2019
ms.author: dapine
ms.openlocfilehash: 2cb2cfbdfbac5d496f109d85977b41a050766ab0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499110"
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
    |Prisnivå|`F0` – den minimala pris nivån|
    |Resursgrupp|Välj en tillgänglig resurs grupp|

1. Klicka på **skapa** och vänta tills resursen har skapats. När den har skapats går du till resurs sidan.
1. Samla in konfigurerade `{ENDPOINT_URI}` och `{API_KEY}`, se [samla in nödvändiga parametrar](../computer-vision-how-to-install-containers.md#gathering-required-parameters) för mer information.
