---
title: "Begränsningar i Azure Active Directory B2B-samarbete | Microsoft Docs"
description: "Aktuella begränsningar för Azure Active Directory B2B-samarbete"
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: 2136015eeb3d8ccfc58bf7a94b423144b9aed52e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Begränsningar i Azure AD B2B-samarbete
Azure Active Directory (AD Azure) B2B-samarbete är för närvarande de begränsningar som beskrivs i den här artikeln.

## <a name="possible-double-multi-factor-authentication"></a>Möjliga dubbla multifaktorautentisering
Du kan använda multifaktorautentisering i resursorganisationen (bjuda in organisation) med Azure AD B2B. Orsaker till den här metoden beskrivs i [villkorlig åtkomst för användare för B2B-samarbete](active-directory-b2b-mfa-instructions.md). Om en partner har redan multifaktorautentisering ställa in och tillämpas, kanske användarna att autentisera en gång i organisationen hem och sedan igen i din egen.

## <a name="instant-on"></a>Startar omedelbart
I B2B-samarbete flöden, vi lägga till användare i katalogen och uppdatera dynamiskt under inbjudan inlösning, tilldelning av appen och så vidare. Uppdateringar och skrivningar normalt sker i en kataloginstans och måste replikeras över alla instanser. Replikeringen är klar när alla instanser uppdateras. Ibland när objektet skrivs eller uppdateras i en instans och anrop till hämta det här objektet till en annan instans, kan replikeringsfördröjningar uppstå. I så fall, uppdatera eller försök att. Om du skriver en app med vårt API är en bra och defensiva idé att lösa problemet med återförsök med vissa inte.

## <a name="next-steps"></a>Nästa steg

Läs andra artiklar om Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Egenskaper för användare av B2B-samarbete](active-directory-b2b-user-properties.md)
* [Lägger till en B2B-samarbete användare till en roll](active-directory-b2b-add-guest-to-role.md)
* [Delegera B2bB samarbete inbjudningar](active-directory-b2b-delegate-invitations.md)
* [Dynamiska grupper och B2B-samarbete](active-directory-b2b-dynamic-groups.md)
* [B2B-samarbete kod och PowerShell-exempel](active-directory-b2b-code-samples.md)
* [Konfigurera SaaS-appar för B2B-samarbete](active-directory-b2b-configure-saas-apps.md)
* [Användartoken för B2B-samarbete](active-directory-b2b-user-token.md)
* [B2B-samarbete användaranspråk mappning](active-directory-b2b-claims-mapping.md)
* [Extern delning av Office 365](active-directory-b2b-o365-external-user.md)
