---
title: Så här integrerar du med Azure Active Directory | Microsoft-dokument
description: Lär dig fördelarna med att integrera ditt program med Azure Active Directory och få resurser för funktioner som förenklad inloggning, identitetshantering, multifaktorautentisering och åtkomstkontroll.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 5e4c6fb91b93b1035c68350a5f77a3c16db7323f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300120"
---
# <a name="integrating-with-azure-active-directory"></a>Integrera med Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

I den här artikeln får du lära dig mer om fördelarna med att integrera ditt program med Azure Active Directory (Azure AD) och få resurser för integrering. Azure AD ger organisationer med identitetshantering i företagsklass för molnprogram. Azure AD-integrering ger dina användare en strömlinjeformad inloggningsupplevelse och hjälper ditt program att följa IT-principen.

## <a name="how-to-integrate"></a>Så här integrerar du

Det finns flera sätt för ditt program att integrera med Azure AD. Dra nytta av så många eller så få av dessa scenarier som är lämpliga för ditt program.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Stöd Azure AD som ett sätt att logga in på ditt program

**Minska instenktionen och minska supportkostnaderna.** Genom att använda Azure AD för att logga in på ditt program har användarna inte ett namn och lösenord att komma ihåg. Som utvecklare har du ett lösenord mindre att lagra och skydda. Att inte behöva hantera glömda lösenordsåterställning kan vara en betydande besparing ensam. Azure AD-befogenheter loggar in för några av världens mest populära molnprogram, inklusive Office 365 och Microsoft Azure. Med hundratals miljoner användare från miljontals organisationer är chansen stor att din användare redan är inloggad på Azure AD. Läs mer om [att lägga till stöd för Azure AD-inloggning](authentication-scenarios.md).

**Förenkla registreringen för ditt program.**  När du registrerar dig för ditt program kan Azure AD skicka viktig information om en användare så att du kan fylla i ditt registreringsformulär eller eliminera det helt. Användare kan registrera sig för ditt program med sitt Azure AD-konto via en välbekant samtyckesupplevelse som liknar dem som finns i sociala medier och mobilappar. Alla användare kan registrera sig och logga in på ett program som är integrerat med Azure AD utan att kräva IT-inblandning. Läs mer om [hur du registrerar ditt program för inloggning på Azure AD-konto](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Bläddra efter användare, hantera användaretablering och kontrollera åtkomsten till ditt program

**Bläddra efter användare i katalogen.**  Använd Microsoft Graph API för att hjälpa användare att söka och söka efter andra personer i organisationen när de bjuder in andra eller beviljar åtkomst, i stället för att kräva att de skriver e-postadresser. Användare kan bläddra med hjälp av ett välbekant gränssnitt för adressboksstil, inklusive att visa information om organisationshierarkin. Läs mer om [Microsoft Graph API](https://docs.microsoft.com/graph/overview).

**Återanvända Active Directory-grupper och distributionslistor som kunden redan hanterar.**  Azure AD innehåller de grupper som kunden redan använder för e-postdistribution och hantering av åtkomst. Använd Microsoft Graph API och återanvända dessa grupper i stället för att kräva att kunden skapar och hanterar en separat uppsättning grupper i programmet. Gruppinformation kan också skickas till din ansökan i inloggningstoken. Läs mer om [Microsoft Graph API](https://docs.microsoft.com/graph/overview).

**Använd Azure AD för att styra vem som har åtkomst till ditt program.**  Administratörer och programägare i Azure AD kan tilldela åtkomst till program till specifika användare och grupper. Med Hjälp av Microsoft Graph API kan du läsa den här listan och använda den för att styra etablering och avetablering av resurser och åtkomst i ditt program.

**Använd Azure AD för rollerbaserad åtkomstkontroll.**  Administratörer och programägare kan tilldela användare och grupper till roller som du definierar när du registrerar ditt program i Azure AD. Rollinformation skickas till ditt program i inloggningstoken och kan även läsas med hjälp av Microsoft Graph API. Läs mer om [hur du använder Azure AD för auktorisering](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Få tillgång till användarens profil, kalender, e-post, kontakter, filer med mera

**Azure AD är auktoriseringsservern för Office 365 och andra Microsoft-företagstjänster.**  Om du stöder Azure AD för inloggning till ditt program eller stöder att länka dina aktuella användarkonton till Azure AD-användarkonton med OAuth 2.0 kan du begära läs- och skrivåtkomst till en användares profil, kalender, e-post, kontakter, filer och annan information. Du kan smidigt skriva händelser till användarens kalender och läsa eller skriva filer till deras OneDrive. Läs mer om [hur du öppnar Api:erna för Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Marknadsföra ditt program på Azure- och Office 365-marknadsplatserna

**Befordra ditt program till de miljontals organisationer som redan använder Azure AD.**  Användare som söker och bläddrar bland dessa marknadsplatser använder redan en eller flera molntjänster, vilket gör dem till kvalificerade molntjänstkunder. Läs mer om hur du marknadsför ditt program [på Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**När användare registrerar sig för ditt program visas det i deras Azure AD-åtkomstpanel och Office 365-appstartikonen.**  Användarna kan snabbt och enkelt återgå till ditt program senare, vilket förbättrar användarnas engagemang. Läs mer om [åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md)för Azure AD .

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Säker kommunikation mellan enheter och service och service till service

**Om du använder Azure AD för identitetshantering av tjänster och enheter minskar koden du behöver skriva och IT-tjänsten kan hantera åtkomst.**  Tjänster och enheter kan hämta token från Azure AD med OAuth och använda dessa token för att komma åt webb-API:er. Med Azure AD kan du undvika att skriva komplex autentiseringskod. Eftersom identiteterna för tjänsterna och enheterna lagras i Azure AD kan IT hantera nycklar och återkallning på ett ställe i stället för att behöva göra detta separat i ditt program.

## <a name="benefits-of-integration"></a>Fördelar med integration

Integrering med Azure AD levereras med fördelar som inte kräver att du skriver ytterligare kod.

### <a name="integration-with-enterprise-identity-management"></a>Integrering med företagsidentitetshantering

**Hjälp ditt program att följa IT-principerna.**  Organisationer integrerar sina system för hantering av företagsidentiteter med Azure AD, så när en person lämnar en organisation förlorar de automatiskt åtkomst till ditt program utan att IT behöver vidta extra åtgärder. IT kan hantera vem som kan komma åt ditt program och avgöra vilka åtkomstprinciper som krävs - till exempel multifaktorautentisering - vilket minskar ditt behov av att skriva kod för att följa komplexa företagsprinciper. Azure AD ger administratörer en detaljerad granskningslogg över vem som loggat in på ditt program så att IT kan spåra användning.

**Azure AD utökar Active Directory till molnet så att ditt program kan integreras med AD.**  Många organisationer runt om i världen använder Active Directory som sitt huvudsakliga inloggnings- och identitetshanteringssystem och kräver att deras program fungerar med AD. Om du integrerar med Azure AD integreras din app med Active Directory.

### <a name="advanced-security-features"></a>Avancerade säkerhetsfunktioner

**Multifaktorautentisering.**  Azure AD tillhandahåller inbyggd multifaktorautentisering. IT-administratörer kan kräva multifaktorautentisering för att komma åt ditt program, så att du inte behöver koda det här stödet själv. Läs mer om [multifaktorautentisering](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Avvikande inloggningsidentifiering.**  Azure AD bearbetar mer än en miljard inloggningar per dag, medan du använder maskininlärningsalgoritmer för att identifiera misstänkt aktivitet och meddela IT-administratörer om eventuella problem. Genom att stödja Azure AD-inloggning får ditt program förmånen av det här skyddet. Läs mer om [hur du visar Azure Active Directory-åtkomstrapport](../active-directory-view-access-usage-reports.md).

**Villkorlig åtkomst.**  Förutom multifaktorautentisering kan administratörer kräva att specifika villkor uppfylls innan användare kan logga in på ditt program. Villkor som kan ställas in inkluderar IP-adressintervallet för klientenheter, medlemskap i angivna grupper och tillståndet för den enhet som används för åtkomst. Läs mer om [Azure Active Directory Villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Enkel utveckling

**Branschstandardprotokoll.**  Microsoft strävar efter att stödja branschstandarder. Microsofts identitetsplattform stöder protokollen OAuth 2.0 och OpenID Connect 1.0 i branschstandard. Läs mer om [autentiseringsprotokoll för Microsoft identity platform](active-directory-v2-protocols.md).

**Bibliotek med öppen källkod.**  Microsoft tillhandahåller fullt stöd för open source-bibliotek för populära språk och plattformar för att påskynda utvecklingen. Källkoden är licensierad under Apache 2.0, och du är fri att punga och bidra tillbaka till projekten. Läs mer om [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Global närvaro och hög tillgänglighet

**Azure AD distribueras i datacenter runt om i världen och hanteras och övervakas dygnet runt.**  Azure AD är identitetshanteringssystemet för Microsoft Azure och Office 365 och distribueras i 28 datacenter runt om i världen. Katalogdata är garanterat replikerade till minst tre datacenter. Globala belastningsutjämnare säkerställer att användarna får åtkomst till den närmaste kopian av Azure AD som innehåller deras data och dirigerar automatiskt om begäranden till andra datacenter om ett problem upptäcks.

## <a name="next-steps"></a>Nästa steg

[Kom igång med att skriva kod](v2-overview.md#getting-started).

[Logga in användare med Microsofts identitetsplattform](authentication-scenarios.md)

