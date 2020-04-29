---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "71996853"
---
>[!NOTE]
> Slut punkter för resurser som skapats efter den 1 juli 2019 Använd det anpassade under domän formatet som visas nedan. Mer information och en fullständig lista över regionala slut punkter finns i [anpassade under domän namn för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för pennan tecknings tolken med hjälp av [Azure Portal](../../cognitive-services-apis-create-account.md). 

* Du kan också få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services/#decision) som är giltig i sju dagar utan kostnad. När du har registrerat dig kommer den och en slut punkt vara tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).

När du har skapat en resurs hämtar du slut punkten och nyckeln genom att öppna resursen på [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)och klicka på **snabb start**.

Skapa två [miljövariabler](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY`– Slut punkten för din resurs. Det kommer att se ut så här: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT`– Prenumerations nyckeln för autentisering av dina begär Anden.   
