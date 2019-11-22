---
title: Hantera gransknings feedback för Azure Apps-erbjudandet på den kommersiella marknaden
description: Så här hanterar du gransknings feedback för Azure Apps-erbjudandet för att visa eller sälja på Azure Marketplace, AppSource eller via program varan för Cloud Solution Provider (CSP) med hjälp av den kommersiella Marketplace-portalen på Microsoft Partner Center.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 7406a6e35929e3df5d342affbcb4b740efa0cab8
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281225"
---
# <a name="handling-review-feedback"></a>Hantera feedback från granskning

Den här artikeln förklarar hur du får åtkomst till Azure DevOps-miljön som används av Microsoft Azure Marketplace gransknings teamet. Om det finns kritiska problem i ditt erbjudande om Azure-program under **Microsoft gransknings** steg kan du logga in på systemet för att visa detaljerad information om de här problemen (granska feedback). När du har åtgärdat alla dessa problem måste du skicka erbjudandet igen för att fortsätta att publicera det på Azure Marketplace. Följande diagram illustrerar hur den här återställnings processen relaterar till publicerings processen.

![Granska feedback process](./media/review-feedback-process.png)

Normalt refereras gransknings problem som pull-begäran (PR). Varje PR är länkat till ett online [Azure-DevOps](https://azure.microsoft.com/services/devops/) (tidigare kallat Visual Studio Team Services-objekt (VSTS)) som innehåller information om problemet. Följande bild visar ett exempel på Partner Center-upplevelsen om problem påträffas under granskningar. 

![Publiceringsstatus](./media/publishing-status.png)

PR som innehåller detaljerad information om sändningen visas i länken "Visa certifierings rapport". I komplexa situationer kan gransknings-och support team också e-posta dig.

## <a name="azure-devops-access"></a>Åtkomst till Azure DevOps

Alla användare med åtkomst till rollen "utvecklare" i Partner Center har åtkomst för att se PR-objekten som refereras till i granska feedback.

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

Använd följande procedur för att granska problem som dokumenteras i pull-begäran.

1. I formuläret **gransknings** avsnitt i publicerings steg klickar du på en PR-länk för att starta webbläsaren och gå till sidan **Översikt** (start) för detta. Följande bild visar ett exempel på Start sidan för kritiska problem för erbjudandet för Contoso-exempelprogrammet. Den här sidan innehåller användbar sammanfattnings information om de gransknings problem som finns i Azure-appen.

    [Start sidan för![pull-begäran](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Klicka på bilden för att expandera.*

1. Valfritt Till höger i fönstret, i avsnittet **principer**, klickar du på problem meddelandet (i det här exemplet: **princip valideringen misslyckades**) för att undersöka lågnivå information om problemet, inklusive de associerade loggfilerna. Fel visas vanligt vis längst ned i loggfilerna.
1. På menyn på vänster sida av start sidan väljer du **filer** för att visa de filer som utgör de tekniska till gångarna för det här erbjudandet. Microsoft-granskarna bör ha lagt till kommentarer som beskriver de identifierade kritiska problemen. I följande exempel har två problem identifierats.

    [Start sidan för![pull-begäran](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Klicka på bilden för att expandera.*

1. Klicka på varje kommentar-nod i det vänstra trädet för att navigera till kommentaren i kontexten för den omgivande koden. Åtgärda problemet som beskrivs i kommentaren genom att korrigera käll koden i teamets projekt.

>[!Note]
>Du kan inte redigera ditt erbjudandes tekniska till gångar i gransknings teamets Azure DevOps-miljö. För utgivare är detta en skrivskyddad miljö för den inneslutna käll koden. Du kan dock lämna svar på kommentarer till Microsoft gransknings teamets fördel.

   I följande exempel har utgivaren granskat, korrigerat och besvarat det första problemet.

   ![Första korrigering och kommentar svar](./media/first-comment-reply.png)

## <a name="next-steps"></a>Nästa steg

När du har korrigerat de kritiska problem som dokumenterats i gransknings PR (s), måste du [Publicera om erbjudandet för Azure-appen](./create-new-azure-apps-offer.md#publish).
