---
title: Azure Active Directory B2B-samarbete och licenser vägledning | Microsoft Docs
description: Azure Active Directory B2B collaboration inte kräver betalda Azure AD-licenser, men du kan också få betalt för funktioner för B2B-gästanvändare
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0032eb1e6b92abb130521945f64fda3133d9b7fc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198889"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Vägledning om Azure Active Directory B2B-samarbete och licenser

Med Azure Active Directory (Azure AD) business-to-business (B2B) samarbete, du kan bjuda in extern användare (eller ”gästanvändare”) för att använda din betalda Azure AD-tjänster. För var och en betald Azure AD-licens som du tilldelar en användare kan du bjuda in upp till fem gästanvändare under den externa användaren tilldelningen.

Användarlicensiering för B2B-gäst automatiskt beräknas och rapporteras baserat på 1:5-förhållande. För närvarande, det går inte att tilldela B2B gäst användarlicenser direkt till gästanvändare.

Dessutom kan kostnadsfria Gäst som användarna kan använda Azure AD-funktioner med inga ytterligare licenskrav. Gästanvändare har åtkomst till kostnadsfria Azure AD-funktioner, även om du inte har någon betald Azure AD-licenser. 

## <a name="examples-calculating-guest-user-licenses"></a>Exempel: Beräkning av gäst-användarlicenser
När du vet hur många gästanvändare behöver åtkomst till din betalda Azure AD-tjänster, kontrollera att du har tillräckligt med Azure AD betald licenser för att täcka gästanvändare i förhållandet krävs 1:5. Här följer några exempel:

- Du vill bjuda in 100 gästanvändare till din Azure AD-appar eller tjänster och du vill tilldela åtkomsthantering och driftsättning till alla gästanvändare. Du bör också att kräva MFA och villkorlig åtkomst för 50 i dessa gästanvändare. Du behöver för att täcka den här kombinationen 10 Azure AD Basic-licenser och 10 Azure AD Premium P1-licenser. Om du tänker använda Identity Protection-funktioner med din gästanvändare måste Azure AD Premium P2-licenser i samma 1:5 förhållandet att täcka gästanvändare.
- Du vill bjuda in 60 gästanvändare som kräver MFA, så du måste ha minst 12 Azure AD Premium P1-licenser. Du har 10 anställda med Azure AD Premium P1-licenser som skulle tillåta upp till 50 gästanvändare under 1:5-licensiering förhållande. Du måste köpa två ytterligare Premium P1-licenser för att täcka 10 ytterligare gästanvändare.

## <a name="next-steps"></a>Nästa steg

Se följande resurser på Azure AD B2B-samarbete:

* [Priser för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Vanliga frågor och svar om Azure Active Directory B2B-samarbete](faq.md)
