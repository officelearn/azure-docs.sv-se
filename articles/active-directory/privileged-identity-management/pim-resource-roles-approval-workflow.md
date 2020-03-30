---
title: Godkänna begäranden för Azure-resursroller i PIM - Azure AD | Microsoft-dokument
description: Lär dig hur du godkänner eller nekar begäranden för Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e779f633efccf7b594c193e165a584d22b1d653b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021974"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Godkänna eller neka begäranden om Azure-resursroller i Privilegierad identitetshantering

Med Privilegierad identitetshantering (PIM) i Azure Active Directory (Azure AD) kan du konfigurera roller för godkännande för aktivering och välja användare eller grupper från din Azure AD-organisation som delegerade godkännare. Vi rekommenderar att du väljer två eller flera godkännare för varje roll för att minska arbetsbelastningen för den privilegierade rolladministratören. Delegerade godkännare har 24 timmar på sig att godkänna begäranden. Om en begäran inte godkänns inom 24 timmar måste den berättigade användaren skicka en ny begäran på nytt. Tidsfönstret för 24 timmar godkännande kan inte konfigureras.

Följ stegen i den här artikeln för att godkänna eller neka begäranden om Azure-resursroller.

## <a name="view-pending-requests"></a>Visa väntande begäranden

Som delegerad godkännare får du ett e-postmeddelande när en Azure-resursrollbegäran väntar på ditt godkännande. Du kan visa dessa väntande begäranden i Privilegierad identitetshantering.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Godkänn begäranden**.

    ![Godkänna begäranden - Sidan Azure-resurser som visar begäran om granskning](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    I avsnittet **Begäran om rollaktiveringar** visas en lista över begäranden som väntar på ditt godkännande.

## <a name="approve-requests"></a>Godkänn förfrågningar

1. Sök efter och välj den begäran som du vill godkänna. En godkänn- eller neka-sida visas.

    ![Godkänn begäranden - godkänn eller neka rutan med information och rutan Motivering](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Ange affärsmotiveringen i rutan **Motivering.**

1. Välj **Godkänn**. Du får ett Azure-meddelande om ditt godkännande.

    ![Godkänn meddelande som visar att begäran har godkänts](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Neka begäranden

1. Sök efter och välj den begäran som du vill neka. En godkänn- eller neka-sida visas.

    ![Godkänn begäranden - godkänn eller neka rutan med information och rutan Motivering](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Ange affärsmotiveringen i rutan **Motivering.**

1. Välj **Neka**. Ett meddelande visas med ditt förnekande.

## <a name="workflow-notifications"></a>Meddelanden om arbetsflöde

Här är lite information om arbetsflödesaviseringar:

- Godkännare meddelas via e-post när en begäran om en roll väntar på granskning. E-postmeddelanden innehåller en direkt länk till begäran, där godkännaren kan godkänna eller neka.
- Begäranden löses av den första godkännaren som godkänner eller nekar.
- När en godkännare svarar på begäran meddelas alla godkännare åtgärden.
- Resursadministratörer meddelas när en godkänd användare blir aktiv i sin roll.

>[!Note]
>En resursadministratör som anser att en godkänd användare inte ska vara aktiv kan ta bort den aktiva rolltilldelningen i Privilegierad identitetshantering. Även om resursadministratörer inte meddelas om väntande begäranden om de inte är en godkännare, kan de visa och avbryta väntande begäranden för alla användare genom att visa väntande begäranden i Privilegierad identitetshantering.

## <a name="next-steps"></a>Nästa steg

- [Utöka eller förnya Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-renew-extend.md)
- [E-postmeddelanden i privilegierad identitetshantering](pim-email-notifications.md)
- [Godkänna eller neka begäranden om Azure AD-roller i privilegierad identitetshantering](azure-ad-pim-approval-workflow.md)
