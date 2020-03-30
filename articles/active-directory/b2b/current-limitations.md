---
title: Begränsningar för B2B-samarbete - Azure Active Directory | Microsoft-dokument
description: Aktuella begränsningar för Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffee01488ecf658ce02a20a41252aca19288667c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263366"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Begränsningar för Azure AD B2B-samarbete
Azure Active Directory (Azure AD) B2B-samarbete är för närvarande föremål för de begränsningar som beskrivs i den här artikeln.

## <a name="possible-double-multi-factor-authentication"></a>Möjlig dubbel multifaktorautentisering
Med Azure AD B2B kan du framtvinga multifaktorautentisering hos resursorganisationen (den inbjudande organisationen). Orsakerna till den här metoden beskrivs i [Villkorlig åtkomst för användare av B2B-samarbete](conditional-access.md). Om en partner redan har konfigurerat och verkställt multifaktorautentisering kan användarna behöva utföra autentiseringen en gång i sin hemorganisation och sedan igen i din.

## <a name="instant-on"></a>Direkt-på
I B2B-samarbetsflödena lägger vi till användare i katalogen och uppdaterar dem dynamiskt under inlösning av inbjudan, apptilldelning och så vidare. Uppdateringarna och skrivningarna sker normalt i en kataloginstans och måste replikeras i alla instanser. Replikeringen slutförs när alla instanser har uppdaterats. Ibland när objektet skrivs eller uppdateras i en instans och anropet för att hämta objektet är till en annan instans, kan replikeringsdämman uppstå. Om det händer uppdaterar eller försöker du igen för att hjälpa till. Om du skriver en app med vårt API, sedan försök med några back-off är en bra, defensiv praxis för att lindra detta problem.

## <a name="azure-ad-directories"></a>Azure AD-kataloger
Azure AD B2B omfattas av Azure AD-tjänstkataloggränser. Mer information om hur många kataloger en användare kan skapa och antalet kataloger som en användare eller gästanvändare kan tillhöra finns i [Azure AD-tjänstbegränsningar och begränsningar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="national-clouds"></a>Nationella moln
[Nationella moln](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) är fysiskt isolerade instanser av Azure. B2B-samarbete stöds inte över nationella molngränser. Om din Azure-klient finns i det offentliga globala molnet kan du till exempel inte bjuda in en användare vars konto finns i ett nationellt moln. Om du vill samarbeta med användaren ber du dem om en annan e-postadress eller skapar ett medlemsanvändarkonto för dem i katalogen.

## <a name="azure-us-government-clouds"></a>Azure amerikanska regeringen moln
Inom Azure US Government-molnet stöds B2B-samarbete för närvarande endast mellan klienter som både finns i Azure US Government-molnet och som båda stöder B2B-samarbete. Om du bjuder in en användare i en klient som inte ingår i Azure US Government-molnet eller som ännu inte stöder B2B-samarbete misslyckas inbjudan eller så kan användaren inte lösa in inbjudan. Mer information om andra begränsningar finns i [Azure Active Directory Premium P1- och P2-varianter](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2).

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Hur vet jag om B2B-samarbete är tillgängligt i min Azure US Government-klientorganisation?
Så här tar du reda på om din Azure US Government-molnklient stöder B2B-samarbete:

1. I en webbläsare går du till följande webbadress och ersätter ditt klientnamn med * &lt;klientnamn:&gt;*

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Hitta `"tenant_region_scope"` i JSON-svaret:

   - Om `"tenant_region_scope":"USGOV”` visas stöds B2B.
   - Om `"tenant_region_scope":"USG"` visas stöds inte B2B.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Inbjudan till ombud B2B-samarbete](delegate-invitations.md)

