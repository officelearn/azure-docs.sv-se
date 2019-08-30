---
title: Vägledning om B2B-samarbets licensiering – Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B-samarbete kräver inte betalda Azure AD-licenser, men du kan också få betalt funktioner för B2B-gäst användare
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
ms.openlocfilehash: 5968536d0bd0fbe2496fd9bf5fca86c7a1c4053d
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70162272"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Vägledning om Azure Active Directory B2B-samarbete och licenser

Med Azure Active Directory (Azure AD) samarbete mellan företag (B2B) kan du bjuda in externa användare (eller "gäst användare") så att de använder dina betalda Azure AD-tjänster. Vissa funktioner är kostnads fria, men för alla betal funktioner i Azure AD kan du bjuda in upp till fem gäst användare för varje Azure AD Edition-licens som du äger för en anställd eller en användare som inte är gäst i din klient organisation.

> [!NOTE]
> Läs [Azure Active Directory priser](https://azure.microsoft.com/pricing/details/active-directory/) för mer information om priser och B2B-samarbets funktioner i Azure AD.

B2B-gäst användar licensiering beräknas och rapporteras automatiskt baserat på 1:5-förhållandet. För närvarande går det inte att tilldela B2B-gäst användar licenser direkt till gäst användare.

Gäst användare kan dessutom använda kostnads fria Azure AD-funktioner utan ytterligare licensierings krav. Gäst användare har till gång till kostnads fria Azure AD-funktioner även om du inte har några betalda Azure AD-licenser. 

## <a name="examples-calculating-guest-user-licenses"></a>Exempel: Beräkna gäst användar licenser
När du har fastställt hur många gäst användare som behöver få åtkomst till dina betal Azure AD-tjänster måste du se till att du har tillräckligt med Azure AD-betalda licenser för gäst användare i det krav som krävs för 1:5. Här följer några exempel:

- Du vill bjuda in 100 gäst användare till dina Azure AD-appar eller-tjänster och tillhandahålla åtkomst hantering och etablering. För 50 av dessa gäst användare vill du också kräva MFA och villkorlig åtkomst så att du behöver 10 Azure AD Premium P1-licenser för de funktionerna. Om du planerar att använda funktioner för identitets skydd med dina gäst användare behöver du Azure AD Premium P2-licenser i samma 1:5-förhållande för att avse gäst användarna.
- Du vill bjuda in 60 gäst användare som alla behöver MFA, så du måste ha minst 12 Azure AD Premium P1-licenser. Du har 10 anställda med Azure AD Premium P1-licenser som ger upp till 50 gäst användare under licens kvoten på 1:5. Du måste köpa två ytterligare Premium P1-licenser för att kunna betala ytterligare 10 gäst användare.

## <a name="next-steps"></a>Nästa steg

Se följande resurser i Azure AD B2B-samarbete:

* [Azure Active Directory priser](https://azure.microsoft.com/pricing/details/active-directory/)
* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Vanliga frågor och svar om Azure Active Directory B2B-samarbete](faq.md)
