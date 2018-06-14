---
title: Kräver Användartilldelning - Azure AD | Microsoft Docs
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
ms.openlocfilehash: b02460435edca336325e472ea910b73e7895c948
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
ms.locfileid: "26598836"
---
# <a name="azure-ad-and-applications-require-user-assignment"></a>Azure AD och program: kräver Användartilldelning
## <a name="requiring-user-assignment"></a>Kräver Användartilldelning
1. Logga in på Azure-portalen med ett administratörskonto.
2. Klicka på den **alla objekt** objekt på huvudmenyn.
3. Välj det bibliotek som du använder för programmet.
4. Klicka på den **program** fliken.
5. Markera programmet i listan över program som är associerade med den här katalogen.
6. Klicka på den **konfigurera** fliken.
7. Ändra den **användaren tilldelning krävs för att Access-appen** växla till Ja.
8. Klicka på den **spara** längst ned på skärmen.

Nu behöver du tilldela användare och/eller grupper till programmet. Se [tilldela användare till ett program](active-directory-applications-guiding-developers-assigning-users.md) och [tilldela grupper till ett program](active-directory-applications-guiding-developers-assigning-groups.md).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
