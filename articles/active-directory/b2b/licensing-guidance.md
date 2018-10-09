---
title: Azure Active Directory B2B-samarbete och licenser vägledning | Microsoft Docs
description: Azure Active Directory B2B collaboration inte kräver betalda Azure AD-licenser, men du kan också få betalt för funktioner för B2B-gästanvändare
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.openlocfilehash: d80794511f334cd6dc5af418e24fc774b7d8728f
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867518"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Vägledning om Azure Active Directory B2B-samarbete och licenser

Med Azure Active Directory (Azure AD) business-to-business (B2B) samarbete, du kan bjuda in extern användare (eller ”gästanvändare”) för att använda din betalda Azure AD-tjänster. För var och en betald Azure AD-licens som du tilldelar en användare kan du bjuda in upp till fem gästanvändare under den externa användaren tilldelningen.

En gästanvändare är någon som inte är medlem i din organisation eller någon av din organisations dotterbolag. Gästanvändare definieras av deras relation till din organisation, inte av de autentiseringsuppgifter de använder för att logga in. I själva verket kan en gästanvändare logga in med en extern identitet eller med autentiseringsuppgifter som ägs av organisationen.

Följande är *inte* gästanvändare:
- Dina anställda, leverantörer som agerar på plats eller på plats agenter
- Anställda, leverantörer som agerar på plats eller på plats agenter för dina dotterbolag

Användarlicensiering för B2B-gäst automatiskt beräknas och rapporteras baserat på 1:5-förhållande. För närvarande, det går inte att tilldela B2B gäst användarlicenser direkt till gästanvändare.

Det finns vissa situationer där en gästanvändare inte är rapporteras med 1:5-kvoten för externa användare. Om en gästanvändare redan har en betald Azure AD-licens i användarens organisation, användaren inte använda någon av dina B2B-gäst användarlicenser. Dessutom kan kostnadsfria Gäst som användarna kan använda Azure AD-funktioner med inga ytterligare licenskrav. Gästanvändare har åtkomst till kostnadsfria Azure AD-funktioner, även om du inte har någon betald Azure AD-licenser. 

## <a name="examples-calculating-guest-user-licenses"></a>Exempel: Beräkna gäst-användarlicenser
När du vet hur många gästanvändare behöver åtkomst till din betalda Azure AD-tjänster, kontrollera att du har tillräckligt med Azure AD betald licenser för att täcka gästanvändare i förhållandet krävs 1:5. Här följer några exempel:

- Du vill bjuda in 100 gästanvändare till din Azure AD-appar eller tjänster och du vill tilldela åtkomsthantering och driftsättning till alla gästanvändare. Du bör också att kräva MFA och villkorlig åtkomst för 50 i dessa gästanvändare. Du behöver för att täcka den här kombinationen 10 Azure AD Basic-licenser och 10 Azure AD Premium P1-licenser. Om du tänker använda Identity Protection-funktioner med din gästanvändare måste Azure AD Premium P2-licenser i samma 1:5 förhållandet att täcka gästanvändare.
- Du vill bjuda in 60 gästanvändare som kräver MFA, så du måste ha minst 12 Azure AD Premium P1-licenser. Du har 10 anställda med Azure AD Premium P1-licenser som skulle tillåta upp till 50 gästanvändare under 1:5-licensiering förhållande. Du måste köpa två ytterligare Premium P1-licenser för att täcka 10 ytterligare gästanvändare.

## <a name="using-the-b2b-collaboration-api-to-invite-users-from-your-affiliates"></a>Med B2B-samarbete API att bjuda in användare från dina dotterbolag

Per definition en B2B-gästanvändare är en extern användare som du bjuder in för att använda din betalda Azure AD-appar och tjänster. En medarbetare, entreprenör på plats eller på plats-agenten i ditt företag eller något av dina dotterbolag inte är berättigad till B2B-samarbete, även om B2B-funktioner används. Här följer några exempel: 
- Du vill använda externa autentiseringsuppgifter (till exempel en sociala identitet) för att bjuda in en användare som är anställd hos din organisation. Det här scenariot följer inte licenskraven och är inte tillåtet. Externa autentiseringsuppgifter Se inte en medarbetare en extern användare.  
- Du vill använda B2B-API: er för att bjuda in användare från någon av din organisations dotterbolag. Även om det här scenariot använder B2B-API: er för att bjuda in användaren kan den inte betraktas som B2B-samarbete. Den följer inte licenskraven eftersom en användare från din koncernbolag inte är en extern användare. 

I dessa båda situationer, bättre lösning är att använda B2B-API för att bjuda in användare som medlemmar (invitedUserType = medlem) och tilldela dem varje Azure AD-licens. 

## <a name="next-steps"></a>Nästa steg

Se följande resurser på Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Vanliga frågor (och svar FAQ) om Azure Active Directory B2B-samarbete](faq.md)
