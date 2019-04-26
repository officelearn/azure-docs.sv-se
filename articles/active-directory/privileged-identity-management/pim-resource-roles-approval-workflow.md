---
title: Godkänn eller neka begäranden för Azure-resursroller i PIM - Azure Active Directory | Microsoft Docs
description: Lär dig mer om att godkänna eller neka begäranden för Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f645b7077ef43dc7eb4d70261b6b601b5e4af1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60288473"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Godkänn eller neka begäranden för Azure-resursroller i PIM

Med Azure Active Directory (Azure AD) Privileged Identity Management (PIM), kan du konfigurera roller för att kräva godkännande för aktivering och välja en eller flera användare eller grupper som delegerad godkännare. Delegerad godkännare har 24 timmar att godkänna förfrågningar. Om en begäran inte godkänns inom 24 timmar, måste behörig användare igen att skicka en ny begäran. Tidsfönstret 24-timmars godkännande kan inte konfigureras.

Följ stegen i den här artikeln att godkänna eller neka begäranden för Azure-resursroller.

## <a name="view-pending-requests"></a>Visa väntande begäranden

Som en delegerad godkännaren får du ett e-postmeddelande när en begäran om Azure-resurs roll är väntar på ditt godkännande. Du kan visa dessa väntande begäranden i PIM.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **godkänna förfrågningar**.

    ![Azure-resurser – Godkänn ansökningar](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    I den **förfrågningar om rollaktiveringar** avsnittet visas en lista över begäranden väntar på ditt godkännande.

## <a name="approve-requests"></a>Godkänn förfrågningar

1. Hitta och på begäran som du vill godkänna. En godkännande-fönstret visas.

    ![Godkänna begäranden fönstret](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. I den **motivering** skriver en orsak.

1. Klicka på **godkänna**.

    Ett meddelande visas med ditt godkännande.

    ![Godkänn meddelande](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Neka förfrågningar

1. Hitta och klicka på den begäran som du vill neka. En godkännande-fönstret visas.

    ![Godkänna begäranden fönstret](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. I den **motivering** skriver en orsak.

1. Klicka på **neka**.

    Ett meddelande visas med din datorn.

## <a name="workflow-notifications"></a>Arbetsflödesmeddelanden

Här är lite information om arbetsflödesmeddelanden:

- Alla medlemmar i listan över godkännare meddelas via e-post när en begäran för en roll väntar på sina granskningar. E-postmeddelanden innehåller en direktlänk till begäran, där godkännaren kan godkänna eller neka.
- Begäranden kan matchas med den första medlemmen i listan som godkänner eller nekar.
- När en godkännare svarar på begäran, meddelas alla medlemmar i listan över godkännare för åtgärden.
- Resurs-administratörer får ett meddelande när en godkända medlemmen aktiveras i deras roll.

>[!Note]
>En anser att en godkända medlemmen inte får vara aktiva resurs-administratör kan ta bort aktiv rolltilldelning i PIM. Även om resursen administratörer meddelas inte om väntande begäranden om de inte är medlemmar i listan över godkännare, kan de visa och avbryta väntande begäranden för alla användare genom att visa väntande begäranden i PIM. 

## <a name="next-steps"></a>Nästa steg

- [Utöka eller förnya Azure-resursroller i PIM](pim-resource-roles-renew-extend.md)
- [E-postmeddelanden i PIM](pim-email-notifications.md)
- [Godkänn eller neka begäranden för Azure AD-roller i PIM](azure-ad-pim-approval-workflow.md)
