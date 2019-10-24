---
title: Godkänn eller neka begär Anden för Azure AD-roller i PIM-Azure Active Directory | Microsoft Docs
description: Lär dig hur du godkänner eller nekar begär Anden för Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3245f7343a48d3e54795c14dcb23b836c8d9d988
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756434"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Godkänn eller neka begär Anden för Azure AD-roller i Privileged Identity Management

Med Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kan du konfigurera roller för att kräva godkännande för aktivering och välja en eller flera användare eller grupper som delegerade god kännare. Delegerade god kännare har 24 timmar på sig att godkänna begär Anden. Om en begäran inte godkänns inom 24 timmar måste den behöriga användaren skicka en ny begäran igen. Tids perioden för godkännande av 24 timmar kan inte konfigureras.

Följ stegen i den här artikeln för att godkänna eller neka begär Anden för Azure AD-roller.

## <a name="view-pending-requests"></a>Visa väntande begär Anden

Som en delegerad god kännare får du ett e-postmeddelande när en begäran om Azure AD-roll väntar på ditt godkännande. Du kan visa dessa väntande begär anden i Privileged Identity Management.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller**.

1. Klicka på **Godkänn begär Anden**.

    ![Azure AD-roller – Godkänn begär Anden](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    Du ser en lista över begär Anden som väntar på ditt godkännande.

## <a name="approve-requests"></a>Godkänn förfrågningar

1. Välj de begär Anden som du vill godkänna och klicka sedan på **Godkänn** för att öppna fönstret Godkänn valda begär Anden.

    ![Listan Godkänn begär Anden med alternativet Godkänn markerat](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Skriv en orsak i rutan **Godkänn orsak** .

    ![Godkänn fönstret valda begär Anden med en Godkänn orsak](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Klicka på **Godkänn**.

    Status symbolen kommer att uppdateras med ditt godkännande.

    ![Rutan Godkänn valda begär Anden efter att en Godkänn-knapp har klickats](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Neka förfrågningar

1. Välj de begär Anden som du vill neka och klicka sedan på **neka** för att öppna fönstret neka valda begär Anden.

    ![Listan Godkänn begär Anden med alternativet neka markerat](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Skriv en orsak i rutan **orsak till neka** .

    ![Neka fönstret valda begär Anden med en orsak till neka](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Klicka på **neka**.

    Status symbolen kommer att uppdateras med ditt avslag.

## <a name="next-steps"></a>Nästa steg

- [E-postmeddelanden i Privileged Identity Management](pim-email-notifications.md)
- [Godkänn eller neka begär Anden för Azures resurs roller i Privileged Identity Management](pim-resource-roles-approval-workflow.md)
