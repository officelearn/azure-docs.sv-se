---
title: Tilldela till Azure AD-appar | Microsoft Docs
description: "Hur du implementerar grupptilldelning för Azure-program."
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
editor: 
ms.assetid: 29b5ba89-a1c7-4f1f-a294-248a40106617
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
robots: noindex
ms.openlocfilehash: 471099c58a7fc1dfd520e693873a76f64c0fbf0a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="assign-azure-active-directory-groups-to-an-application"></a>Tilldela ett program med Azure Active Directory-grupper
Innan du kan tilldela användare och grupper till ett program, måste du begära Användartilldelning. Information om hur du kan kräva Användartilldelning finns i [kräver Användartilldelning](active-directory-applications-guiding-developers-requiring-user-assignment.md) artikel.

Den här artikeln förutsätter att du redan har skapat grupper i active directory som du använder för det här programmet.

## <a name="assigning-groups-to-an-application"></a>Tilldela grupper till ett program
1. Logga in på Azure-portalen med ett administratörskonto.
2. Klicka på den **alla objekt** objekt på huvudmenyn.
3. Välj det bibliotek som du använder för programmet.
4. Klicka på den **program** fliken.
5. Markera programmet i listan över program som är associerade med den här katalogen.
6. Klicka på den **användare och grupper** fliken.
7. Filtrera listan över grupper i active directory med hjälp av den **grupper** listrutan.
8. Välj en grupp.
9. Klicka på **TILLDELA**.
10. Klicka på **Ja** när du tillfrågas.

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
