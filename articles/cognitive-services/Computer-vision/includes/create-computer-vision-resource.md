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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876108"
---
## <a name="create-an-computer-vision-resource"></a>Skapa en datorseenderesurs

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på [Skapa **datorseende** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) resurs.
1. Ange alla nödvändiga inställningar:

    |Inställning|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Location|Välj en plats i närheten och tillgänglig|
    |Prisnivå|`F0`- den minimala prisnivån|
    |Resursgrupp|Välj en tillgänglig resursgrupp|

1. Klicka på **Skapa** och vänta tills resursen har skapats. När den har skapats navigerar du till resurssidan.
1. Samla in `{ENDPOINT_URI}` `{API_KEY}`konfigurerade och , se [samla in nödvändiga parametrar](../computer-vision-how-to-install-containers.md#gathering-required-parameters) för detaljer.
