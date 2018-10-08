---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: fcf3bf29e2cdf89bdc93c7ebac313e5d9a9c18f0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044042"
---
Du kommer åt slutpunkten för förutsägelser med en slutpunktsnyckel. I den här snabbstarten kan du använda den kostnadsfria startnyckeln som medföljde LUIS-kontot. 
 
1. Logga in med ditt LUIS-konto. 

    [![Skärmbild av applistan i LUIS](media/cognitive-services-luis/app-list.png "Skärmbild av applistan i LUIS")](media/cognitive-services-luis/app-list.png)

2. Välj ditt namn på menyn uppe till höger och välj sedan **Inställningar**.

    ![Åtkomst till menyn för användarinställningar i LUIS](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. Kopiera värdet för **Authoring key** (Redigeringsnyckel). Du kommer att använda den senare i snabbstarten. 

    [![Skärmbild av användarinställningarna i LUIS](media/cognitive-services-luis/get-user-authoring-key.png "Skärmbild av användarinställningarna i LUIS")](media/cognitive-services-luis/get-user-authoring-key.png)

    Med redigeringsnyckeln kan du göra ett obegränsat antal kostnadsfria förfrågningar till redigerings-API:t och köra upp till 1 000 frågor mot slutpunkts-API:t för förutsägelser per månad för alla dina LUIS-appar. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->