---
title: Hantera Azure-program granska feedback – Azure Marketplace | Microsoft Docs
description: Beskriver hur du använder Azure DevOps för att hantera granska feedback för erbjudanden som Azure-program för Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pbutlerm
ms.openlocfilehash: e5fe076cfe733b4fa0151570603c090af98de0e8
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882696"
---
# <a name="handling-review-feedback"></a>Hantering av granska feedback

Den här artikeln förklarar hur du kommer åt Azure DevOps-miljö som används av granskningsteamet för Microsoft Azure Marketplace.  Om allvarliga problem som hittas i erbjudandet Azure-program under den **Microsoft granska** steg, du kan logga in på det här systemet att visa detaljerad information om dessa ärenden (granska feedback).  När du har åtgärdat alla problem, måste du skicka ditt erbjudande till fortsätta publicera på Azure Marketplace.  Följande diagram illustrerar hur den här feedbackprocessen relaterar till publiceringsprocessen.

![Publicera steg med VSTS-feedback](./media/pub-flow-vsts-access.png)

Granska problem är normalt refereras till som pull-begäran (PR).  Varje pull-begäran är länkad till en Onlinekälla [Azure DevOps](https://azure.microsoft.com/services/devops/) (som tidigare kallades för Visual Studio Team Services (VSTS)) objekt som innehåller information om problemet.  Följande bild visar ett exempel på en granska PR-referens.  För komplexa situationer kan också granska och supportteam e du. 

![Status för fliken visas granska feedback](./media/status-tab-ms-review.png)


## <a name="vsts-access"></a>VSTS-åtkomst

Om du vill visa PR-objekten som refereras i Granska feedback, måste utgivare först tilldelas rätt behörighet.  Annars kan nya utgivare ta emot en `401 - Not Authorized` svar sidan när du försöker visa pull-begäranden.  Utför följande steg för att begära åtkomst till den här Azure DevOps-lagringsplatsen:

1. Samla in följande information:
    - Ditt namn på utgivare och -ID
    - Erbjudandetyp (Azure app), erbjuder namnet och SKU-ID
    - Pull-begäran länka till exempel: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`  Den här URL: en kan hämtas från meddelandet eller adressen till sidan 401-svar.
    - E-postadresserna för personerna från organisationen publishing som du vill bevilja åtkomst till.  Den här listan ska innehålla ägare-adresser som du angav när du registrerar en utgivare på partnerportalen i molnet.
2. Skapa ett supportärende.  I namnlisten i Cloud Partner Portal, väljer du den **hjälpa** knappen och välj sedan **Support** på menyn.  Du default web browser bör starta och gå till Microsoft support incident sidan Ny.  (Du kan behöva logga in först.)
3. Ange den **problemtyp** som **marketplace onboarding** och **kategori** som **problem med anslutningen**och välj sedan **Start begäran**.

    ![Stöd för biljett kategori](./media/support-incident1.png)

4. I **steg 1 av 2** , ange din kontaktinformation och välj **Fortsätt**.
5. I **steg 2 av 2** anger du ett incidentrubrik (till exempel `Request VSTS access`) och ange den information du samlade in i det första steget (ovan).  Läsa och godkänna avtalet och välj sedan **skicka**.

Om incidentskapande, visas en bekräftelsesida.  Spara bekräftelseinformationen som på den här sidan som referens.  Microsoft Support-teamet ska svara på din förfrågan inom ett par arbetsdagar.


## <a name="reviewing-the-pull-request"></a>Granska pull-begäran 

Använd följande procedur för att granska problem som beskrivs i pull-begäran.

1. I den **Microsoft granska** delen av **publicera steg** klickar du på på en PR-länk för att starta webbläsaren och gå till den **översikt** (hemsida) för den här pullförfrågan.  Följande bild visar ett exempel kritiskt problem startsida för apperbjudandet för Contoso-exemplet.  Den här sidan innehåller användbar översiktsinformation om granskning problem finns i Azure-appen.  

    [![Startsida för pull-begäran](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Klicka på bilden för att expandera.*
    
2. (Valfritt) På höger sida av fönstret under **principer**, klicka på problemet meddelandet (i det här exemplet: **Gick inte att verifiera principen**) att undersöka de lägre detaljnivåerna för utfärdande, inklusive de associerade loggfilerna.  Fel vanligtvis visas längst ned i loggfilerna.

3. På menyn på vänster sida på sidan Välj **filer** att visa listan filerna som utgör de tekniska resurserna för det här erbjudandet.  Microsoft-granskare bör har lagt till kommentarer som beskriver de identifierade kritiska problemen.  I följande exempel visas har två problem identifierats. 

    [![Startsida för pull-begäran](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Klicka på bilden för att expandera.*

4. Klicka på varje kommentarsnod i det vänstra trädet att navigera till kommentar i kontexten för den omgivande kod.  Åtgärda källkoden i ditt team-projektet för att åtgärda problemet som beskrivs av kommentaren.

> [!Note]
> Du kan inte redigera ditt erbjudande tekniska resurser inom teamet granska Azure DevOps-miljö.  Detta är en skrivskyddad miljö för inneslutna källkoden för utgivare.  Du kan dock lämna svar på kommentarer till förmån för Microsoft-teamet för granskning.

   I följande exempel har utgivaren granskat korrigerad och besvarade första problemet.

   ![Först åtgärda och kommentera svara](./media/first-comment-reply.png)


## <a name="next-steps"></a>Nästa steg

När du har korrigerat allvarliga problem som beskrivs i Granska PR(s) måste du [publicera ditt erbjudande i Azure app](./cpp-publish-offer.md).
