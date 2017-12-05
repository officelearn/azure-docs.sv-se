---
title: "Felsöka dina API: er med hjälp av spårning av förfrågan i Azure API Management | Microsoft Docs"
description: "Följ stegen i den här kursen lär dig hur du granska stegen i Azure API Management för begäranden."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 7b9bec7927169b9d820c095a7d11705264e7dcfe
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="debug-your-apis-using-request-tracing"></a>Felsöka dina API: er med hjälp av spårning av förfrågan

Den här självstudiekursen beskrivs hur du inspektera behandling av begäranden för att hjälpa dig med felsökning och felsökning av din API. 

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Spåra ett anrop

![API-inspector](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>Krav

+ Slutför följande Snabbstart: [skapa en instans av Azure API Management](get-started-create-service-instance.md).
+ Dessutom slutföra följande kursen: [Import och publicera din första API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="trace-a-call"></a>Spåra ett anrop

1. Välj **API: er**.
2. Klicka på **Demo konferens API** API-listan.
3. Välj **GetSpeakers** igen.
4. Växla till den **Test** fliken.
5. Se till att använda en HTTP-huvud med namnet **Ocp-Apim-Trace** med värdet **SANT**.
6. Klicka på **”skicka”** att göra ett API-anrop. 
7. Vänta tills anropet för att slutföra. 
8. Gå till den **Trace** fliken i den **API konsolen**. Du kan klicka på någon av följande länkar för att hoppa till detaljerade spårningsloggar info: **inkommande**, **backend**, **utgående**.

    I den **inkommande** avsnittet kan du se den ursprungliga begäran API Management har fått från anroparen och alla principer som tillämpas på begäran inklusive hastighetsbegränsning och set-huvudet principer som vi har lagt till i steg 2.

    I den **backend** avsnittet visas de förfrågningar som API-hantering som skickats till API-backend- och -svar togs emot.
    
    I den **utgående** avsnittet kan du se alla principer som tillämpas på svaret innan du skickar tillbaka till anroparen.
 
    > [!TIP]
    > Varje steg visar även hur lång tid eftersom begäran tas emot av API-hantering.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Spåra ett anrop

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Använd revisioner](api-management-get-started-revise-api.md)
