---
title: Hantera feedback om granskning för Azure Apps-erbjudande på den kommersiella marknadsplatsen
description: Hantera granskningsfeedback för Azure Apps-erbjudande för att lista eller sälja i Azure Marketplace, AppSource eller via CSP-programmet (Cloud Solution Provider) med hjälp av Portalen Commercial Marketplace på Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 3932f3fc71dc6427b6cdf93d3a7bc58534a9981c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279801"
---
# <a name="handling-review-feedback"></a>Hantera feedback från granskning

I den här artikeln beskrivs hur du kommer åt Azure DevOps-miljön som används av Granskningsteamet för Microsoft Azure Marketplace. Om kritiska problem hittas i ditt Azure-programerbjudande under Granskningssteget **microsoft** kan du logga in på det här systemet för att visa detaljerad information om dessa problem (granska feedback). När du har åtgärdat alla dessa problem måste du skicka in ditt erbjudande igen för att fortsätta publicera det på Azure Marketplace. Följande diagram illustrerar hur den här återkopplingsprocessen relaterar till publiceringsprocessen.

![Granska feedbackprocessen](./media/review-feedback-process.png)

Vanligtvis refereras granskningsproblem som pull-begäran (PR). Varje PR är länkad till ett [online-Azure DevOps-objekt](https://azure.microsoft.com/services/devops/) (tidigare kallat Visual Studio Team Services)) som innehåller information om problemet. Följande bild visar ett exempel på partnercentrets upplevelse om problem hittas under granskningar. 

![Publiceringsstatus](./media/publishing-status.png)

Den PR som innehåller specifika detaljer om inlämningen kommer att nämnas i länken "Visa certifieringsrapport". För komplexa situationer kan gransknings- och supportteamen också skicka e-post till dig.

## <a name="azure-devops-access"></a>Azure DevOps-åtkomst

Alla användare med åtkomst till rollen "utvecklare" i Partner Center har åtkomst till de PR-objekt som refereras i granskningsfeedback.

<!---
To view the PR items referenced in review feedback, publishers must first be granted proper authorization. Otherwise, new publishers receive a 401 - Not Authorized response page when trying to view PRs. To request access to this Azure DevOps repository, perform the following steps:

1. Collect the following information:
    * Your publisher name and ID
    * Offer type (Azure app), offer name, and SKU ID
    * The pull request link, for example: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`. This URL can be retrieved from the notification message or the address of the 401 response page.
    * The email address(es) of the individuals from your publishing organization that you want access granted to. This list should include the owner address(es) you provided when registering as a publisher on the Cloud Partner Portal (or Partner Center??).
1. Create a support incident. In the title bar of the Cloud Partner Portal (??), select the **Help** button, then choose **Support** from the menu. Your default web browser should launch and navigate to the Microsoft new support incident page. (You may have to sign in first.)
1. Specify the **Problem type** as **marketplace onboarding** and **Category** as **Access problem**, then select **Start request**.

    ![Create an incident](./media/create-an-incident.png)

1. In **Step 1 of 2** page, supply your contact information and select **Continue**.
1. In **Step 2 of 2** page, specify an incident title (for example Request Azure DevOps access) and supply the information you collected in the first step (above). Read and accept the agreement, then select **Submit**.

If the incident creation was successful, a confirmation page is displayed. Save the confirmation information on this page for your reference. The Microsoft Support Team should reply to your access request within a few business days. --->

## <a name="reviewing-the-pull-request"></a>Granska pull-begäran

Använd följande procedur för att granska problem som dokumenterats i pull-begäran.

1. I **formuläret Microsofts granskningsavsnitt** i formuläret Publiceringssteg klickar du på en PR-länk för att starta webbläsaren och navigerar till sidan **Översikt** (start) för den här PR-enheten. Följande bild visar ett exempel på den kritiska frågans startsida för contoso-exempelapperbjudandet. Den här sidan innehåller användbar sammanfattande information om granskningsproblemen som finns i Azure-appen.

    [![Hämta startsida för begäran](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Klicka på bilden för att expandera.*

1. (Valfritt) Klicka på problemet (i det **Policies**här exemplet: **Principvalidering**till höger i fönstret) för att undersöka information på låg nivå om problemet, inklusive tillhörande loggfiler. Fel visas vanligtvis längst ned i loggfilerna.
1. I menyn till vänster på startsidan väljer du **Filer** för att visa listfilerna som innehåller de tekniska tillgångarna för det här erbjudandet. Microsoft granskarna bör ha lagt till kommentarer som beskriver de identifierade kritiska problemen. I följande exempel har två problem upptäckts.

    [![Hämta startsida för begäran](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Klicka på bilden för att expandera.*

1. Klicka på varje kommentarnod i det vänstra trädet för att navigera till kommentaren i samband med den omgivande koden. Åtgärda källkoden i gruppens projekt för att korrigera problemet som beskrivs i kommentaren.

>[!Note]
>Du kan inte redigera erbjudandets tekniska resurser i granskningsteamets Azure DevOps-miljö. För utgivare är detta en skrivskyddad miljö för den inneslutna källkoden. Du kan dock lämna svar på kommentarerna till förmån för Microsofts granskningsteam.

   I följande exempel har utgivaren granskat, korrigerat och svarat på det första problemet.

   ![Första fix och kommentar svar](./media/first-comment-reply.png)

## <a name="next-steps"></a>Nästa steg

När du har korrigerat de kritiska problem som dokumenterats i gransknings-PR:erna måste du [publicera om erbjudandet om Azure-appen](./create-new-azure-apps-offer.md#publish).
