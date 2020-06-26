---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c202ba1d7363af9791daa801f0c447c49a80859b
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378603"
---
>[!NOTE]
> Slut punkter för resurser som skapats efter den 1 juli 2019 Använd det anpassade under domän formatet som visas nedan. Mer information och en fullständig lista över regionala slut punkter finns i [anpassade under domän namn för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för pennan tecknings tolken med hjälp av [Azure Portal](../../cognitive-services-apis-create-account.md).

När du har skapat en resurs hämtar du slut punkten och nyckeln genom att öppna resursen på [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)och klicka på **snabb start**.

Skapa två [miljövariabler](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY`– Slut punkten för din resurs. Det kommer att se ut så här: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT`– Prenumerations nyckeln för autentisering av dina begär Anden.   
