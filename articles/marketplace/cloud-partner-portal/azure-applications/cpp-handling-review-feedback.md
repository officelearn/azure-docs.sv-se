---
title: Hantera feedback för Azure-programgranskning | Azure Marketplace
description: Förklarar hur du använder Azure dataDevOpss för att hantera gransknings feedback för Azures program erbjudanden för Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pabutler
ms.openlocfilehash: ef4aff57948034fb369bd74564306b7b8674b377
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827587"
---
# <a name="handling-review-feedback"></a>Hantera feedback från granskning

Den här artikeln förklarar hur du får åtkomst till Azure DevOps-miljön som används av Microsoft Azure Marketplace gransknings teamet.  Om det finns kritiska problem i ditt erbjudande om Azure-program under **Microsoft gransknings** steg kan du logga in på systemet för att visa detaljerad information om de här problemen (granska feedback).  När du har åtgärdat alla dessa problem måste du skicka erbjudandet igen för att fortsätta att publicera det på Azure Marketplace.  Följande diagram illustrerar hur den här återställnings processen relaterar till publicerings processen.

![Publicera steg med Azure DevOps feedback](./media/pub-flow-vsts-access.png)

Normalt refereras gransknings problem som pull-begäran (PR).  Varje PR är länkat till ett online [Azure-DevOps](https://azure.microsoft.com/services/devops/) (tidigare kallat Visual Studio Team Services-objekt (VSTS)) som innehåller information om problemet.  Följande bild visar ett exempel på en recension PR-referens.  I komplexa situationer kan gransknings-och support team också e-posta dig. 

![Fliken status visar gransknings information](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Åtkomst till Azure DevOps

Om du vill visa PR-objekten som refereras i granska feedback måste utgivare först beviljas korrekt behörighet.  I annat fall får nya utgivare en `401 - Not Authorized` svars sida när du försöker visa pull.  Utför följande steg för att begära åtkomst till den här Azure DevOps-lagringsplatsen:

1. Samla in följande information:
    - Ditt utgivar namn och ID
    - Erbjudande typ (Azure App), erbjudande namn och SKU-ID
    - Länken pull-begäran, till exempel: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` den här URL: en kan hämtas från aviserings meddelandet eller adressen till svars sidan för 401.
    - E-postadresserna för de individer från publicerings organisationen som du vill bevilja åtkomst till.  Den här listan bör innehålla de ägar adresser som du angav när du registrerade dig som utgivare på Cloud Partner Portal.
2. Skapa en support incident.  I namn listen för Cloud Partner Portal väljer du knappen **Hjälp** och väljer sedan **stöd** på menyn.  Du ska starta standard webbläsaren och gå till sidan Microsoft ny support incident.  (Du kan behöva logga in först.)
3. Ange **problem typen** som **Marketplace-onboarding** och **kategori** som **åtkomst problem**och välj sedan **Starta förfrågan**.

    ![Kategorin support ärende](./media/support-incident1.png)

4. Ange din kontakt information på sidan **steg 1 av 2** och välj **Fortsätt**.
5. På sidan **steg 2 av 2** anger du en incident rubrik (till exempel `Request Azure DevOps access`) och anger den information som du samlade in i det första steget (ovan).  Läs och godkänn avtalet och välj sedan **Skicka**.

Om incidenten har skapats visas en bekräftelse sida.  Spara bekräftelse informationen på den här sidan för din referens.  Microsoft Support-teamet bör svara på din åtkomstbegäran inom några arbets dagar.


## <a name="reviewing-the-pull-request"></a>Granska pull-begäran 

Använd följande procedur för att granska problem som dokumenteras i pull-begäran.

1. I avsnittet **granskning av Microsoft** i **publicerings steg** klickar du på en PR-länk för att starta webbläsaren och gå till sidan **Översikt** (start) för detta PR.  Följande bild visar ett exempel på ett kritiskt problem på Start sidan för erbjudandet contoso-exempel.  Den här sidan innehåller användbar sammanfattnings information om de gransknings problem som finns i Azure-appen.  

    [Start sidan för![pull-begäran](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Klicka på bilden för att expandera.*
    
2. Valfritt Till höger i fönstret, i avsnittet **principer**, klickar du på problem meddelandet (i det här exemplet: **princip valideringen misslyckades**) för att undersöka lågnivå information om problemet, inklusive de associerade loggfilerna.  Fel visas vanligt vis längst ned i loggfilerna.

3. På menyn på vänster sida av start sidan väljer du **filer** för att visa de filer som utgör de tekniska till gångarna för det här erbjudandet.  Microsoft-granskarna bör ha lagt till kommentarer som beskriver de identifierade kritiska problemen.  I följande exempel har två problem identifierats. 

    [Start sidan för![pull-begäran](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Klicka på bilden för att expandera.*

4. Klicka på varje kommentar-nod i det vänstra trädet för att navigera till kommentaren i kontexten för den omgivande koden.  Åtgärda problemet som beskrivs i kommentaren genom att korrigera käll koden i teamets projekt.

> [!Note]
> Du kan inte redigera ditt erbjudandes tekniska till gångar i gransknings teamets Azure DevOps-miljö.  För utgivare är detta en skrivskyddad miljö för den inneslutna käll koden.  Du kan dock lämna svar på kommentarer till Microsoft gransknings teamets fördel.

   I följande exempel har utgivaren granskat, korrigerat och besvarat det första problemet.

   ![Första korrigering och kommentar svar](./media/first-comment-reply.png)


## <a name="next-steps"></a>Nästa steg

När du har korrigerat de kritiska problem som dokumenterats i gransknings PR (s), måste du [Publicera om erbjudandet för Azure-appen](./cpp-publish-offer.md).
