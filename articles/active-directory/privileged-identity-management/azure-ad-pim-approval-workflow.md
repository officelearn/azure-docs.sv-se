---
title: Azure Privileged Identity Management-arbetsflöden | Microsoft Docs
description: Lär dig mer om godkännandearbetsflöden i Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 025bcd0cde8d73cfdd4d79a77256a1705950f90a
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444654"
---
# <a name="approvals"></a>Godkännanden

## <a name="overview"></a>Översikt

Med godkännanden för Privileged Identity Management kan du konfigurera roller för att kräva godkännande för aktivering och välja en eller flera användare eller grupper som delegerad godkännare. Vill du fortsätta läsa information om hur du konfigurerar roller och välj godkännare.


## <a name="new-terminology"></a>Ny terminologi

*Berättigad för användarroll* – en berättigad roll-användare är en användare i din organisation som har tilldelats till en Azure AD-roll som berättigade (rollen kräver aktivering).

*Delegerad godkännaren* – en delegerad godkännaren är en eller flera personer eller grupper i din Azure AD som ansvarar för att godkänna förfrågningar om för att aktivera roller.

## <a name="scenarios"></a>Scenarier

Den privata förhandsversionen har stöd för följande scenarier:

**Som en privilegierad roll administratör (PRA) kan du:**

-   [aktivera godkännande för specifika roller](#enable-approval-for-specific-roles)

-   [Ange godkännare användare och/eller grupper för att godkänna begäranden](#specify-approver-users-and/or-groups-to-approve-requests)

-   [Visa historik för begäran och godkännande för alla Privilegierade roller](#view-request-and-approval-history-for-all-privileged-roles)

**Som en avsedda godkännare kan du:**

-   [Visa väntande godkännanden (begäranden)](#view-pending-approvals-requests)

-   [godkänna eller avvisa begäranden om rollen utökade privilegier (enkel och/eller)](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [Ange en motivering för min avvisandet](#provide-justification-for-my-approval/rejection) 

**Som en behörig användare i rollen kan du:**

-   [begära aktivering av en roll som kräver godkännande](#request-activation-of-a-role-that-requires-approval)

-   [Visa status för din begäran att aktivera](#view-the-status-of-your-request-to-activate)

-   [Slutföra uppgiften i Azure AD om aktiveringen godkändes](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>Navigering

Vi har uppdaterat navigering för att stödja godkännanden

![](media/azure-ad-pim-approval-workflow/image001.png)

Standard landningssida ger enkel åtkomst till information om PIM och den nya godkännande-dokumentationen.

![](media/azure-ad-pim-approval-workflow/image002.png)

Vi har också lagt till ett nytt avsnitt för alla användare av PIM, min granskningshistorik. Här hittar du all information som rör din identitet. Detta inkluderar alla pågående och slutförda begäranden, några beslut som du har gjort om begäranden som du har löst och alla tidigare rollaktiveringar på en lättåtkomlig plats.

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>Aktivera godkännande för specifika roller

Aktivera godkännande för en viss roll genom att först välja Directory-roller i det vänstra navigeringsfönstret.

![](media/azure-ad-pim-approval-workflow/image004.png)

Hitta och välj inställningar i det vänstra navigeringsfönstret för Katalogroller

![](media/azure-ad-pim-approval-workflow/image006.png)

Välj Privilegierade roller:

![](media/azure-ad-pim-approval-workflow/image009.png)

Välj ”Aktivera” i den kräver godkännande av:

![](media/azure-ad-pim-approval-workflow/image011.png)

När du har aktiverat, kommer att expandera bladet för att visa följande information:

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
Om du inte anger någon godkännare, blir PRA(s) standard godkännare. PRA(s) skulle krävas för att godkänna alla aktiveringsbegäranden för den här rollen.

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>Ange godkännare användare och/eller grupper för att godkänna begäranden

Klicka på alternativet för ”Välj godkännare” om du vill delegera godkännande:

![](media/azure-ad-pim-approval-workflow/image015.png)

När bladet välj godkännare har lästs in kan du söka efter en viss användare eller grupp i sökfältet längst upp och att välja i listan i förväg och sedan klicka på ”Välj” när du är klar:

![](media/azure-ad-pim-approval-workflow/image017.png)

Obs: Du kan välja flera användare eller grupper i taget.

Ditt val visas i listan över valda godkännare som visas nedan:

![](media/azure-ad-pim-approval-workflow/image019.png)

Om du vill ta bort en godkännare, klickar du på knappen Ta bort bredvid användarens namn.

Upprepa processen för att lägga till ytterligare godkännare.

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>Visa historik för begäran och godkännande för alla Privilegierade roller

Om du vill visa historik för begäran och godkännande för alla Privilegierade roller, Välj granskningshistorik från instrumentpanelen:

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
Du kan sortera data av åtgärden och leta efter ”aktivering Approved”

### <a name="view-pending-approvals-requests"></a>Visa väntande godkännanden (begäranden)

Som en delegerad godkännaren får du e-postmeddelanden när en begäran väntar på ditt godkännande. Välj fliken ”väntande godkännandebegäranden” i det vänstra navigeringsfältet på instrumentpanelen (i den nya navigeringen) om du vill visa dessa begäranden i PIM-portalen.

![](media/azure-ad-pim-approval-workflow/image023.png)

Därifrån kan visas en lista över begäranden väntar på godkännande:

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>Godkänna eller avvisa begäranden om rollen utökade privilegier (enkel och/eller)

Välj de förfrågningar som du vill godkänna eller neka och klicka på knappen i Åtgärdsfältet som motsvarar ditt beslut:

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>Ange en motivering för min avvisandet

Då öppnas ett nytt blad för att godkänna eller neka flera begäranden samtidigt. Ange en motivering till ditt beslut och klicka på Godkänn (eller neka) på nederkant eller bladet:

![](media/azure-ad-pim-approval-workflow/image029.png)

När begäran-processen är klar visas symbolen för statusen återspeglas beslut som du gjort (i det här exemplet beslutet är godkänna):

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>Begär aktivering av en roll som kräver godkännande

Begär aktivering av en roll som kräver godkännande kan initieras från det gamla PIM-navigeringsfönstret eller den nya navigeringen när processen för rollaktivering förblir densamma. Välj helt enkelt en roll i listan över roller för att aktivera:

![](media/azure-ad-pim-approval-workflow/image033.png)

Om en privilegierad roll kräver Multi-Factor Authentication, uppmanas du att slutföra uppgiften först:

![](media/azure-ad-pim-approval-workflow/image035.png)

När det är klart, klickar på Aktivera och ange en motivering (vid behov):

![](media/azure-ad-pim-approval-workflow/image037.png)

Begäranden visas ett meddelande om att begäran väntar på godkännande:

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>Visa status för din begäran att aktivera

Visa status för en väntande begäran om att aktivera måste kunna nås från den nya navigeringen. I det vänstra navigeringsfältet, klicka på fliken ”Mina förfrågningar”:

![](media/azure-ad-pim-approval-workflow/image041.png)

Tillstånd för begäran som standard som ”väntande”, men du kan växla för att se alla eller nekas begäranden.

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>Slutföra uppgiften i Azure AD om aktiveringen godkändes

När begäran har godkänts rollen är aktiv och du kan fortsätta med allt arbete som kräver den här rollen.

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>Nästa steg

Din feedback är viktig för oss. Ställ gärna lämna kommentarer eller feedback till oss här!
