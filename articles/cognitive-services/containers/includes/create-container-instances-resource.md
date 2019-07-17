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
ms.openlocfilehash: 05284d434e6bd22fd50957f7cc5ec966f88a4fd4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229197"
---
## <a name="create-an-azure-container-instance-resource"></a>Skapa en Behållarinstans i Azure-resurs

1. Gå till den [skapa](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) för Container Instances.

2. På den **grunderna** ange följande information:

    |Inställning|Värde|
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

4. På den **Avancerat** ange de nödvändiga **miljövariabler** behållaren fakturering i inställningarna för ACI-resursen:

    | Nyckel | Värde |
    |--|--|
    |`apikey`|Kopierade från den **nycklar** för resursen. Det är en 32 alfanumeriska tecken lång sträng utan mellanslag eller tankstreck, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Kopierade från den **översikt** för resursen.|
    |`eula`|`accept`|

1. Klicka på **granska och skapa**
1. När valideringen lyckats klickar du på **skapa** att avsluta processen
1. När resursen har distribuerats är det redo