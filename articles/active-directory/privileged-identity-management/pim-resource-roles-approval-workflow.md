---
title: Godkänn eller neka begär Anden för Azure Resource roles i PIM-Azure Active Directory | Microsoft Docs
description: Lär dig hur du godkänner eller nekar begär Anden för Azures resurs roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b39434f8763e44a126f74ac9a19596e4413ae9c
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804268"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Godkänn eller neka begär Anden för Azure Resource roles i PIM

Med Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kan du konfigurera roller för att kräva godkännande för aktivering och välja en eller flera användare eller grupper som delegerade god kännare. Delegerade god kännare har 24 timmar på sig att godkänna begär Anden. Om en begäran inte godkänns inom 24 timmar måste den behöriga användaren skicka en ny begäran igen. Tids perioden för godkännande av 24 timmar kan inte konfigureras.

Följ stegen i den här artikeln för att godkänna eller Neka förfrågningar om Azure-resurs roller.

## <a name="view-pending-requests"></a>Visa väntande begär Anden

Som en delegerad god kännare får du ett e-postmeddelande när en Azure Resource Role-begäran väntar på ditt godkännande. Du kan visa dessa väntande begär anden i PIM.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Godkänn begär Anden**.

    ![Sidan Godkänn förfrågningar – Azure-resurser som visar begäran om granskning](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    I avsnittet **begär Anden om roll aktiveringar** visas en lista över begär Anden som väntar på ditt godkännande.

## <a name="approve-requests"></a>Godkänn ansökningar

1. Leta upp och klicka på den begäran som du vill godkänna. Rutan Godkänn eller neka visas.

    ![Rutan Godkänn begär Anden – Godkänn eller neka i fönstret med information och motivering](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Skriv en orsak i rutan **motivering** .

1. Klicka på **Godkänn**.

    Ett meddelande visas med ditt godkännande.

    ![Godkänn meddelandet som visar att begäran har godkänts](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Neka förfrågningar

1. Leta upp och klicka på den begäran som du vill neka. Rutan Godkänn eller neka visas.

    ![Rutan Godkänn begär Anden – Godkänn eller neka i fönstret med information och motivering](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Skriv en orsak i rutan **motivering** .

1. Klicka på **neka**.

    Ett meddelande visas med ditt avslag.

## <a name="workflow-notifications"></a>Arbets flödes meddelanden

Här är lite information om arbets flödes meddelanden:

- Alla medlemmar i listan god kännare meddelas via e-post när en begäran om en roll väntar på att granskas. E-postmeddelanden innehåller en direkt länk till begäran, där god kännaren kan godkänna eller neka.
- Begär Anden löses av den första medlemmen i listan som godkänner eller nekar.
- När en god kännare svarar på begäran, meddelas alla medlemmar i listan god kännare om åtgärden.
- Resurs administratörer meddelas när en godkänd medlem blir aktiv i sin roll.

>[!Note]
>En resurs administratör som tror att en godkänd medlem inte ska vara aktiv kan ta bort den aktiva roll tilldelningen i PIM. Även om resurs administratörer inte meddelas om väntande begär Anden om de inte är medlemmar i listan god kännare, kan de Visa och avbryta väntande begär Anden för alla användare genom att visa väntande begär anden i PIM. 

## <a name="next-steps"></a>Nästa steg

- [Utöka eller förnya Azure Resource roles i PIM](pim-resource-roles-renew-extend.md)
- [E-postmeddelanden i PIM](pim-email-notifications.md)
- [Godkänn eller neka begär Anden för Azure AD-roller i PIM](azure-ad-pim-approval-workflow.md)
