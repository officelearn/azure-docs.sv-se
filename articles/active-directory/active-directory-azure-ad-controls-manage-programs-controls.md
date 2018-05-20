---
title: Hantera program och kontroller för Azure AD åtkomst granskningar | Microsoft Docs
description: Du kan skapa ytterligare program för varje styrning och riskhantering efterlevnad initiativ i din organisation att samla och ordna Azure Active Directory access granskningar som kontroller.
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: billmath
ms.openlocfilehash: a3fb812623b490e27907f63c1f7c6610ae754fb8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="manage-programs-and-their-controls"></a>Hantera program och deras kontroller 

Azure Active Directory (AD Azure) innehåller åtkomst granskning av medlemmar i gruppen och programåtkomst. De här exemplen kontroller Kontrollera tillsyn för vem som har åtkomst till din organisations gruppmedlemskap och program. Organisationer kan använda de här kontrollerna för att effektivt hantera sina styrning och riskhantering krav på efterlevnad.

## <a name="create-and-manage-programs-and-their-controls"></a>Skapa och hantera program och deras kontroller
Du kan förenkla spåra och samla in åtkomst omdömen för olika ändamål genom att ordna dem i program. Granska varje åtkomst kan länkas till ett program. När du förbereder rapporter för en granskare fokusera du sedan på åtkomst granskningar i omfånget för en viss initiative.  Program och åtkomst granska resultaten visas för användare i rollen Global administratör eller säkerhetsadministratör säkerhet läsare.

Om du vill se en lista över program, gå till den [åtkomst går igenom sidan](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) och välj **program**.

**Standardprogrammet** finns alltid. Om du använder en global administratör kan skapa du ytterligare program. Exempelvis kan du ha ett program för varje kompatibilitet eller företagsmål.

Om du inte längre behöver ett program och det inte har några kontroller som är länkade till den, kan du ta bort den.

## <a name="next-steps"></a>Nästa steg

- [Skapa en åtkomstgranskning för medlemmar i en grupp eller åtkomst till ett program](active-directory-azure-ad-controls-create-access-review.md)
- [Hämta resultaten för en åtkomst-granskning](active-directory-azure-ad-controls-retrieve-access-review.md)
