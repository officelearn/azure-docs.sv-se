---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: ede1fb4bd2a9a6e6536959053e3ca4d8e4a82f87
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327379"
---
>[!NOTE]
> Slut punkter för resurser som skapats efter den 1 juli 2019 Använd det anpassade under domän formatet som visas nedan. Mer information och en fullständig lista över regionala slut punkter finns i [anpassade under domän namn för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för pennan tecknings tolken med hjälp av [Azure Portal](../../cognitive-services-apis-create-account.md).

När du har skapat en resurs hämtar du slut punkten och nyckeln genom att öppna resursen på [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)och klicka på **snabb start**.

Skapa två [miljövariabler](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` – Prenumerations nyckeln för autentisering av dina begär Anden. 

* `INK_RECOGNITION_ENDPOINT` – Slut punkten för din resurs. Resultatet ser ut så här: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   
