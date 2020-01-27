---
title: Anpassade under domäner
titleSuffix: Azure Cognitive Services
description: Anpassade under domän namn för varje kognitiv tjänst resurs skapas via Azure Portal, Azure Cloud Shell eller Azure CLI.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647681"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Anpassade under domän namn för Cognitive Services

Azure Cognitive Services använda anpassade under domän namn för varje resurs som skapats via [Azure Portal](https://portal.azure.com), [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)eller [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Till skillnad från regionala slut punkter, som var vanliga för alla kunder i en angiven Azure-region, är anpassade under domän namn unika för resursen. Anpassade under domän namn krävs för att aktivera funktioner som Azure Active Directory (Azure AD) för autentisering.

## <a name="how-does-this-impact-existing-resources"></a>Hur påverkar detta befintliga resurser?

Cognitive Services resurser som skapats före den 1 juli 2019 kommer att använda de regionala slut punkterna för den associerade tjänsten. Dessa slut punkter fungerar med befintliga och nya resurser.

Om du vill migrera en befintlig resurs för att utnyttja anpassade under domän namn, så att du kan aktivera funktioner som Azure AD, följer du dessa anvisningar:

1. Logga in på Azure Portal och leta upp den Cognitive Services resurs som du vill lägga till ett anpassat under domän namn i.
2. Leta upp och välj **skapa anpassat domän namn**på bladet **Översikt** .
3. Då öppnas en panel med instruktioner för att skapa en unik anpassad under domän för din resurs.
   > [!WARNING]
   > När du har skapat ett anpassat under domän namn **kan du inte** ändra det.

## <a name="do-i-need-to-update-my-existing-resources"></a>Måste jag uppdatera mina befintliga resurser?

Nej. Den regionala slut punkten fortsätter att fungera för nya och befintliga Cognitive Services och det anpassade under domän namnet är valfritt. Även om ett anpassat under domän namn läggs till fortsätter den regionala slut punkten att fungera med resursen.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>Vad händer om ett SDK ber mig om en resurs region?

> [!WARNING]
> Tal tjänsterna stöder **inte** anpassade under domäner för tillfället. Använd de regionala slut punkterna när du använder tal tjänsterna och associerade SDK: er.

Regionala slut punkter och anpassade under domän namn stöds både och kan användas utbytbart. Den fullständiga slut punkten krävs dock.

Regions information finns på bladet **Översikt** för resursen i [Azure Portal](https://portal.azure.com). En fullständig lista över regionala slut punkter finns i finns [det en lista över regionala slut punkter?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>Är anpassade under domän namn regionala?

Ja. Om du använder ett eget under domän namn ändras inte några av de regionala aspekterna av din Cognitive Services-resurs.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Vilka är kraven för ett anpassat under domän namn?

Ett eget under domän namn är unikt för din resurs. Namnet får bara innehålla alfanumeriska tecken och `-` tecknet. Det måste vara mellan 2 och 64 tecken långt och får inte sluta med en `-`.

## <a name="can-i-change-a-custom-domain-name"></a>Kan jag ändra ett anpassat domän namn?

Nej. När ett eget under domän namn har skapats och associerats med en resurs går det inte att ändra.

## <a name="can-i-reuse-a-custom-domain-name"></a>Kan jag återanvända ett anpassat domän namn?

Varje anpassat under domän namn är unikt, så för att kunna återanvända ett anpassat under domän namn som du har tilldelat till en Cognitive Services resurs måste du ta bort den befintliga resursen. När resursen har tagits bort kan du återanvända det anpassade under domän namnet.

## <a name="is-there-a-list-of-regional-endpoints"></a>Finns det en lista över regionala slut punkter?

Ja. Det här är en lista över regionala slut punkter som du kan använda med Azure Cognitive Services-resurser.

> [!NOTE]
> Translator Text API och API:er för Bing-sökresultat använder globala slut punkter.

| Slut punkts typ | Region | Slutpunkt |
|---------------|--------|----------|
| Offentligt | Global (Translator Text & Bing) | `https://api.cognitive.microsoft.com` |
| | Östra Australien | `https://australiaeast.api.cognitive.microsoft.com` |
| | Södra Brasilien | `https://brazilsouth.api.cognitive.microsoft.com` |
| | Centrala Kanada | `https://canadacentral.api.cognitive.microsoft.com` |
| | Centrala USA | `https://centralus.api.cognitive.microsoft.com` |
| | Östasien | `https://eastasia.api.cognitive.microsoft.com` |
| | Östra USA | `https://eastus.api.cognitive.microsoft.com` |
| | USA, östra 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | Frankrike, centrala | `https://francecentral.api.cognitive.microsoft.com` |
| | Centrala Indien | `https://centralindia.api.cognitive.microsoft.com` |
| | Östra Japan | `https://japaneast.api.cognitive.microsoft.com` |
| | Sydkorea, centrala | `https://koreacentral.api.cognitive.microsoft.com` |
| | Norra centrala USA | `https://northcentralus.api.cognitive.microsoft.com` |
| | Norra Europa | `https://northeurope.api.cognitive.microsoft.com` |
| | Sydafrika, norra | `https://southafricanorth.api.cognitive.microsoft.com` |
| | Södra centrala USA | `https://southcentralus.api.cognitive.microsoft.com` |
| | Sydostasien | `https://southeastasia.api.cognitive.microsoft.com` |
| | Storbritannien, södra | `https://uksouth.api.cognitive.microsoft.com` |
| | Västra centrala USA | `https://westcentralus.api.cognitive.microsoft.com` |
| | Västra Europa | `https://westeurope.api.cognitive.microsoft.com` |
| | Västra USA | `https://westus.api.cognitive.microsoft.com` |
| | Västra USA 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | Virginia (USA-förvaltad region) | `https://virginia.api.cognitive.microsoft.us` |
| Kina | Kina, östra 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | Kina, norra | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>Se även

* [Vad är Cognitive Services?](Welcome.md)
* [Autentisering](authentication.md)
