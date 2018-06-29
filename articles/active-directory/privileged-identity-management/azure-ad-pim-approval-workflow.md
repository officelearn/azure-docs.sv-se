---
title: Azure Privileged Identity Management godkännande arbetsflöden | Microsoft Docs
description: Lär dig mer om godkännandearbetsflöden i Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 135c789dc6e41e07bb939ece679756c8c42de2d1
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085291"
---
# <a name="approvals"></a>Godkännanden

## <a name="overview"></a>Översikt

Med godkännanden för Privileged Identity Management kan du konfigurera roller för att kräva godkännande för aktivering och välja en eller flera användare eller grupper som delegerad godkännare. Vill du fortsätta läsa information om hur du konfigurerar roller och välj godkännare.


## <a name="new-terminology"></a>Nya terminologi

*Behörig användare i rollen* – en användare med berättigade roll är en användare inom din organisation som har tilldelats till en Azure AD-roll som kvalificerade (rollen kräver aktivering).

*Delegerad godkännare* – en delegerad godkännare är en eller flera personer eller grupper i din Azure AD som ansvarar för att godkänna begäranden till Aktivera roller.

## <a name="scenarios"></a>Scenarier

Privata förhandsgranskningen stöder följande scenarion:

**Som en privilegierade rollen Administratör (PRA) kan du:**

-   [aktivera godkännande för specifika roller](#enable-approval-for-specific-roles)

-   [Ange godkännare användare och/eller grupper för att godkänna begäranden](#specify-approver-users-and/or-groups-to-approve-requests)

-   [Visa historiken för begäran och godkännande för alla Privilegierade roller](#view-request-and-approval-history-for-all-privileged-roles)

**Som en utsedda användare kan du:**

-   [Visa väntande godkännanden (antal begäranden)](#view-pending-approvals-requests)

-   [godkänna eller Avvisa begäran för rollen höjning (enkel och/eller)](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [Ange en motivering till min godkännande/nekande](#provide-justification-for-my-approval/rejection) 

**Som en behörig användare i rollen kan du:**

-   [begära aktivering av en roll som kräver godkännande](#request-activation-of-a-role-that-requires-approval)

-   [Visa status för din begäran om att aktivera](#view-the-status-of-your-request-to-activate)

-   [slutföra uppgiften i Azure AD om aktivering har godkänts](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>Navigering

Vi har uppdaterat navigering för att stödja godkännanden

![](media/azure-ad-pim-approval-workflow/image001.png)

Standard landningssida ger åtkomst till information om PIM och den nya godkännande-dokumentationen.

![](media/azure-ad-pim-approval-workflow/image002.png)

Vi har lagt till ett nytt avsnitt för alla användare av PIM Mina granskningshistorik. Här hittar du alla uppgifter som är relevanta för din identitet. Här ingår alla väntande och slutförda begäranden, alla beslut som du har gjort om begäranden som du har löst och alla dina senaste roll-aktiveringar i en och samma plats.

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>Aktivera godkännande för specifika roller

Om du vill aktivera godkännande för en viss roll, Välj Directory roller från det vänstra navigeringsfönstret.

![](media/azure-ad-pim-approval-workflow/image004.png)

Sök efter och välj inställningar i det vänstra navigeringsfönstret i katalogen

![](media/azure-ad-pim-approval-workflow/image006.png)

Välj Privilegierade roller:

![](media/azure-ad-pim-approval-workflow/image009.png)

Välj ”Aktivera” i den kräver godkännande av:

![](media/azure-ad-pim-approval-workflow/image011.png)

När du har aktiverat, expanderar bladet för att visa följande information:

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
Om du inte anger någon godkännare blir PRA(s) standard godkännare. PRA(s) krävs för att godkänna alla aktiveringsbegäranden för den här rollen.

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>Ange godkännare användare och/eller grupper för att godkänna begäranden

Klicka på alternativet för ”Välj godkännare” om du vill delegera godkännande:

![](media/azure-ad-pim-approval-workflow/image015.png)

När bladet välj godkännare läses in, kan du söka efter en specifik användare eller grupp med sökfältet längst upp och att välja i listan i förväg och klicka på ”Välj” när du är klar:

![](media/azure-ad-pim-approval-workflow/image017.png)

Obs: Du kan välja flera användare eller grupper i taget.

Valet visas i listan över valda godkännare som visas nedan:

![](media/azure-ad-pim-approval-workflow/image019.png)

Om du vill ta bort en granskare, klickar du på knappen Ta bort bredvid användarens namn.

Upprepa proceduren för att lägga till ytterligare godkännare.

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>Visa historiken för begäran och godkännande för alla Privilegierade roller

Om du vill visa historik för begäran och godkännande för alla Privilegierade roller, Välj granskningshistorik från instrumentpanelen:

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
Du kan sortera data av åtgärden och leta efter ”aktivering godkända”

### <a name="view-pending-approvals-requests"></a>Visa väntande godkännanden (antal begäranden)

Som en delegerad godkännare får du e-postaviseringar när en begäran väntar på ditt godkännande. Välj fliken ”väntande godkännandebegäranden” i det vänstra navigeringsfältet från instrumentpanelen (i det nya navigeringsfältet) om du vill visa dessa begäranden i PIM-portalen.

![](media/azure-ad-pim-approval-workflow/image023.png)

Därifrån visas en lista med begäranden som väntar på godkännande:

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>Godkänna eller Avvisa begäran för rollen höjning (enkel och/eller)

Välj de begäranden som du vill godkänna eller neka och klicka på knappen i Åtgärdsfältet som motsvarar ditt beslut:

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>Ange en motivering till min godkännande/nekande

Då öppnas ett nytt blad om du vill godkänna eller neka flera begäranden samtidigt. Ange en motivering för ditt beslut och klicka på Godkänn (eller neka) längst ned eller bladet:

![](media/azure-ad-pim-approval-workflow/image029.png)

När processen för begäran är klar Statussymbolen återspeglas beslut som du gjort (i det här exemplet beslutet är godkänna):

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>Begära aktivering av en roll som kräver godkännande

Begär aktivering av en roll som kräver godkännande kan initieras från den gamla PIM-navigeringen eller nya navigering som processen för rollaktivering är densamma. Välj bara en roll i listan över roller för att aktivera:

![](media/azure-ad-pim-approval-workflow/image033.png)

Om en privilegierad roll kräver Multi-Factor Authentication, uppmanas du att slutföra uppgiften först:

![](media/azure-ad-pim-approval-workflow/image035.png)

Slutföra en gång, klicka på Aktivera och ange en motivering (vid behov):

![](media/azure-ad-pim-approval-workflow/image037.png)

Begäranden visas ett meddelande om att begäran väntar på godkännande:

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>Visa status för din begäran om att aktivera

Visa status för en väntande begäran om att aktivera måste kunna nås från den nya navigeringen. Välj fliken ”Mina förfrågningar” i det vänstra navigeringsfältet:

![](media/azure-ad-pim-approval-workflow/image041.png)

Tillstånd för begäran som standard ”väntande”, men du kan växla mellan för att se alla eller nekade begäranden.

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>Slutföra uppgiften i Azure AD om aktivering har godkänts

När begäran har godkänts rollen är aktiv och du kan fortsätta med allt arbete som kräver den här rollen.

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>Nästa steg

Din feedback är viktig för oss. Gärna att dela kommentarer och feedback med oss här!
