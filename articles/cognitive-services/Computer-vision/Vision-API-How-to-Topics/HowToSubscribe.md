---
title: Hämta prenumerationsnycklar - visuellt innehåll
titlesuffix: Azure Cognitive Services
description: Lär dig mer om att hämta prenumerationsnycklar för anrop till API för visuellt innehåll i Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 05/19/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 03e519520d4a956a5c9690dc1327089505aafced
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120864"
---
# <a name="how-to-obtain-subscription-keys"></a>Hur du hämtar prenumerationsnycklar

Datorn Vision services kräver särskilda prenumerationsnycklar. Varje anrop till ett API för visuellt innehåll kräver en prenumerationsnyckel. Nyckeln måste antingen skickas via en frågesträngparameter eller anges i begärans sidhuvud.

Om du vill registrera dig för prenumerationsnycklar, se [prenumerationer](https://azure.microsoft.com/try/cognitive-services/). Det är kostnadsfritt att registrera dig. Priserna för dessa tjänster kan komma att ändras.

> [!NOTE]
> Din prenumerationsnycklar är giltiga för endast en av dessa [Microsoft Azure-regionerna](https://azure.microsoft.com/regions/). 

| Region | Adress |
|---|---|
| Västra USA | westus.API.cognitive.microsoft.com |
| USA, östra 2 | eastus2.api.cognitive.microsoft.com |
| Västra centrala USA | westcentralus.api.cognitive.microsoft.com |
| Västra Europa | westeurope.api.cognitive.microsoft.com |
| Sydostasien | southeastasia.api.cognitive.microsoft.com |

Om du registrerar dig med hjälp av den kostnadsfria utvärderingsversionen av visuellt innehåll, dina prenumerationsnycklar är giltiga för den **westcentral** region (`https://westcentralus.api.cognitive.microsoft.com/`). Det är i de flesta fall. Men om du registrerar dig för visuellt innehåll med Microsoft Azure-konto via den [ https://azure.microsoft.com/ ](https://azure.microsoft.com/) webbplatsen du anger regionen för din utvärderingsversion i ovanstående lista över regioner.

Exempel: Om du registrerar dig för visuellt innehåll med Microsoft Azure-konto och du anger `westus` för din region, måste du använda den `westus` region för REST API-anrop (`https://westus.api.cognitive.microsoft.com/`).

Om du glömmer regionen för din prenumerationsnyckel när du har fått din utvärderingsversion nyckel kan du hitta din region på [ https://azure.microsoft.com/try/cognitive-services/my-apis/ ](https://azure.microsoft.com/try/cognitive-services/my-apis/).

### <a name="related-links"></a>Relaterade länkar:

* [Prisalternativ för API: er för Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
