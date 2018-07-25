---
title: Samarbeta med andra deltagare på LUIS-appar i Azure | Microsoft Docs
description: Lär dig hur du samarbeta med andra deltagare på Språkförståelse (LUIS) program.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: diberry
ms.openlocfilehash: 9ea0269439b3d00bf36186cf2fd5c73311526bec
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225609"
---
# <a name="collaborate-with-others-on-language-understanding-luis-apps"></a>Samarbeta med andra appar för Språkförståelse (LUIS)  

Du kan samarbeta med andra på LUIS-appen. 

## <a name="owner-and-collaborators"></a>Ägaren och medarbetare
En app har en enda ägare men kan ha många medarbetare. 

## <a name="add-collaborator"></a>Lägg till medarbetare

Att tillåta medarbetare att redigera din LUIS-app på den **inställningar** sidan för din LUIS-app, anger du e-postadress för medarbetare och klicka på **Lägg till medarbetare**.

![Lägg till medarbetare](./media/luis-how-to-collaborate/add-collaborator.png)

* Medarbetare kan logga in och redigera din LUIS-app samtidigt som du arbetar med appen. <!--If a collaborator edits the LUIS app, you see a notification at the top of the browser.-->
* Medarbetare kan inte lägga till andra medarbetare.

Se [Azure Active Directory-klientanvändare](luis-how-to-account-settings.md#azure-active-directory-tenant-user) mer information om Active Directory-användarkonton. 

## <a name="set-application-as-public"></a>Ange program som offentliga
Se [offentliga slutpunkten appåtkomst](luis-concept-security.md#public-app-endpoint-access) för mer information.

### <a name="transfer-of-ownership"></a>Överlåtelse av äganderätt
Medan LUIS inte stöder för närvarande överlåtelse av äganderätt, du kan exportera din app och en annan LUIS-användare kan importera appen. Det kan finnas mindre skillnader i LUIS poäng mellan de två programmen. 
