---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c68e5b7ab24e2d7e7f30ddc356ae3c4382137507
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369095"
---
>[!NOTE]
> Slut punkter för resurser som skapats efter den 1 juli 2019 Använd det anpassade under domän formatet som visas nedan. Mer information och en fullständig lista över regionala slut punkter finns i [anpassade under domän namn för Cognitive Services](../../cognitive-services-custom-subdomains.md). 

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för pennan tecknings tolken med hjälp av [Azure Portal](../../cognitive-services-apis-create-account.md).

När du har skapat en resurs hämtar du slut punkten och nyckeln genom att öppna resursen på [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)och klicka på **snabb start**.

Skapa två [miljövariabler](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` – Prenumerations nyckeln för autentisering av dina begär Anden. 

* `INK_RECOGNITION_ENDPOINT` – Slut punkten för din resurs. Resultatet ser ut så här: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`