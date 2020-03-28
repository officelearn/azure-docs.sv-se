---
title: Felsöka dina API:er med hjälp av spårning av förfrågan i Azure API Management | Microsoft Docs
description: Följ stegen i den här självstudien för att lära dig hur du kontrollerar stegen för bearbetningen av begäran i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: fc5e8c7a7aa0d4693d96c3405ec0e180a6d13f8e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75768543"
---
# <a name="debug-your-apis-using-request-tracing"></a>Felsöka API:er med hjälp av spårning av förfrågningar

I den här självstudien beskrivs hur du kontrollerar bearbetning av begäran för felsökning av ditt API. 

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Spåra ett anrop

![API Inspector](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>Krav

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

## <a name="trace-a-call"></a>Spåra ett anrop

![API-spårning](media/api-management-howto-api-inspector/06-DebugYourAPIs-01-TraceCall.png)

1. Välj **API:er**.
2. Klicka på **Demo Conference API** i API-listan.
3. Växla till fliken **Test**.
4. Välj åtgärden **GetSpeakers**.
5. Se till att ta med HTTP-huvudet med namnet **Ocp-Apim-Trace** med värdet satt till **true**.

   > [!NOTE]
   > * Om Ocp-Apim-Subscription-Key inte fylls i automatiskt kan du hämta den genom att gå till Utvecklarportalen och exponera nycklarna på profilsidan.
   > * Om du vill få en spårning när HTTP-huvudet för Ocp-Apim-Trace används måste inställningen **Tillåt spårning** för prenumerationsnyckeln vara aktiverad. Om du vill konfigurera inställningen **Tillåt spårning** väljer du **Prenumerationer**under **API-hantering** på den vänstra menyn .
   >   ![Tillåt spårning i fönstret API Management-prenumerationer](media/api-management-howto-api-inspector/allowtracing.png)

6. Klicka på **Skicka** om du vill ringa ett API-anrop. 
7. Vänta tills anropet är klart. 
8. Gå till fliken **Spåra** i **API-konsolen**. Klicka på någon av följande länkar för att komma till detaljerad spårningsinfo: **inkommande**, **serverdel**, **utgående**.

    Under **inkommande** kan du se den ursprungliga begäran som API Management fick från anroparen och alla principer som tillämpas på begäran, inklusive hastighetsbegränsning och angivet sidhuvud som lades till i steg 2.

    Under **serverdelen** visas de förfrågningar som API Management skickade till serverdelen för API:et och svaret den fick.

    Under **utgående** visas alla principer som tillämpas på svaret innan det skickas tillbaka till anroparen.

    > [!TIP]
    > Alla steg visar också hur lång tid det tog efter att begäran togs emot av API Management.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Spåra ett anrop

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Använd revideringar](api-management-get-started-revise-api.md)
