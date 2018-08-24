---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 25a70d314ecb690a34e89a3dff7d2c4fad8b7a00
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819876"
---
Åtkomst till slutpunkten för förutsägelse anges med en slutpunktsnyckel som. I den här snabbstarten, använda kostnadsfria startprogrammet nyckeln som associeras med ditt Understanding Intelligent Service-konto. 
 
1. Logga in med ditt Understanding Intelligent Service-konto. 

    [![Skärmbild av Språkförståelse (LUIS) applista](media/cognitive-services-luis/app-list.png "skärmbild för Språkförståelse (LUIS) app-lista")](media/cognitive-services-luis/app-list.png)

2. Välj ditt namn i övre högra menyn och välj sedan **inställningar**.

    ![LUIS användaråtkomst inställningarna menyn](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. Kopiera värdet för den **redigering nyckeln**. Du kan använda den senare i snabbstarten. 

    [![Skärmbild av Språkförståelse (LUIS) användarinställningar](media/cognitive-services-luis/get-user-authoring-key.png "användarinställningar för skärmbild för Språkförståelse (LUIS)")](media/cognitive-services-luis/get-user-authoring-key.png)

    Redigering nyckel kan kostnadsfria obegränsat antal förfrågningar till redigering API och upp till 1000 frågor till slutpunkten för förutsägelse API per månad för alla dina LUIS-appar. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->