---
title: Förstå Azure AD-programtillåtelser inträffar | Microsoft Docs
description: Lär dig mer om Azure AD godkänna upplevelser för att se hur du kan använda den när du hanterar och utveckla program i Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: celested
ms.reviewer: zachowd
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7de892143e1c69953cc60429ea0d24df194f0df
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795193"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Förstå medgivande i Azure AD-program

Lär dig mer om Azure Active Directory (Azure AD)-programmet samtycker användarupplevelse. Så du kan smart hantera program för din organisation och/eller utveckla program med en mer sömlös upplevelse för medgivande.

## <a name="consent-and-permissions"></a>Medgivande och behörigheter

Medgivande är en process för en användare beviljas behörighet att ett program för att komma åt skyddade resurser å deras vägnar. En administratör eller användare kan be dig om medgivande till att tillåta åtkomst till deras organisation/person-data.

Faktiska användarupplevelsen för att godkänna varierar beroende på principer som angetts i användarens klientorganisation, användarens omfattningen myndigheten (eller roll) och vilken typ av [behörigheter](https://docs.microsoft.com/azure/active-directory/develop/active-directory-permissions) som begärs av klientprogrammet. Det innebär att utvecklare och administratörer av klientorganisationer har viss kontroll över samtycke upplevelsen. Administratörer har flexibilitet och inaktivera principer på en klient eller app för att styra samtycke upplevelsen i deras klienter. Programutvecklare kan bestämmer vilka typer av behörigheter som krävs och om de vill hjälper användaren att användaren godkänner flow eller administratörsflöde för medgivande.

- **Användaren godkännandeflöde** är när en programutvecklare dirigerar användare till auktoriseringsslutpunkten med avsikt att registrera ditt medgivande för endast den aktuella användaren.
- **Admin-godkännandeflöde** är när en programutvecklare uppmanar användare att administratören godkänna slutpunkt med avsikt att registrera ditt medgivande för hela klientorganisationen. För att säkerställa medgivande administratörsflöde fungerar korrekt, måste programutvecklare lista alla behörigheter i den `RequiredResourceAccess` -egenskapen i programmanifestet. Mer information finns i [programmanifestet](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest).

## <a name="building-blocks-of-the-consent-prompt"></a>Byggblock fråga om medgivande

Medgivandetext har utformats för att se till att användare har tillräcklig information för att avgöra om de litar på klientprogram att komma åt skyddade resurser å deras vägnar. Förstå de olika byggstenarna hjälper användare beviljar medgivande gör mer informerade beslut och det gör att utvecklare kan bygga bättre användarupplevelser.

Följande diagram och tabell innehåller information om de olika byggstenarna i fråga om medgivande.

![Byggblock fråga om medgivande](./media/application-consent-experience/consent_prompt.png)

| # | Komponent | Syfte |
| ----- | ----- | ----- |
| 1 | Användaridentifierare | Den här identifieraren representerar den användare som begär åtkomst till skyddade resurser på uppdrag av klientprogrammet. |
| 2 | Rubrik | Rubrik-ändringar baserat på om användarna går igenom godkännandeflöde användare eller administratör. I användarens medgivande flow kommer rubriken att ”behörighet begärt” medan medgivande administratörsflöde rubriken har ytterligare en rad ”acceptera för din organisation”. |
| 3 | Applogotyp | Den här bilden hjälpa användare som har en visuell ledtråd om den här appen är appen de avsedda att få åtkomst till. Den här bilden kommer från programutvecklare och ägarskap för den här bilden är inte verifieras. |
| 4 | Appnamn | Det här värdet bör informera användare som begär åtkomst till sina data. Observera att det här namnet kommer från utvecklare och ägarskap för den här appens namn är inte verifieras. |
| 5 | Utgivardomän | Det här värdet bör ge användare med en domän som de kanske kan utvärdera för tillförlitlighet. Den här domänen tillhandahålls av utvecklare och ägarskap för den här publisher-domänen har verifierats. |
| 6 | Behörigheter | Den här listan innehåller de behörigheter som begärts av klientprogrammet. Användare bör alltid utvärdera vilka behörigheter som har begärts att förstå vilka data som klientprogrammet ska ha behörighet att komma åt å deras vägnar om de godkänner. Det är bäst att begära åtkomst till behörigheterna som med minsta privilegier som programutvecklare av. |
| 7 | Beskrivning av behörighet | Det här värdet tillhandahålls av tjänsten exponerar behörigheterna. Om du vill se beskrivningarna behörighet, måste du växla ikonen bredvid behörigheten. |
| 8 | App-villkor | Dessa villkor innehålla länkar till användningsvillkoren och sekretesspolicy för programmet. Utgivaren ansvarar för beskriver deras regler i sina användningsvillkor. Dessutom ansvarar utgivaren för att avslöja på sätt som de använder och delar användardata i deras sekretesspolicy. Om utgivaren inte tillhandahåller länkar till dessa värden för program med flera klienter, kommer det finnas en fetstil varning på medgivandetext. |
| 9 | https://myapps.microsoft.com | Detta är en länk där användare kan granska och ta bort alla icke-Microsoft-program som för närvarande har åtkomst till sina data. |

## <a name="common-consent-scenarios"></a>Vanliga scenarier för medgivande

Här följer medgivande-upplevelser som en användare kan se i de vanliga scenarierna för godkännande:

1. Enskilda användare åtkomst till en app som uppmanar dem att användaren godkänner flow men kräver en behörighetsuppsättning som är inom den aktuella omfattningen av behörighet.
    
    1. Administratörer kan se en ytterligare kontroll i den traditionella medgivande så att de medgivande för hela klientorganisationen. Kontrollen ska vara standard på av, så endast när administratörer uttryckligen Kontrollera rutan kommer samtycker beviljas för hela klientorganisationen. Från och med dagens datum visas endast den här kryssrutan för rollen som Global administratör, så att Cloud och App delad Admin inte visas den här kryssrutan.

        ![Medgivandetext för scenariot 1a](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Användarna ser det traditionella medgivandetext.

        ![Medgivandetext för scenariot 1b](./media/application-consent-experience/consent_prompt_1b.png)

2. Enskilda användare åtkomst till en app som kräver minst en behörighet som ligger utanför deras omfattningen av administratörsbehörighet.
    1. Administratörer ser samma meddelande som 1.i som anges ovan.
    2. Användare kommer att blockeras från att godkänna att programmet och de får ett meddelande om att be sina administratör om åtkomst till appen. 
                
        ![Medgivandetext för scenariot 1b](./media/application-consent-experience/consent_prompt_2b.png)

3. Personer som navigera eller dirigeras till administratören godkänna flödet.
    1. Administrativa användare ser medgivandetext för administratören. Rubriken och Behörighetsbeskrivningar ändras på den här uppmaningen ändringar-Markera det faktum att den accepterar meddelandet kommer ger appen åtkomst till data som begärdes för hela klientorganisationen.
        
        ![Medgivandetext för scenariot 1b](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Icke-administratörer ser samma skärm som 2. ii ovan.

## <a name="next-steps"></a>Nästa steg
- Få en stegvisa översikt över [hur Azure AD-ramverket för medgivande implementerar medgivande](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Mer information lär du dig [hur ett program med flera innehavare kan använda ramverket för medgivande](active-directory-devhowto-multi-tenant-overview.md) implementerar ”användare” och ”admin” medgivande stöder mer avancerade mönster för flera nivåer.
- Lär dig [så här konfigurerar du appens utgivardomän](howto-configure-publisher-domain.md).
