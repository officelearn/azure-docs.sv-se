---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 9d46b304d598b4830cf325909f77eea6b68af757
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89303983"
---
>[!NOTE]
> Slut punkter för resurser som skapats efter den 1 juli 2019 Använd det anpassade under domän formatet som visas nedan. Mer information och en fullständig lista över regionala slut punkter finns i [anpassade under domän namn för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för pennan tecknings tolken med hjälp av [Azure Portal](../../cognitive-services-apis-create-account.md).

När du har skapat en resurs hämtar du slut punkten och nyckeln genom att öppna resursen på [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)och klicka på **snabb start**.

Skapa två [miljövariabler](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` – Prenumerations nyckeln för autentisering av dina begär Anden. 

* `INK_RECOGNITION_ENDPOINT` – Slut punkten för din resurs. Resultatet ser ut så här: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   
