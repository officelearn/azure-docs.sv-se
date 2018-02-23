---
title: Grunderna i Azure Identitetshantering
description: "Molnbaserade identiteter är numera det bästa sättet att behålla kontrollen över och få insyn i hur och när användare får åtkomst till företagets program och data."
keywords: 
author: jeffgilb
manager: mtillman
ms.reviewr: jsnow
ms.author: jeffgilb
ms.date: 07/05/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: a7eb669aac109ae8cde6d75129004d322c270651
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2018
---
# <a name="fundamentals-of-azure-identity-management"></a>Grunderna i Azure Identitetshantering

Eftersom fler och fler digitala företagsresurser lever utanför företagsnätverket, i molnet och på enheter, blir nödvändigt med en bra molnbaserade identitets- och lösning. Molnbaserad identiteter är nu det bästa sättet att behålla kontrollen över och insyn i hur och när användare åtkomst till företagets program och data.

Microsoft har skydda molnbaserade identiteter för över tio år och nu med [Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), skydd datorerna är tillgängliga för dig. Med Azure AD Kontrollera Företagsadministratörer enkelt användar- och accountability med bättre säkerhet och styrning än någonsin tidigare.

Azure AD Premium är en molnbaserad identitets- och lösning med avancerade funktionerna som gör att en säker identitet för alla appar, identitetsskydd (förbättrad av den [Microsoft intelligence säkerhet graph](https://www.microsoft.com/en-us/security/intelligence)), och Privileged Identity Management. Inte en övervakning eller reporting verktyget Azure AD Premium kan skydda användaridentiteter i realtid och du kan skapa risk-baserade, anpassningsbar åtkomstprinciper för att skydda din organisations data.

Titta på den här korta videon för en snabb översikt över Azure AD identity hanteringen och skyddet:
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Microsoft ger inte bara en identitet som tar dig överallt, men också en uppsättning verktyg för att automatisera, att skydda och hantera IT inom din organisation. Även efter ankomsten av molntjänster har fortfarande begäran för att hantera och styra IT-uppgifter som supportavdelningen anrop till återställning av lösenord, hantering av användare och programförfrågningar. Vilket komplicerar saker ytterligare tar anställda nu med sina personliga enheter till arbetet och använder tillgängliga SaaS-program. Det gör att underhålla kontroll över sina program över företagets datacenter och offentliga molnplattformar svårigheter.

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Ansluta lokala Active Directory med Azure AD och Office 365
Organisationer som har gjort stora investeringar i lokala Active Directory kan utöka de placeringar till molnet genom att integrera sina lokala kataloger med Azure AD till [hybrididentitetshantering](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Det gör användarna mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst till resurser oavsett plats. Användare och organisationer kan sedan använda enkel inloggning (SSO) för åtkomst både lokala resurser och molntjänster som Office 365.

[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) är det enda verktyg du behöver integrationen klar. Azure AD Connect innehåller funktioner för att stödja dina behov för synkronisering av identitet och ersätter äldre versioner av identitetsintegrationsverktyg som DirSync och Azure AD Sync. Med Azure AD Connect har aktiverats för Identitetshantering och synkronisering mellan lokala och Azure AD via:

- Synkronisering – Den här komponenten är ansvarig för att skapa användare, grupper och andra objekt. Den är också ansvarig för att se till att identitetsinformationen för dina lokala användare och grupper matchar molnet. Tillbakaskrivning av lösenord kan också aktiveras för att synkronisera lokala kataloger när användaren uppdaterar sina lösenord i Azure AD.
- AD FS - Federation är en valfri funktion som tillhandahålls av Azure AD Connect som kan användas för att konfigurera en hybridmiljö med hjälp av en lokal AD FS-infrastruktur. Federation kan användas av organisationer i adress komplexa distributioner, till exempel enkel inloggning vid tillämpning av AD-inloggning princip och smartkort eller tredje parts MFA.
- Hälsoövervakning – [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health) kan tillhandahålla robust övervakning och ger en central plats i Azure portal för att visa den här aktiviteten.

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>Öka produktiviteten och minska supportkostnaderna med självbetjäning och enkel inloggning

Anställda är mer produktiva när de har ett enda användarnamn och lösenord för att komma ihåg och en konsekvent upplevelse från varje enhet. Sparar också tid när de kan utföra självbetjäning aktiviteter som [återställer glömt lösenordet](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) eller begär åtkomst till ett program utan att vänta på stöd från supportavdelningen.

Azure AD [utökar lokala Active Directory-](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) i molnet, hjälper användarna att använda sina primära organisationskonto för både sina domänanslutna enheter företagets resurser och alla webb- och SaaS-program som de behöver för att Använd för att få arbetet gjort. Förutom att inte behöva komma ihåg flera uppsättningar av användarnamn och lösenord, användarnas programåtkomst kan också automatiskt etablera (eller tas bort) baserat på deras organisation gruppmedlemskap och deras status som en medarbetare. Och du kan kontrollera att åtkomsten för galleriet appar eller egna lokala appar som du har utvecklats och publicerats via den [Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="manage-and-control-access-to-corporate-resources"></a>Hantera och styra åtkomsten till företagsresurser
Microsoft identitets- och lösningar hjälp IT skydda åtkomst till program och resurser i företagets datacenter och i molnet, aktivera nivåer av verifiering som [multifaktorautentisering](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)och [principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). Misstänkt aktivitet med avancerad säkerhet rapportering, granskning och aviseringar hjälper dig att minimera potentiella säkerhetsproblem.

Principer för villkorlig åtkomst i Azure AD Premium ger du företagsadministratören, möjlighet att skapa regler för gruppolicy-baserad åtkomst för alla Azure AD-anslutna program (SaaS-appar, anpassade appar som körs i molnet eller lokala webbprogram). Azure AD utvärderar dessa principer i realtid och tillämpar dem när en användare försöker komma åt ett program. Azure identity protection-principer kan du vidta åtgärder automatiskt om misstänkt aktivitet har identifierats. Dessa åtgärder kan omfatta blockerar åtkomsten till användare med hög risk, multifaktorautentisering framtvingades och återställa användarlösenord om det verkar som om autentiseringsuppgifter har komprometterats.


## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management

[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started), ingår i Azure Active Directory Premium P2-erbjudandet kan du identifiera, begränsa och övervaka administrativa konton och deras åtkomst till resurser i Azure Active Directory och andra Microsoft online-tjänster. Du kan dessutom administrera på begäran administrativ åtkomst för den exakta tidsperioden som du behöver.

Privileged Identity Management kan tillämpa administratörsbehörighet på begäran så att administratörer kan begära Multi-Factor autentiserade, tillfällig rättighetsökning sina för förkonfigurerade tidsperioder innan sina konton återgår till normal användare tillstånd.

## <a name="benefits-of-azure-identity"></a>Fördelarna med Azure identitet

Med Azure identity management kan du:

-   Skapa och hantera en enstaka identitet för varje användare i hela företaget, synkronisera användare, grupper och enheter med [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

-   Ger enkel inloggning tillgång till ditt program, bland annat tusentals förintegrerade SaaS-appar eller ge säker fjärråtkomst till lokala SaaS-program med den [Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

-   Aktivera åtkomst programsäkerhet genom att tillämpa regelbaserad [Multifaktorautentisering](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) både lokala och molnbaserade program.

-   Förbättra produktiviteten för användaren med [Självbetjäning för lösenordsåterställning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords), och grupp och programmet begäranden med hjälp av den [MyApps portal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help).

-   Dra nytta av den [hög tillgänglighet och tillförlitlighet](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications) av över hela världen, företagsklass, molnbaserade identitets- och hanteringslösning.

## <a name="next-steps"></a>Nästa steg
[Mer information om identitetslösningar i Azure](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions)
