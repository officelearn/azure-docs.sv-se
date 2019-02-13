---
title: Godkänn eller neka begäranden för Azure AD-katalogroller i PIM | Microsoft Docs
description: Lär dig mer om att godkänna eller neka begäranden för Azure AD-katalogroller i Azure AD Privileged Identity Management (PIM).
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
ms.subservice: pim
ms.date: 08/29/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40bae06fcb174e7beb2c9bb708fea4189014f316
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163905"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>Godkänn eller neka begäranden för Azure AD-katalogroller i PIM

Med Azure AD Privileged Identity Management (PIM), kan du konfigurera roller för att kräva godkännande för aktivering och välja en eller flera användare eller grupper som delegerad godkännare. Följ stegen i den här artikeln att godkänna eller neka begäranden för Azure AD-katalogroller.

## <a name="view-pending-requests"></a>Visa väntande begäranden

Som en delegerad godkännaren får du ett e-postmeddelande när en begäran om Azure AD directory roll är väntar på ditt godkännande. Du kan visa dessa väntande begäranden i PIM.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-katalogroller**.

1. Klicka på **godkänna förfrågningar**.

    ![PIM-Azure AD-katalogroller - roller](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    Du ser en lista över begäranden väntar på ditt godkännande.

## <a name="approve-requests"></a>Godkänn förfrågningar

1. Välj de förfrågningar som du vill godkänna och klicka sedan på **Godkänn** att öppna Godkänn valda förfrågningar fönstret.

    ![PIM godkänna begäranden lista](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. I den **Godkänn skäl** skriver en orsak.

    ![PIM Godkänn valda förfrågningar](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Klicka på **godkänna**.

    Symbolen för statusen kommer att uppdateras med ditt godkännande.

    ![PIM Godkänn valda förfrågningar](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Neka förfrågningar

1. Välj de förfrågningar som du vill neka och klicka sedan på **neka** att öppna neka valda förfrågningar fönstret.

    ![PIM godkänna begäranden lista](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. I den **skäl för nekande** skriver en orsak.

    ![PIM neka valda förfrågningar](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Klicka på **neka**.

    Symbolen för statusen kommer att uppdateras med din datorn.

## <a name="next-steps"></a>Nästa steg

- [E-postmeddelanden i PIM](pim-email-notifications.md)
- [Godkänn eller neka begäranden för Azure-resursroller i PIM](pim-resource-roles-approval-workflow.md)
