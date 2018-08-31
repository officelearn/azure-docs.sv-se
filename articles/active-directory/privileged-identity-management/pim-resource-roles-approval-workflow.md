---
title: Godkänn eller neka begäranden för Azure-resursroller i PIM | Microsoft Docs
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
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e5cda31af5ac95e7ebe2b858b1d7355ea3f2a6bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189812"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Godkänn eller neka begäranden för Azure-resursroller i PIM

För att godkänna eller avvisa en begäran, måste du vara medlem i listan med godkännare. 

1. I PIM, Välj **godkänna förfrågningar** från fliken på den vänstra menyn och leta upp begäran.

   ![Fönstret ”godkänna begäranden”](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Välj begäran, ange en motivering för beslutet och välj **Godkänn** eller **neka**. Begäran är sedan löst.

   ![Valda begäran med detaljerad information](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Arbetsflödesmeddelanden

Här följer några fakta om arbetsflödesmeddelanden:

- Alla medlemmar i listan över godkännare meddelas via e-post när en begäran för en roll väntar på sina granskningar. E-postmeddelanden innehåller en direktlänk till begäran, där godkännaren kan godkänna eller neka.
- Begäranden kan matchas med den första medlemmen i listan som godkänner eller nekar. 
- När en godkännare svarar på begäran, meddelas alla medlemmar i listan över godkännare för åtgärden. 
- Resurs-administratörer får ett meddelande när en godkända medlemmen aktiveras i deras roll. 

>[!Note]
>En anser att en godkända medlemmen inte får vara aktiva resurs-administratör kan ta bort aktiv rolltilldelning i PIM. Även om resursen administratörer meddelas inte om väntande begäranden om de inte är medlemmar i listan över godkännare, kan de visa och avbryta väntande begäranden för alla användare genom att visa väntande begäranden i PIM. 

## <a name="next-steps"></a>Nästa steg

- [Godkänn eller neka begäranden för Azure AD-katalogroller i PIM](azure-ad-pim-approval-workflow.md)
- [E-postmeddelanden i PIM](pim-email-notifications.md)
