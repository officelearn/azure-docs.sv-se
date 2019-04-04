---
title: Hur du integrerar med Azure Active Directory | Microsoft Docs
description: En guide till fördelarna med och resurser för integrering med Azure Active Directory.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/04/2019
ms.author: celested
ms.reviewer: bryanla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 687b2848dc4bcf4e0c8935795eb66e07c3a5a7bd
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894635"
---
# <a name="integrating-with-azure-active-directory"></a>Integrera med Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory (Azure AD) kan organisationer företagsklass Identitetshantering för molnprogram. Azure AD-integrering ger användarna en smidig inloggningsupplevelse och bidrar till att dina program som överensstämmer med IT-princip.

## <a name="how-to-integrate"></a>Så här integrerar du

Det finns flera sätt för ditt program att integrera med Azure AD. Dra nytta av så många eller så lite av dessa scenarier som passar ditt program.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Stöd för Azure AD som ett sätt att logga in på ditt program

**Minska friktionen inloggning och minska kostnaderna för support.** Genom att använda Azure AD för att logga in på ditt program kan användarna inte har ett mer namn och lösenord för att komma ihåg. Som utvecklare har du en mindre lösenord för att lagra och skydda. Inte behöver hantera glömda lösenordsåterställning kan vara en fristående avsevärda besparingar. Azure AD Driver logga in för några av världens mest populära molnprogram, inklusive Office 365 och Microsoft Azure. Med hundratals miljoner användare från miljontals organisationer risken är användaren redan har loggat in till Azure AD. Läs mer om [lägger till stöd för Azure AD-inloggningen](authentication-scenarios.md).

**Förenkla logga in för ditt program.**  När du registrerar dig för ditt program, kan Azure AD skicka viktig information om en användare så att du redan fylla din registrering formuläret eller eliminera den helt. Användare kan registrera sig för ditt program med sina Azure AD-konto via en bekant medgivande upplevelse som liknar dem som finns i sociala medier och mobilprogram. Alla användare kan registrera dig och logga in till ett program som är integrerad med Azure AD utan att IT-utrustning. Läs mer om [registrera dig ditt program för Azure AD-kontoinloggning](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Bläddra efter användare, hantera etableringen av användare och styra åtkomsten till ditt program

**Sök efter användare i katalogen.**  Använd Graph API för att hjälpa användare att söka och bläddra till andra personer i organisationen när du bjuder in andra eller beviljar åtkomst, i stället för att behöva ange e-post-adresser. Användare kan bläddra med hjälp av ett bekant adressboken style gränssnitt, inklusive visar information om organisationens hierarki. Läs mer om den [Graph API](active-directory-graph-api.md).

**Återanvända Active Directory-grupper och distributionslistor kunden redan hanterar.**  Azure AD innehåller de grupper som kunden redan använder för e-distribution och hantering av åtkomst. Med Graph API, återanvända dessa grupper i stället för att dina kunder för att skapa och hantera en separat uppsättning grupper i ditt program. Gruppinformation kan även skickas till ditt program i inloggning token. Läs mer om den [Graph API](active-directory-graph-api.md).

**Använda Azure AD för att kontrollera vem som har åtkomst till programmet.**  Administratörer och programägare i Azure AD kan ge åtkomst till program till specifika användare och grupper. Med Graph API kan du läsa den här listan och använda den för att styra aktivering och inaktivering av resurser och få åtkomst till i ditt program.

**Använd Azure AD för roller-baserad åtkomstkontroll.**  Administratörer och programägare kan tilldela användare och grupper till roller som du definierar när du registrerar ditt program i Azure AD. Rollinformation skickas till ditt program i inloggning token och kan också läsa med Graph API. Läs mer om [med hjälp av Azure AD för auktorisering](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Få åtkomst till användarens profil, kalender, e-post, kontakter, filer och mer

**Azure AD är auktoriseringsservern för Office 365 och andra Microsoft-företagstjänster.**  Om du stöder Azure AD för att logga in på ditt program eller länka dina aktuella användarkonton till Azure AD-användarkonton med hjälp av OAuth 2.0-stöd, kan du begära Läs- och skrivbehörighet till en användares profil, kalender, e-post, kontakter, filer och annan information. Du kan smidigt skriva händelser till användares kalender, och läsa eller skriva filer till OneDrive. Läs mer om [åtkomst till Office 365 API: er](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Marknadsför ditt program i Azure och Office 365 marknadsplatser

**Marknadsför ditt program till miljontals organisationer som redan använder Azure AD.**  Användare som Sök och bläddra bland dessa marknadsplatser använder redan en eller flera molntjänster, vilket gör dem kvalificerade kunder för tjänst i molnet. Mer information om att uppgradera ditt program i [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**När användarna registrerar sig för ditt program, visas den i sina Azure AD-åtkomstpanelen och Office 365-appstartaren.**  Användare kommer att kunna snabbt och enkelt gå tillbaka till ditt program senare kan förbättra användarnas engagemang. Läs mer om den [Azure AD-åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Säker kommunikation för enhet-till-tjänst och tjänst-till-tjänst

**Med hjälp av Azure AD för Identitetshantering av tjänster och enheter minskar den kod som du behöver skriva och gör att IT-om du vill hantera åtkomsten.**  Tjänster och enheter kan hämta token från Azure AD med hjälp av OAuth och använda dessa tokens för att få åtkomst till webb-API: er. Använda Azure AD kan du undvika skriva komplex autentiseringslogik kod. Eftersom identiteten för de tjänster och enheter lagras i Azure AD kan IT kan hantera nycklar och återkallade certifikat på en plats i stället för att göra detta separat i ditt program.

## <a name="benefits-of-integration"></a>Fördelarna med integrering

Integrering med Azure AD levereras med fördelar som inte behöver du skriva ytterligare kod.

### <a name="integration-with-enterprise-identity-management"></a>Integrering med enterprise-Identitetshantering

**Hjälpa programmet att uppfylla IT-principer.**  Organisationer integrera hanteringssystem för identitet sina företag med Azure AD, så när en person lämnar en organisation, de automatiskt förlorar åtkomst till ditt program utan att behöva vidta ytterligare åtgärder för IT. IT kan hantera vem som kan komma åt ditt program och ta reda på vilka principer för åtkomst krävs – för exempel Multi-Factor authentication - minskar behovet av att skriva kod för att uppfylla komplexa företagets principer. Azure AD ger administratörer en detaljerad granskningsloggen för som loggat in på ditt program så IT kan spåra användning.

**Azure AD utökar Active Directory till molnet så att ditt program kan integreras med AD.**  Många organisationer över hela världen använder Active Directory huvudsaklig inloggning och identitetshanteringssystem och kräver sina program ska fungera med AD. Integrera med Azure AD är din app med Active Directory.

### <a name="advanced-security-features"></a>Avancerade säkerhetsfunktioner

**Multifaktorautentisering.**  Azure AD tillhandahåller inbyggd multifaktorautentisering. IT-administratörer kan kräva multifaktorautentisering åtkomst till ditt program så att du inte behöver koda det själv. Läs mer om [Multifaktorautentisering](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Avvikande inloggning identifiering.**  Azure AD bearbetar fler än en miljard inloggningar per dag, när machine learning-algoritmer för att identifiera misstänkt aktivitet och meddela IT-administratörer för möjliga problem. Eftersom det finns stöd för Azure AD-inloggningen, hämtar programmet fördelen med det här skyddet. Läs mer om [visa Azure Active Directory access-rapport](../active-directory-view-access-usage-reports.md).

**Villkorlig åtkomst.**  Förutom multifaktorautentisering, Administratörer kan kräva särskilda villkor vara uppfyllda innan användarna kan logga in på ditt program. Villkor som kan ställas in omfattar IP-adressintervallet för klientenheter, medlemskap i angivna grupperna och tillståndet för enheten som används för åtkomst. Läs mer om [villkorlig åtkomst i Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Enkel utveckling

**Branschens standardprotokoll.**  Microsoft strävar efter att ge stöd branschstandarder. Microsoft identity-plattformen har stöd för de vanliga OAuth 2.0 och OpenID Connect 1.0-protokoll. Läs mer om [autentiseringsprotokoll för Microsoft identity-plattformen](active-directory-v2-protocols.md).

**Bibliotek med öppen källkod.**  Microsoft tillhandahåller bibliotek med helt öppen källkod för populära språk och plattformar för att snabba upp utvecklingen. Källkoden är licensierad under Apache 2.0 och du kan välja att Förgrena och bidra till projekten. Läs mer om [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Närvaro i hela världen och hög tillgänglighet

**Azure AD har distribuerats i datacenter runtom i världen och hanteras och övervakas dygnet runt.**  Azure AD är identitetshanteringssystem för Microsoft Azure och Office 365 och distribueras i 28 Datacenter runtom i världen. Katalogdata är säkert att replikeras till minst tre datacenter. Global belastningsutjämnare och kontrollera användare åtkomst den närmaste kopian av Azure AD som innehåller sina data och omdirigera automatiskt begäranden till andra Datacenter om ett problem har identifierats.

## <a name="next-steps"></a>Nästa steg

[Börja skriva kod](v2-overview.md#getting-started).

[Logga in med hjälp av Microsoft identity-plattformen användare](authentication-scenarios.md)

