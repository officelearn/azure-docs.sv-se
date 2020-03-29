---
title: Anpassade underdomäner
titleSuffix: Azure Cognitive Services
description: Anpassade underdomännamn för varje Cognitive Service-resurs skapas via Azure-portalen, Azure Cloud Shell eller Azure CLI.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647681"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Anpassade underdomännamn för Cognitive Services

Azure Cognitive Services använder anpassade underdomännamn för varje resurs som skapas via [Azure-portalen,](https://portal.azure.com) [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)eller Azure [CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Till skillnad från regionala slutpunkter, som var vanliga för alla kunder i en viss Azure-region, är anpassade underdomännamn unika för resursen. Anpassade underdomännamn krävs för att aktivera funktioner som Azure Active Directory (Azure AD) för autentisering.

## <a name="how-does-this-impact-existing-resources"></a>Hur påverkar detta befintliga resurser?

Cognitive Services-resurser som skapats före den 1 juli 2019 använder de regionala slutpunkterna för den associerade tjänsten. Dessa slutpunkter fungerar med befintliga och nya resurser.

Om du vill migrera en befintlig resurs för att använda anpassade underdomännamn, så att du kan aktivera funktioner som Azure AD, följer du dessa instruktioner:

1. Logga in på Azure-portalen och leta reda på den Cognitive Services-resurs som du vill lägga till ett anpassat underdomännamn till.
2. Leta reda på och välj **Generera anpassat domännamn i** **bladet Översikt** .
3. Detta öppnar en panel med instruktioner för att skapa en unik anpassad underdomän för din resurs.
   > [!WARNING]
   > När du har skapat ett anpassat underdomännamn kan det **inte** ändras.

## <a name="do-i-need-to-update-my-existing-resources"></a>Behöver jag uppdatera mina befintliga resurser?

Nej. Den regionala slutpunkten fortsätter att fungera för nya och befintliga Cognitive Services och det anpassade underdomännamnet är valfritt. Även om ett anpassat underdomännamn läggs till fortsätter den regionala slutpunkten att fungera med resursen.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>Vad händer om en SDK frågar mig om regionen för en resurs?

> [!WARNING]
> **Taltjänsterna** stöder inte anpassade underdomäner just nu. Använd de regionala slutpunkterna när du använder taltjänsterna och tillhörande SDK:er.

Regionala slutpunkter och anpassade underdomännamn stöds båda och kan användas omväxlande. Den fullständiga slutpunkten krävs dock.

Regioninformation är tillgänglig i **bladet Översikt** för din resurs i [Azure-portalen](https://portal.azure.com). För den fullständiga listan över regionala slutpunkter, se [Finns det en lista över regionala slutpunkter?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>Är anpassade underdomännamn regionala?

Ja. Om du använder ett anpassat underdomännamn ändras inte någon av de regionala aspekterna av cognitive services-resursen.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Vilka är kraven för ett anpassat underdomännamn?

Ett anpassat underdomännamn är unikt för din resurs. Namnet kan bara innehålla alfanumeriska tecken och tecknet. `-` Det måste vara mellan 2 och 64 tecken `-`i längd och kan inte sluta med en .

## <a name="can-i-change-a-custom-domain-name"></a>Kan jag ändra ett eget domännamn?

Nej. När ett anpassat underdomännamn har skapats och associerats med en resurs kan det inte ändras.

## <a name="can-i-reuse-a-custom-domain-name"></a>Kan jag återanvända ett eget domännamn?

Varje anpassat underdomännamn är unikt, så för att kunna återanvända ett anpassat underdomännamn som du har tilldelat en Cognitive Services-resurs måste du ta bort den befintliga resursen. När resursen har tagits bort kan du återanvända det anpassade underdomännamnet.

## <a name="is-there-a-list-of-regional-endpoints"></a>Finns det en lista över regionala slutpunkter?

Ja. Det här är en lista över regionala slutpunkter som du kan använda med Azure Cognitive Services-resurser.

> [!NOTE]
> Api:et för översättare och Bing-sökning använder globala slutpunkter.

| Typ av slutpunkt | Region | Slutpunkt |
|---------------|--------|----------|
| Offentlig | Globalt (översättare text & Bing) | `https://api.cognitive.microsoft.com` |
| | Australien, östra | `https://australiaeast.api.cognitive.microsoft.com` |
| | Brasilien, södra | `https://brazilsouth.api.cognitive.microsoft.com` |
| | Kanada, centrala | `https://canadacentral.api.cognitive.microsoft.com` |
| | USA, centrala | `https://centralus.api.cognitive.microsoft.com` |
| | Asien, östra | `https://eastasia.api.cognitive.microsoft.com` |
| | USA, östra | `https://eastus.api.cognitive.microsoft.com` |
| | USA, östra 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | Frankrike, centrala | `https://francecentral.api.cognitive.microsoft.com` |
| | Indien, centrala | `https://centralindia.api.cognitive.microsoft.com` |
| | Japan, östra | `https://japaneast.api.cognitive.microsoft.com` |
| | Sydkorea, centrala | `https://koreacentral.api.cognitive.microsoft.com` |
| | USA, norra centrala | `https://northcentralus.api.cognitive.microsoft.com` |
| | Europa, norra | `https://northeurope.api.cognitive.microsoft.com` |
| | Sydafrika North | `https://southafricanorth.api.cognitive.microsoft.com` |
| | USA, södra centrala | `https://southcentralus.api.cognitive.microsoft.com` |
| | Sydostasien | `https://southeastasia.api.cognitive.microsoft.com` |
| | Storbritannien, södra | `https://uksouth.api.cognitive.microsoft.com` |
| | USA, västra centrala | `https://westcentralus.api.cognitive.microsoft.com` |
| | Europa, västra | `https://westeurope.api.cognitive.microsoft.com` |
| | USA, västra | `https://westus.api.cognitive.microsoft.com` |
| | USA, västra 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | US Gov, Virginia | `https://virginia.api.cognitive.microsoft.us` |
| Kina | Kina Öst 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | Kina, norra | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>Se även

* [Vilka är de kognitiva tjänsterna?](Welcome.md)
* [Autentisering](authentication.md)
