---
title: Kräv Användartilldelning – Azure AD | Microsoft Docs
description: Så här kräver Användartilldelning för Azure-program.
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: ''
ms.assetid: 30b78cba-1e0f-472f-8314-f2250a9b91c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: kgremban
robots: noindex
ms.openlocfilehash: 4519681d9b91383d27c00a992f85b0cb5d74f235
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859470"
---
# <a name="azure-ad-and-applications-require-user-assignment"></a>Azure AD och program: Kräv Användartilldelning
## <a name="requiring-user-assignment"></a>Kräv Användartilldelning
1. Logga in på Azure-portalen med ett administratörskonto.
2. Klicka på den **alla tjänster** objekt på huvudmenyn.
3. Välj det bibliotek som du använder för programmet.
4. Klicka på den **företagsprogram** fliken.
5. Välj programmet i listan över program som är associerade med den här katalogen.
6. Klicka på den **egenskaper** fliken.
7. Ändra den **Användartilldelning krävs?** växla till Ja.
8. Klicka på den **spara** längst upp på skärmen.

Du har nu tilldela användare och/eller grupper till programmet. Se [tilldela användare till ett program](active-directory-applications-guiding-developers-assigning-users.md) och [tilldela grupper till ett program](active-directory-applications-guiding-developers-assigning-groups.md).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
