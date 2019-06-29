---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en resurs för azure container-instans (ACI).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: b24a78873c3220b969fc548d2553e465e80773cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455060"
---
## <a name="create-an-azure-container-instance-aci-resource"></a>Skapa en resurs i Azure Container Instance (ACI)

1. Gå till den [skapa](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) för Container Instances.

2. På den **grunderna** ange följande information:

    |Inställning|Värde|
    |--|--|
    |Prenumeration|Välj din prenumeration.|
    |Resursgrupp|Välj den tillgängliga resursgruppen eller skapa en ny som `cognitive-services`.|
    |Containerns namn|Ange ett namn som `cognitive-container-instance`. Det här namnet måste vara i lägre versaler.|
    |Location|Välj en region för distribution.|
    |Avbildningstyp|`Public`|
    |Avbildningens namn|Ange platsen för Cognitive Services-behållare. Detta kan vara på samma plats som du använde i den `docker pull` kommandot _till exempel_: <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |OS-typ|`Linux`|
    |Size|Ändra storlek till de föreslagna rekommendationerna för din specifika Cognitive Service-behållare.:<br>2 kärnor<br>4 GB

3. På den **nätverk** ange följande information:

    |Inställning|Värde|
    |--|--|
    |Portar|Ange TCP-porten som `5000`. Visar behållaren på port 5000.|

4. På den **Avancerat** ange följande information för att passera behållaren [nödvändiga fakturering inställningar](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments) till ACI-resursen:

    |Sidan Avancerat nyckel|Sidan Avancerat värde|
    |--|--|
    |`apikey`|Kopierade från den **nycklar** sidan av Text Analytics-resurs. Det är en 32 alfanumeriska tecken lång sträng utan mellanslag eller tankstreck, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Kopierade från den **översikt** sidan av Text Analytics-resurs. Exempel: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. Klicka på **granska och skapa**
1. När valideringen lyckats klickar du på **skapa** att avsluta processen
1. När resursen har distribuerats är det redo för användning