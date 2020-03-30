---
title: Hantering av feedback om granskning av Azure-program | Azure Marketplace
description: Förklarar hur du använder Azure DevOps för att hantera granskningsfeedback för Azure-programerbjudanden för Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: dsindona
ms.openlocfilehash: b5f290bae908ac753801eef2c9b8394ca1bb7a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285307"
---
# <a name="handling-review-feedback"></a>Hantera feedback från granskning

I den här artikeln beskrivs hur du kommer åt Azure DevOps-miljön som används av Granskningsteamet för Microsoft Azure Marketplace.  Om kritiska problem hittas i ditt Azure-programerbjudande under Granskningssteget **microsoft** kan du logga in på det här systemet för att visa detaljerad information om dessa problem (granska feedback).  När du har åtgärdat alla dessa problem måste du skicka in ditt erbjudande igen för att fortsätta publicera det på Azure Marketplace.  Följande diagram illustrerar hur den här återkopplingsprocessen relaterar till publiceringsprocessen.

![Publiceringssteg med Azure DevOps-feedback](./media/pub-flow-vsts-access.png)

Vanligtvis refereras granskningsproblem som pull-begäran (PR).  Varje PR är länkad till ett [online-Azure DevOps-objekt](https://azure.microsoft.com/services/devops/) (tidigare kallat Visual Studio Team Services)) som innehåller information om problemet.  Följande bild visar ett exempel på en recension PR-referens.  För komplexa situationer kan gransknings- och supportteamen också skicka e-post till dig. 

![Fliken Status som visar recensionsfeedback](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Azure DevOps-åtkomst

Om du vill visa de PR-objekt som refereras i granskningsfeedback måste utgivare först beviljas korrekt auktorisering.  Annars får nya utgivare `401 - Not Authorized` en svarssida när de försöker visa PRs.  Så här begär du åtkomst till den här Azure DevOps-databasen:

1. Samla in följande information:
    - Utgivarens namn och ID
    - Erbjudandetyp (Azure-app), erbjudandenamn och SKU-ID
    - Länken pull request, till `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` exempel: Den här URL:en kan hämtas från meddelandemeddelandet eller adressen till 401-svarssidan.
    - E-postadressen för de personer från din publiceringsorganisation som du vill ha åtkomst beviljad.  Den här listan ska innehålla den ägaresadress som du angav när du registrerade dig som utgivare på Cloud Partner Portal.
2. Skapa en supportincident.  I namnlisten på Cloud Partner Portal väljer du **hjälpknappen** och väljer sedan **Support** på menyn.  Du standardwebbläsare bör starta och navigera till Microsofts nya supportincidentsida.  (Du kan behöva logga in först.)
3. Ange **problemtypen** som **onboarding och** **Kategori** som **Access-problem**och välj sedan **Startbegäran**.

    ![Kategorin Supportbiljett](./media/support-incident1.png)

4. I **steg 1 av 2** anger du dina kontaktuppgifter och väljer **Fortsätt**.
5. I **steg 2 på 2-sidan** anger `Request Azure DevOps access`du en incidentrubrik (till exempel ) och anger den information som du samlade in i det första steget (ovan).  Läs och acceptera avtalet och välj sedan **Skicka**.

Om händelseskapandet lyckades visas en bekräftelsesida.  Spara bekräftelseinformationen på den här sidan som referens.  Microsofts supportteam bör svara på din åtkomstbegäran inom några arbetsdagar.


## <a name="reviewing-the-pull-request"></a>Granska pull-begäran 

Använd följande procedur för att granska problem som dokumenterats i pull-begäran.

1. I **microsofts granskningsavsnitt** i formuläret **Publiceringssteg** klickar du på en PR-länk för att starta webbläsaren och navigera till sidan **Översikt** (start) för den här PR-enheten.  Följande bild visar ett exempel på en kritisk ärendesida för Contoso-exempelapperbjudandet.  Den här sidan innehåller användbar sammanfattande information om granskningsproblemen som finns i Azure-appen.  

    [![Hämta startsida för begäran](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Klicka på bilden för att expandera.*
    
2. (Valfritt) Klicka på problemet (i det **Policies**här exemplet: **Principvalidering**till höger i fönstret) för att undersöka information på låg nivå om problemet, inklusive tillhörande loggfiler.  Fel visas vanligtvis längst ned i loggfilerna.

3. I menyn till vänster på startsidan väljer du **Filer** för att visa listfilerna som innehåller de tekniska tillgångarna för det här erbjudandet.  Microsoft granskarna bör ha lagt till kommentarer som beskriver de identifierade kritiska problemen.  I följande exempel har två problem upptäckts. 

    [![Hämta startsida för begäran](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Klicka på bilden för att expandera.*

4. Klicka på varje kommentarnod i det vänstra trädet för att navigera till kommentaren i samband med den omgivande koden.  Åtgärda källkoden i gruppens projekt för att korrigera problemet som beskrivs i kommentaren.

> [!Note]
> Du kan inte redigera erbjudandets tekniska resurser i granskningsteamets Azure DevOps-miljö.  För utgivare är detta en skrivskyddad miljö för den inneslutna källkoden.  Du kan dock lämna svar på kommentarerna till förmån för Microsofts granskningsteam.

   I följande exempel har utgivaren granskat, korrigerat och svarat på det första problemet.

   ![Första fix och kommentar svar](./media/first-comment-reply.png)


## <a name="next-steps"></a>Nästa steg

När du har korrigerat de kritiska problem som dokumenterats i gransknings-PR:erna måste du [publicera om erbjudandet om Azure-appen](./cpp-publish-offer.md).
