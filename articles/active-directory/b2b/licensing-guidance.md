---
title: B2B-licensieringsvägledning för samarbete - Azure Active Directory | Microsoft-dokument
description: Azure Active Directory B2B-samarbete kräver inte betalda Azure AD-licenser, men du kan också få betalt funktioner för B2B-gästanvändare
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28d02160163da4081ad8adbe233b27fee970a0df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74868855"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B-licensieringsvägledning för samarbete

Med Azure Active Directory (Azure AD) B2B-samarbete (Business-to-Business) kan du bjuda in externa användare (eller "gästanvändare") att använda dina betalda Azure AD-tjänster. Vissa funktioner är kostnadsfria, men för alla betalda Azure AD-funktioner kan du bjuda in upp till fem gästanvändare för varje Azure AD-utgåva som du äger för en anställd eller en icke-gästanvändare i din klientorganisation.

> [!NOTE]
> Mer information om Azure AD-priser och B2B-samarbetsfunktioner finns i [Azure Active Directory-priser.](https://azure.microsoft.com/pricing/details/active-directory/)

B2B gästanvändarlicensiering beräknas automatiskt och rapporteras baserat på förhållandet 1:5. 

Dessutom kan gästanvändare använda kostnadsfria Azure AD-funktioner utan ytterligare licenskrav. Gästanvändare har tillgång till kostnadsfria Azure AD-funktioner även om du inte har några betalda Azure AD-licenser. 

## <a name="examples-calculating-guest-user-licenses"></a>Exempel: Beräkna gästanvändarlicenser
När du har bestämt hur många gästanvändare som behöver komma åt dina betalda Azure AD-tjänster kontrollerar du att du har tillräckligt med Azure AD-betalda licenser för att täcka gästanvändare i det erforderliga förhållandet 1:5. Här följer några exempel:

- Du vill bjuda in 100 gästanvändare till dina Azure AD-appar eller -tjänster och tillhandahålla åtkomsthantering och etablering. För 50 av dessa gästanvändare vill du också kräva MFA och villkorlig åtkomst, så för dessa funktioner behöver du 10 Azure AD Premium P1-licenser. Om du planerar att använda identitetsskyddsfunktioner med gästanvändarna behöver du Azure AD Premium P2-licenser i samma 1:5-förhållande för att täcka gästanvändarna.
- Du vill bjuda in 60 gästanvändare som alla behöver MFA, så du måste ha minst 12 Azure AD Premium P1-licenser. Du har 10 anställda med Azure AD Premium P1-licenser, vilket skulle tillåta upp till 50 gästanvändare under 1:5-licensieringsförhållandet. Du måste köpa ytterligare två Premium P1-licenser för att täcka ytterligare 10 gästanvändare.

## <a name="next-steps"></a>Nästa steg

Se följande resurser för Azure AD B2B-samarbete:

* [Azure Active Directory-priser](https://azure.microsoft.com/pricing/details/active-directory/)
* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Vanliga frågor och svar om Azure Active Directory B2B-samarbete](faq.md)
