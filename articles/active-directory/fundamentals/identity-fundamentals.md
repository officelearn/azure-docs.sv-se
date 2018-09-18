---
title: Vad är grunderna i Azure identitets- och åtkomsthantering? – Azure Active Directory | Microsoft Docs
description: Läs mer om de avancerade funktionerna och ytterligare verktyg som är tillgängliga med Azure Active Directory Premium-versionerna.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2018
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: f7baa29c77ae4af9813bfc755a39cc07288a3ad2
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734683"
---
# <a name="what-are-the-fundamentals-of-azure-identity-and-access-management"></a>Vad är grunderna i Azure identitets- och åtkomsthantering?
Azure AD Premium är en molnbaserad identitets- och hanteringslösning, med funktioner för avancerat skydd. Dessa avancerade funktioner att tillhandahålla en säker identitet för alla dina appar, identitetsskydd (förbättrad med den [Microsoft information security graph](https://www.microsoft.com/security/intelligence)), och [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md). Azure AD hjälper dig för att skydda användarnas identiteter i realtid, vilket hjälper dig att skapa riskbaserad och anpassningsbar åtkomstprinciper runt data i din organisation.

Titta på den här korta videon för en snabb översikt över identitetshantering och skydd i Azure AD:
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Azure AD tillhandahåller också en uppsättning av verktyg som hjälper dig skydda, automatisera och hantera din miljö, inklusive återställning av lösenord, hantering av användare och grupp och app-begäranden. Azure AD kan också hjälpa dig att hantera enheter som ägs av användare och åtkomst till och kontroll av programvara som en tjänst (SaaS)-appar.

Mer information om kostnaderna för Azure Active Directory Premium-versionerna och tillhörande verktyg finns i [priser för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Anslut lokal Active Directory till Azure AD och Office 365
Utöka din lokala Active Directory-implementering i molnet genom att integrera dina lokala kataloger med Azure AD via [hybrididentitetshantering](https://aka.ms/aadframework). [Azure AD Connect](../connect/active-directory-aadconnect.md) ger den här integreringen och ge användarna en enda identitet och enkel inloggning (SSO) åtkomst till både lokala resurser och cloud services, till exempel Office 365.

Azure AD Connect ersätter äldre versioner av identitetsintegrationsverktyg som DirSync och Azure AD Sync, vilket hjälper för att stödja dina behov med identity synkronisering mellan lokala och Azure AD. Azure AD Connect-synkronisering är aktiverat via:

- **Synkronisering.** Ansvarig för att skapa användare, grupper och andra objekt. Det är också ansvarig för att se till att identitetsinformationen för dina lokala användare matchar vad som finns i Azure AD. Aktivera tillbakaskrivning av lösenord hjälper också att synkronisera dina lokala kataloger när användare uppdaterar lösenord i Azure AD.

- Autentisering. Det är viktigt att välja rätt autentiseringsmetod när du konfigurerar din Azure AD-hybrididentitetslösning. Du kan välja molnautentisering (Lösenordshashsynkronisering / direktautentisering) eller federerad autentisering (AD FS) för din organisation. Mer information om tillgängliga alternativen finns i [välja rätt autentiseringsmetod för din Azure Active Directory-hybrididentitetslösning](https://aka.ms/auth-options).

- **Övervakning av hälsotillstånd.** Azure AD Connect Health tillhandahåller övervakning och en central plats på Azure portal för att visa den här aktiviteten. Mer information finns i [Övervaka den lokala identitetsinfrastrukturen och synkroniseringstjänster i molnet](../connect-health/active-directory-aadconnect-health.md).

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>Öka produktiviteten och minska supportkostnader med självbetjänings- och enkel inloggning-miljöer
Användare sparar tid när de har ett användarnamn och lösenord, tillsammans med en konsekvent upplevelse på alla enheter. Användare också spara tid genom att utföra uppgifter för självbetjäning som[återställa glömda lösenord](../user-help/active-directory-passwords-update-your-own-password.md) eller för att begära åtkomst till ett program utan att behöva vänta på att få hjälp från supportavdelningen.

Främjar enkel inloggning och enhetlig upplevelse, Azure AD [utökar din lokala Active Directory](../connect/active-directory-aadconnect.md) till molnet, så att användarna använder sina primära organisationskonto för sina domänanslutna enheter företagets resurser, och webb- och SaaS-program som de ska användas för att få ett klar. 

Dessutom programåtkomst kan automatiskt etablerade (eller avetableras) baserat på gruppmedlemskap och anställningsstatus för en användare, vilket hjälper dig att styra åtkomsten till galleriappar eller dina egna lokala appar som du har utvecklat och publicerats via den [Azure AD-programproxyn](../manage-apps/application-proxy.md).

## <a name="manage-and-control-access-to-your-organizational-resources"></a>Hantera och styr åtkomsten till din organisations resurser
Lösningar för Microsoft identitets- och hjälper dig att skydda åtkomsten till appar och resurser i din organisations datacenter och i molnet. Den här åtkomsthantering som hjälper dig för att ger ytterligare kontrollmöjligheter verifiering som [Multifaktorautentisering](../authentication/concept-mfa-howitworks.md) och [principer för villkorlig åtkomst](../conditional-access/overview.md). Övervakning av misstänkt aktivitet via Avancerad säkerhetsrapportering, granskning och aviseringar kan också hjälpa för att minska potentiella säkerhetsproblem.

Använda principer för villkorlig åtkomst i Azure AD Premium kan du skapa principbaserad åtkomstregler för alla Azure AD-anslutna appar, till exempel anpassade appar som körs i molnet eller lokala eller webbappar på SaaS-appar). Azure AD utvärderar reglerna i realtid, verkställer dem. när en användare försöker komma åt en app. Azure identity protection-principer gör att du automatiskt vidta åtgärder (som blockerar åtkomst, Multifaktorautentisering framtvingades eller återställa användarlösenord) om misstänkt aktivitet upptäcks.

## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management
[Privileged Identity Management (PIM)](../privileged-identity-management/pim-getting-started.md), ingår i Azure Active Directory Premium 2-utgåva, hjälper dig att upptäcka, begränsa och övervaka administrativa konton och deras åtkomst till resurser i din Azure Active Directory och andra Microsoft online services. PIM hjälper dig också att administrera på begäran administrativ åtkomst för den exakta tidsperioden som du behöver, vilket innebär att du tillåter administratörer att begära Multi-Factor Authentication autentiseras, tillfällig rättighetsökning deras under en förinställd tidsperiod innan det går tillbaka till en normal användartillstånd sina konton.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure AD-arkitekturen finns i [vad är Azure AD-arkitekturen?](active-directory-architecture.md).
