---
title: Begränsningar i Azure Active Directory B2B-samarbete | Microsoft Docs
description: Aktuella begränsningar för Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 562076e9529ffeac4cb0f99c1ffd4d4866d0bd1a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Begränsningar i Azure AD B2B-samarbete
Azure Active Directory (AD Azure) B2B-samarbete är för närvarande de begränsningar som beskrivs i den här artikeln.

## <a name="possible-double-multi-factor-authentication"></a>Möjliga dubbla multifaktorautentisering
Du kan använda multifaktorautentisering i resursorganisationen (bjuda in organisation) med Azure AD B2B. Orsaker till den här metoden beskrivs i [villkorlig åtkomst för användare för B2B-samarbete](conditional-access.md). Om en partner har redan multifaktorautentisering ställa in och tillämpas, kanske användarna att autentisera en gång i organisationen hem och sedan igen i din egen.

## <a name="instant-on"></a>Startar omedelbart
I B2B-samarbete flöden, vi lägga till användare i katalogen och uppdatera dynamiskt under inbjudan inlösning, tilldelning av appen och så vidare. Uppdateringar och skrivningar normalt sker i en kataloginstans och måste replikeras över alla instanser. Replikeringen är klar när alla instanser uppdateras. Ibland när objektet skrivs eller uppdateras i en instans och anrop till hämta det här objektet till en annan instans, kan replikeringsfördröjningar uppstå. I så fall, uppdatera eller försök att. Om du skriver en app med vårt API är en bra och defensiva idé att lösa problemet med återförsök med vissa inte.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Delegera B2bB samarbete inbjudningar](delegate-invitations.md)

