---
title: Hämta prenumerationsnycklar - visuellt innehåll
titlesuffix: Azure Cognitive Services
description: Lär dig mer om att hämta prenumerationsnycklar för anrop till API för visuellt innehåll i Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 05/19/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: c4c7ee1ab756f458c6e9fed4ff5ee54dfaba919e
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55222243"
---
# <a name="how-to-obtain-subscription-keys"></a>Hur du hämtar prenumerationsnycklar

Datorn Vision services kräver särskilda prenumerationsnycklar. Varje anrop till ett API för visuellt innehåll kräver en prenumerationsnyckel. Nyckeln måste antingen skickas via en frågesträngparameter eller anges i begärans sidhuvud.

Om du vill registrera dig för prenumerationsnycklar, se [prenumerationer](https://azure.microsoft.com/try/cognitive-services/). Det är kostnadsfritt att registrera dig. Priserna för dessa tjänster kan komma att ändras.

>[!NOTE]
Din prenumerationsnycklar är giltiga för endast en av dessa [Microsoft Azure-regionerna](https://azure.microsoft.com/regions/). 

| Region | Adress |
|---|---|
| Västra USA | westus.API.cognitive.microsoft.com |
| USA, östra 2 | eastus2.api.cognitive.microsoft.com |
| Västra centrala USA | westcentralus.api.cognitive.microsoft.com |
| Västeuropa | westeurope.api.cognitive.microsoft.com |
| Sydostasien | southeastasia.api.cognitive.microsoft.com |

Om du registrerar dig med hjälp av den kostnadsfria utvärderingsversionen av visuellt innehåll, dina prenumerationsnycklar är giltiga för den **westcentral** region (`https://westcentralus.api.cognitive.microsoft.com/`). Det är i de flesta fall. Men om du registrerar dig för visuellt innehåll med Microsoft Azure-konto via den [ https://azure.microsoft.com/ ](https://azure.microsoft.com/) webbplatsen du anger regionen för din utvärderingsversion i ovanstående lista över regioner.

Exempel: Om du registrerar dig för visuellt innehåll med Microsoft Azure-konto och du anger `westus` för din region, måste du använda den `westus` region för REST API-anrop (`https://westus.api.cognitive.microsoft.com/`).

Om du glömmer regionen för din prenumerationsnyckel när du har fått din utvärderingsversion nyckel kan du hitta din region på [ https://azure.microsoft.com/try/cognitive-services/my-apis/ ](https://azure.microsoft.com/try/cognitive-services/my-apis/).

### <a name="related-links"></a>Relaterade länkar:

* [Prisalternativ för API: er för Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
