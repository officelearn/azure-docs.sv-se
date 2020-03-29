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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499110"
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
