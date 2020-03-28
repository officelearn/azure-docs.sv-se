---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71996853"
---
>[!NOTE]
> Slutpunkter för resurser som skapats efter den 1 juli 2019 använder det anpassade underdomänformatet som visas nedan. Mer information och en fullständig lista över regionala slutpunkter finns i [Anpassade underdomännamn för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Bläckkon recognizer med [Azure-portalen](../../cognitive-services-apis-create-account.md). 

* Du kan också få en [testnyckel](https://azure.microsoft.com/try/cognitive-services/#decision) giltig i sju dagar gratis. När du har registrerat dig kommer den och en slutpunkt att vara tillgängliga på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).

När du har skapat en resurs hämtar du slutpunkten och nyckeln genom att öppna resursen på [Azure-portalen](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)och klicka på **Snabbstart**.

Skapa två [miljövariabler:](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)

* `INK_RECOGNITION_SUBSCRIPTION_KEY`- Slutpunkten för din resurs. Det kommer att se ut så här: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT`- Prenumerationsnyckeln för att autentisera dina förfrågningar.   
