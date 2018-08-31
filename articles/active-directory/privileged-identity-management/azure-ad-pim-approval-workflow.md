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
ms.component: pim
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7bf1e437e97fdb4d929af23bd7b2a9abb49268df
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189166"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>Godkänn eller neka begäranden för Azure AD-katalogroller i PIM

Med Privileged Identity Management kan du konfigurera roller för att kräva godkännande för aktivering och välja en eller flera användare eller grupper som delegerad godkännare.

## <a name="view-pending-approvals-requests"></a>Visa väntande godkännanden (begäranden)

Som en delegerad godkännaren får du e-postmeddelanden när en begäran väntar på ditt godkännande. Välj fliken ”väntande godkännandebegäranden” i det vänstra navigeringsfältet på instrumentpanelen (i den nya navigeringen) om du vill visa dessa begäranden i PIM-portalen.

![](media/azure-ad-pim-approval-workflow/image023.png)

Därifrån kan visas en lista över begäranden väntar på godkännande:

![](media/azure-ad-pim-approval-workflow/image024.png)

## <a name="approve-or-deny-requests-for-role-elevation-single-andor-bulk"></a>Godkänn eller avvisa begäranden om rollen utökade privilegier (enkel och/eller)

Välj de förfrågningar som du vill godkänna eller neka och klicka på knappen i Åtgärdsfältet som motsvarar ditt beslut:

![](media/azure-ad-pim-approval-workflow/image025.png)

## <a name="provide-justification-for-my-approvaldenial"></a>Ange en motivering för min godkännande/avvisades

Då öppnas ett nytt blad för att godkänna eller neka flera begäranden samtidigt. Ange en motivering till ditt beslut och klicka på Godkänn (eller neka) på nederkant eller bladet:

![](media/azure-ad-pim-approval-workflow/image029.png)

När begäran-processen är klar visas symbolen för statusen återspeglas beslut som du gjort (i det här exemplet beslutet är godkänna):

![](media/azure-ad-pim-approval-workflow/image031.png)

## <a name="next-steps"></a>Nästa steg

- [Godkänn eller neka begäranden för Azure-resursroller i PIM](pim-resource-roles-approval-workflow.md)
- [E-postmeddelanden i PIM](pim-email-notifications.md)
