---
title: Så här integrerar du med Azure Active Directory | Microsoft Docs
description: Lär dig fördelarna med att integrera ditt program med Azure Active Directory och få resurser för funktioner som förenklad inloggning, identitets hantering, Multi-Factor Authentication och åtkomst kontroll.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: da694dd77186ce22bd7080540bd3b08e14f205b0
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835362"
---
# <a name="integrating-with-azure-active-directory"></a>Integrera med Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

I den här artikeln får du lära dig om fördelarna med att integrera ditt program med Azure Active Directory (Azure AD) och få resurser för integrering. Azure AD tillhandahåller organisationer med identitets hantering i företags klass för moln program. Azure AD-integrering ger användarna en strömlinjeformad inloggnings upplevelse och hjälper ditt program att följa IT-principer.

## <a name="how-to-integrate"></a>Så här integrerar du

Det finns flera sätt för ditt program att integrera med Azure AD. Dra nytta av så många eller så få av de här scenarierna som passar ditt program.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Stöd för Azure AD som ett sätt att logga in på ditt program

**Sänk in friktionen och minska support kostnaderna.** Genom att använda Azure AD för att logga in i ditt program får användarna inget namn och lösen ord att komma ihåg. Som utvecklare har du ett mindre lösen ord för att lagra och skydda. Att inte behöva hantera återställningar av glömt lösen ord kan vara en betydande besparing. Azure AD har behörighet att logga in för några av världens mest populära moln program, inklusive Office 365 och Microsoft Azure. Med hundratals miljoner användare från miljon tals organisationer, är det troligt att användaren redan är inloggad i Azure AD. Läs mer om [att lägga till stöd för Azure AD-inloggning](authentication-scenarios.md).

**Förenkla registreringen av ditt program.**  När du registrerar dig för ditt program kan Azure AD skicka viktig information om en användare så att du kan fylla i ditt registrerings formulär på förhand eller ta bort det helt. Användare kan registrera sig för ditt program med hjälp av sitt Azure AD-konto via en välbekant upplevelse som liknar dem som finns i sociala medier och mobila program. Alla användare kan registrera sig och logga in i ett program som är integrerat med Azure AD utan att behöva delta i det. Läs mer om [att signera ditt program för inloggning med Azure AD-konto](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Bläddra efter användare, hantera användar etablering och kontrol lera åtkomsten till ditt program

**Bläddra efter användare i katalogen.**  Använd Graph API för att hjälpa användarna att söka efter andra personer i organisationen när de bjuder in andra eller beviljar åtkomst, i stället för att behöva skriva e-postadresser. Användare kan bläddra med hjälp av ett välbekant adress boks format gränssnitt, inklusive Visa information om organisationshierarkin. Läs mer om [Graph API](active-directory-graph-api.md).

**Återanvända Active Directory grupper och distributions listor som kunden redan hanterar.**  Azure AD innehåller de grupper som din kund redan använder för e-postdistribution och hantering av åtkomst. Använd Graph API och återanvänd dessa grupper i stället för att kräva att kunden skapar och hanterar en separat uppsättning grupper i ditt program. Grupp information kan också skickas till ditt program i inloggnings-token. Läs mer om [Graph API](active-directory-graph-api.md).

**Använd Azure AD för att kontrol lera vem som har åtkomst till ditt program.**  Administratörer och program ägare i Azure AD kan tilldela åtkomst till program till specifika användare och grupper. Med hjälp av Graph API kan du läsa den här listan och använda den för att styra etablering och avetablering av resurser och åtkomst i ditt program.

**Använd Azure AD för roller baserade Access Control.**  Administratörer och program ägare kan tilldela användare och grupper till roller som du definierar när du registrerar ditt program i Azure AD. Roll information skickas till programmet i inloggnings-token och kan också läsas med Graph API. Läs mer om hur du [använder Azure AD för auktorisering](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Få åtkomst till användarens profil, kalender, e-post, kontakter, filer med mera

**Azure AD är auktoriseringsservern för Office 365 och andra Microsoft Business Services.**  Om du har stöd för Azure AD för inloggning i ditt program eller om du har stöd för att länka dina aktuella användar konton till Azure AD-användarkonton med OAuth 2,0, kan du begära Läs-och skriv åtkomst till en användares profil, kalender, e-post, kontakter, filer och annan information. Du kan skriva händelser till användarens kalender sömlöst och läsa eller skriva filer till deras OneDrive. Lär dig mer om att [komma åt Office 365-API: erna](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Marknadsför ditt program på Azure-och Office 365-marknads platser

**Marknadsför ditt program till miljon tals organisationer som redan använder Azure AD.**  Användare som söker efter och surfar på dessa marknads platser använder redan en eller flera moln tjänster, vilket gör dem till kvalificerade moln tjänst kunder. Lär dig mer om att marknadsföra ditt program på [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**När användarna registrerar sig för ditt program visas det i Azure AD-åtkomst panelen och Office 365-program start.**  Användarna kan snabbt och enkelt återvända till ditt program senare, vilket förbättrar användar engagemanget. Läs mer om [Azure AD-åtkomst panelen](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Skydda kommunikation från enheter till tjänst och tjänst till tjänst

**Genom att använda Azure AD för identitets hantering av tjänster och enheter minskar du koden som du behöver skriva och gör det möjligt att hantera åtkomst.**  Tjänster och enheter kan hämta token från Azure AD med OAuth och använda dessa token för att få åtkomst till webb-API: er. Med Azure AD kan du undvika att skriva komplex autentisering. Eftersom identiteterna för tjänsterna och enheterna lagras i Azure AD kan den Hantera nycklar och återkallning på en plats i stället för att behöva göra detta separat i ditt program.

## <a name="benefits-of-integration"></a>Fördelar med integrering

Integrering med Azure AD levereras med fördelar som inte kräver att du skriver ytterligare kod.

### <a name="integration-with-enterprise-identity-management"></a>Integrering med företags identitets hantering

**Hjälp ditt program att följa IT-principer.**  Organisationer integrerar sina företags identitets hanterings system med Azure AD, så när en person lämnar en organisation kommer de automatiskt att förlora åtkomsten till ditt program utan att behöva vidta ytterligare åtgärder. DEN kan hantera vem som har åtkomst till ditt program och avgör vilka åtkomst principer som krävs, till exempel Multi-Factor Authentication – vilket minskar behovet av att skriva kod för att följa komplexa företags principer. Azure AD ger administratörer en detaljerad Gransknings logg över vem som har loggat in på ditt program, så att den kan spåra användningen.

**Azure AD utökar Active Directory till molnet så att ditt program kan integreras med AD.**  Många organisationer runtom i världen använder Active Directory som sitt huvudsakliga inloggnings-och identitets hanterings system och kräver att deras program fungerar med AD. Integrering med Azure AD integrerar din app med Active Directory.

### <a name="advanced-security-features"></a>Avancerade säkerhetsfunktioner

**Multi-Factor Authentication.**  Azure AD tillhandahåller inbyggd Multi-Factor Authentication. IT-administratörer kan kräva Multi-Factor Authentication för att få åtkomst till ditt program, så att du inte behöver koda detta stöd själv. Läs mer om [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Identifiering av avvikande inloggning.**  Azure AD bearbetar mer än en miljard inloggningar per dag, samtidigt som du använder Machine Learning-algoritmer för att identifiera misstänkt aktivitet och meddela IT-administratörer om möjliga problem. Med stöd för Azure AD-inloggning får ditt program fördelen med detta skydd. Läs mer om hur du [visar Azure Active Directory åtkomst rapporten](../active-directory-view-access-usage-reports.md).

**Villkorlig åtkomst.**  Förutom Multi-Factor Authentication kan administratörer kräva att vissa villkor uppfylls innan användarna kan logga in till ditt program. Villkor som kan ställas in inkluderar IP-adressintervallet för klient enheter, medlemskap i angivna grupper och tillståndet för enheten som används för åtkomst. Läs mer om [Azure Active Directory villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Enkel utveckling

**Bransch standard protokoll.**  Microsoft strävar efter att stödja bransch standarder. Microsoft Identity Platform stöder OAuth 2,0-och OpenID Connect 1,0-protokoll som är bransch standard. Läs mer om [autentiseringsprotokoll för Microsoft Identity Platform](active-directory-v2-protocols.md).

**Bibliotek med öppen källkod.**  Microsoft tillhandahåller fullt stöd för bibliotek med öppen källkod för populära språk och plattformar för att påskynda utvecklingen. Käll koden är licensierad i Apache 2,0 och du är kostnads fri att gå tillbaka till projekten. Lär dig mer om [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Världs omfattande närvaro och hög tillgänglighet

**Azure AD distribueras i Data Center över hela världen och hanteras och övervakas dygnet runt.**  Azure AD är ett identitets hanterings system för Microsoft Azure och Office 365 och distribueras i 28-datacenter runtom i världen. Katalog data garanteras att replikeras till minst tre Data Center. Globala belastningsutjämnare säkerställer att användare kommer åt den närmaste kopian av Azure AD som innehåller deras data och dirigerar automatiskt om begär anden till andra data Center om ett problem upptäcks.

## <a name="next-steps"></a>Nästa steg

[Kom igång med att skriva kod](v2-overview.md#getting-started).

[Logga in användare med Microsoft Identity Platform](authentication-scenarios.md)

