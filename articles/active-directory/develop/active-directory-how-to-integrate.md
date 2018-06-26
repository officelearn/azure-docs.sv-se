---
title: Integrera med Azure Active Directory | Microsoft Docs
description: En guide till fördelarna och resurser för integrering med Azure Active Directory.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.reviewer: bryanla
ms.custom: aaddev
ms.openlocfilehash: b6de3094e903c3584aa4a57e7792e4b07711f74a
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937146"
---
# <a name="integrating-with-azure-active-directory"></a>Integrera med Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory kan organisationer företagsklass Identitetshantering för molnprogram. Azure AD-integrering och ger användarna en effektiviserad upplevelse inloggning och hjälper ditt program som överensstämmer med IT-princip.

## <a name="how-to-integrate"></a>Hur du integrerar
Det finns flera sätt för programmet att integrera med Azure AD. Dra nytta av så många eller som fåtal av dessa scenarier som är lämpligt för ditt program.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Stöd för Azure AD som ett sätt att logga In på ditt program
**Minska inloggning friktion och minska kostnaderna för support.** Genom att använda Azure AD för att logga in på ditt program kan användarna inte har ett mer namn och lösenord. Som en utvecklare har du en mindre lösenord för att lagra och skydda. Inte behöver hantera glömt lösenord kanske enbart betydande besparingar. Azure AD används logga in för några av världens mest populära molnprogram, inklusive Office 365 och Microsoft Azure. Med flera hundra miljoner användare från miljontals organisationer risken kan dina användare är redan inloggad i Azure AD. Lär dig mer om [lägger till stöd för Azure AD inloggning](active-directory-authentication-scenarios.md).

**Förenkla logga in för ditt program.**  När du registrerar dig för programmet kan Azure AD skicka viktig information om en användare så att du före fylla din registrering formulär eller ta bort den helt. Användare kan registrera dig för ditt program med hjälp av sina Azure AD-kontot via en bekant medgivande upplevelse liknar de som finns i sociala media och mobila program. Alla användare kan logga och logga in till ett program som är integrerad med Azure AD utan inblandning av IT. Lär dig mer om [registrerat dig ditt program för Azure AD-kontot inloggningen](../../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Bläddra efter användare, hantera Användaretablering och styra åtkomsten till ditt program
**Sök efter användare i katalogen.**  Använd Graph API för att hjälpa användarna att söka och leta efter andra personer i organisationen när bjuda in andra eller bevilja åtkomst, i stället för att begära att ange e-post-adresser. Användare kan bläddra i en bekant adress book style-gränssnittet, inklusive visar information om organisationens hierarki. Lär dig mer om den [Graph API](active-directory-graph-api.md).

**Använd Active Directory-grupper och distributionslistor kunden redan hanterar igen.**  Azure AD innehåller de grupper som kunden redan använder för e-distribution och hantering av åtkomst. Med Graph API kan återanvända dessa grupper i stället för att kunden ska skapa och hantera en separat uppsättning grupper i ditt program. Information om grupp kan också skickas till ditt program i inloggning token. Lär dig mer om den [Graph API](active-directory-graph-api.md).

**Använda Azure AD för att styra vem som har åtkomst till ditt program.**  Administratörer och ägare i Azure AD kan ge behörighet till program till användare och grupper. Med Graph API kan du läsa den här listan och använda den för att styra etablering och avetablering av resurser och åtkomst till i ditt program.

**Använda Azure AD för roller-baserad åtkomstkontroll.**  Administratörer och ägare kan tilldela användare och grupper roller som du definierar när du registrerar ditt program i Azure AD. Rollen informationen skickas till ditt program i inloggning token och kan också läsa med Graph API. Lär dig mer om [med hjälp av Azure AD för auktorisering](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Få åtkomst till användarens profil, kalender, e-post, kontakter, filer och mer
**Azure AD är auktoriserings-servern för Office 365 och andra Microsoft-företagstjänster.**  Om du stöder Azure AD för inloggning till programmet eller länka dina aktuella användarkonton till Azure AD-användarkonton med OAuth 2.0-stöd, kan du begära Läs- och skrivbehörighet till en användares profil, kalender, e-post, kontakter, filer och annan information. Du kan sömlöst skriva händelser till användarens kalender och läsa eller skriva filer till sina OneDrive. Lär dig mer om [åtkomst till Office 365-API: er](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Uppgradera ditt program i Azure och Office 365 marknadsplatser
**Uppgradera ditt program till miljontals organisationer som redan använder Azure AD.**  Användare söka och bläddra dessa marknadsplatser använder redan en eller flera molntjänster, gör dem kvalificerade kunder för tjänst i molnet. Mer information om att uppgradera ditt program i [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**När användare loggar för ditt program, visas den i Azure AD-åtkomstpanelen och Office 365 app starta.**  Användare kommer att kunna snabbt och enkelt återgå till ditt program senare kan förbättra användaren engagement. Lär dig mer om den [Azure AD-åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Säker kommunikation för enhet-till-tjänst och tjänster
**Använda Azure AD för Identitetshantering av enheter och tjänster minskar du behöver skriva koden och för möjliggör för IT för att hantera åtkomst.**  Tjänster och enheter kan hämta token från Azure AD med hjälp av OAuth och använda dessa token för åtkomst till webb-API: er. Använda Azure AD kan du undvika skriva Autentiseringskod för sammansatt. Eftersom identiteten för tjänster och enheter lagras i Azure AD kan IT kan hantera nycklar och återkallade certifikat på en plats i stället för att du gör detta separat i ditt program.

## <a name="benefits-of-integration"></a>Fördelar med integrering
Integrering med Azure AD ingår fördelar som inte kräver att skriva ytterligare kod.

### <a name="integration-with-enterprise-identity-management"></a>Integrering med Enterprise Identity Management
**Hjälpa ditt program som överensstämmer med IT-principer.**  Organisationer integrera sina företagssystem identity management med Azure AD, så när en person lämnar organisationen, de automatiskt förlorar åtkomsten till programmet utan att behöva vidta ytterligare åtgärder för IT. IT kan hantera vem som kan komma åt programmet och ta reda på vilka principer för åtkomst krävs – för exempel Multi-Factor authentication - minskar behovet av att skriva kod för att följa komplexa företagets principer. Azure AD ger administratörer en detaljerad granskningsloggen för som loggat in på ditt program så IT kan spåra användning.

**Azure AD utökar Active Directory till molnet så att programmet kan integreras med AD.**  Många organisationer över hela världen använder Active Directory UPN-inloggning och identity management-systemet och kräva sina program för att arbeta med AD. Appen integrerar med Azure AD kan integreras med Active Directory.

### <a name="advanced-security-features"></a>Funktioner för avancerad säkerhet
**Multifaktorautentisering.**  Azure AD innehåller interna multifaktorautentisering. IT-administratörer kan kräva multifaktorautentisering åtkomst till ditt program så att du inte behöver code detta stöd själv. Lär dig mer om [Multifaktorautentisering](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Avvikande inloggning identifiering.**  Azure AD bearbetar fler än en miljard inloggningar per dag när maskininlärningsalgoritmer för att identifiera misstänkt aktivitet och meddela IT-administratörer för möjliga problem. Programmet hämtar fördelen med det här skyddet genom att stödja Azure AD-inloggning. Lär dig mer om [Visa rapport för Azure Active Directory-åtkomst](../active-directory-view-access-usage-reports.md).

**Villkorlig åtkomst.**  Förutom multifaktorautentisering, Administratörer kan kräva att vissa villkor vara uppfyllda innan användarna kan logga in på ditt program. Villkoren som du kan ange omfattar IP-adressintervall klientenheter, medlemskap i angivna grupperna och tillståndet för den enhet som används för åtkomst. Lär dig mer om [villkorlig åtkomst i Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Enkel utveckling
**Standardprotokollen.**  Microsoft strävar efter att stödja branschstandarder. Azure AD stöder SAML 2.0, OpenID Connect 1.0, OAuth 2.0 och WS-Federation 1.2-autentiseringsprotokoll. Graph API är OData 4.0-kompatibel. Om programmet redan har stöd för SAML 2.0- eller OpenID Connect 1.0-protokoll för federerad inloggning, kan det vara enkelt att lägga till stöd för Azure AD. Lär dig mer om [autentiseringsprotokoll som stöds av Azure AD](active-directory-authentication-protocols.md).

**Bibliotek med öppen källkod.**  Microsoft tillhandahåller bibliotek med öppen källkod för fullt stöd för populära språk och plattformar som ska gå fortare. Källkoden är licensierad under Apache 2.0 och du kan duplicera och bidra till projekt. Lär dig mer om [Azure AD-autentiseringsbibliotek](active-directory-authentication-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Världen och hög tillgänglighet
**Azure AD har distribuerats i datacenter runtom i världen och hanteras och övervakas dygnet runt.**  Azure AD är identity management-systemet för Microsoft Azure och Office 365 och har distribuerats i 28 Datacenter runtom i världen. Katalogdata är säkert att replikeras till minst tre datacenter. Globala belastningsutjämnare och kontrollera åtkomst för användare närmaste kopia av Azure AD som innehåller data och omdirigera automatiskt begäranden till andra Datacenter om ett problem har upptäckts.

## <a name="next-steps"></a>Nästa steg
[Börja skriva kod](active-directory-developers-guide.md#get-started).

[Logga In med hjälp av Azure AD användare](active-directory-authentication-scenarios.md)

