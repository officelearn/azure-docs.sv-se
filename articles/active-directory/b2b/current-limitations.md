---
title: Begränsningar av B2B - samarbete i Azure Active Directory | Microsoft Docs
description: Aktuella begränsningar för Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7bf4d1d0f510ccad614452db74c291f6c61dcf54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60355802"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Begränsningar i Azure AD B2B-samarbete
Azure Active Directory (Azure AD) B2B-samarbete är för närvarande de begränsningar som beskrivs i den här artikeln.

## <a name="possible-double-multi-factor-authentication"></a>Möjliga dubbla multifaktorautentisering
Med Azure AD B2B upprätthålla du Multi-Factor authentication på resursorganisationen (organisationen som bjuder in). Orsaker till den här metoden beskrivs i [villkorlig åtkomst för användare i B2B-samarbetet](conditional-access.md). Om en partner har redan multifaktorautentisering ställa in och tillämpas, användarna kan behöva utföra autentisering en gång i deras organisation, hem och sedan igen i ditt.

## <a name="instant-on"></a>Startar omedelbart
I B2B-samarbete flöden, vi lägga till användare i katalogen och uppdatera dem dynamiskt under inlösning av inbjudan, apptilldelning och så vidare. Uppdateringar och skrivningar normalt ske i en katalog-instansen och måste replikeras över alla instanser. Replikeringen är klar när alla instanser har uppdaterats. Ibland när objektet är avsedd eller uppdateras i en instans och anrop för att hämta det här objektet till en annan instans, kan replikeringsfördröjningar inträffa. Om detta händer, uppdatera eller försök att. Om du skriver en app med vårt API är en bra och försvarsstatistik idé att lösa problemet med återförsök med vissa backoff.

## <a name="azure-ad-directories"></a>Azure AD-kataloger
Azure AD B2B är föremål för Azure AD tjänstbegränsningar directory. Mer information om antal kataloger som en användare kan skapa och antal kataloger till som en användare eller gäst kan höra, finns i [Azure AD-tjänsten begränsningar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="next-steps"></a>Nästa steg

Se följande artiklar på Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Delegera inbjudningar för B2B-samarbete](delegate-invitations.md)

