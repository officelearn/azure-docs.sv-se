---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en resurs för Azure container-instans.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 38addf4651373ba0f4df411325218a255c835508
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717186"
---
## <a name="create-an-azure-container-instance-resource"></a>Skapa en Behållarinstans i Azure-resurs

1. Gå till den [skapa](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) för Container Instances.

2. På den **grunderna** ange följande information:

    |Inställning|Value|
    |--|--|
    |Subscription|Välj din prenumeration.|
    |Resource group|Välj den tillgängliga resursgruppen eller skapa en ny som `cognitive-services`.|
    |Containerns namn|Ange ett namn som `cognitive-container-instance`. Namnet måste vara i lägre versaler.|
    |Location|Välj en region för distribution.|
    |Avbildningstyp|`Public`|
    |Avbildningens namn|Ange platsen för Cognitive Services-behållare. Platsen kan vara samma som används i den `docker pull` kommandot, referera till den [behållare databaser och bilder](../../cognitive-services-container-support.md#container-repositories-and-images) för tillgängliga namnen och deras motsvarande lagringsplats.|
    |OS-typ|`Linux`|
    |Size|Ändra storlek till de föreslagna rekommendationerna för din specifika Cognitive Service-behållare:<br>2 CPU-kärnor<br>4 GB

3. På den **nätverk** ange följande information:

    |Inställning|Value|
    |--|--|
    |Portar|Ange TCP-porten som `5000`. Visar behållaren på port 5000.|

4. På den **Avancerat** ange de nödvändiga **miljövariabler** för behållaren [fakturering inställningar](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments) på ACI-resursen:

    | Nyckel | Värde |
    |--|--|
    |`apikey`|Kopierade från den **nycklar** sidan av Text Analytics-resurs. Det är en 32 alfanumeriska tecken lång sträng utan mellanslag eller tankstreck, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Kopierade från den **översikt** sidan av Text Analytics-resurs. Exempel: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. Klicka på **granska och skapa**
1. När valideringen lyckats klickar du på **skapa** att avsluta processen
1. När resursen har distribuerats är det redo