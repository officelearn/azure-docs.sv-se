---
title: Granska åtkomsten till Azure-resursroller i PIM - Azure AD | Microsoft-dokument
description: Lär dig hur du granskar åtkomsten för Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ddb4d0ff1339f1427c5041528cdbe464a345b37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847009"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Granska åtkomsten till Azure-resursroller i Privilegierad identitetshantering

Pim-åtkomstgranskningar (Privileged Identity Management) kan skydda åtkomst till privilegierade roller i Azure Active Directory (Azure AD). I den här artikeln beskrivs stegen för att slutföra en granskning av dina privilegierade rolltilldelningar i en Azure AD-åtkomstgranskning.

Om du har tilldelats en administrativ roll kan du behöva slutföra en åtkomstgranskning av administratören för att bekräfta behovet av en roll. Bekräftelsebegäran kan komma ett e-postmeddelande som innehåller en länk, eller så kan du bekräfta i [Azure-portalen](https://portal.azure.com).

Om du är en privilegierad rolladministratör som är intresserad av åtkomstgranskningar kan du få mer information om [hur du startar en åtkomstgranskning](pim-resource-roles-start-access-review.md).

## <a name="approve-or-deny-access"></a>Godkänna eller neka åtkomst

Du kan godkänna eller neka åtkomst baserat på om du fortfarande använder den här rollen eller inte. Välj **Godkänn** om du vill stanna kvar i rollen eller **Neka** om du inte behöver åtkomsten längre. Din status ändras först när granskaren har tillämpar resultaten.

Så här hittar du och slutför åtkomstgranskningen:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj **Azure Active Directory** och öppna **Privilegierad identitetshantering**.
1. Välj **Granska åtkomst**.

   ![Skärmbild av programmet Privilegierad identitetshantering, med åtkomstbladet Granska markerat](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Välj den recension som du vill slutföra.
1. Välj **Godkänn** eller **Neka**. Ange **en**affärsmotivering för ditt beslut om det behövs i rutan Ange en affärsmotivering för ditt beslut.

   ![Skärmbild av sidan Granska information](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Nästa steg

- [Utföra en åtkomstgranskning av mina Azure AD-roller i Privilegierad identitetshantering](pim-how-to-perform-security-review.md)
