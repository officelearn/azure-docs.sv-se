---
title: Registrera programmet för att använda Azure Active Directory | Microsoft-dokument
description: Den här artikeln är skriven för IT Pro och innehåller riktlinjer för hur du integrerar Azure-program med Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 10/30/2018
ms.author: mimart
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba54f8042c20a00f8d559ddce28e007a93afaace
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108286"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Utveckla branschappar för Azure Active Directory
Den här guiden innehåller en översikt över hur du utvecklar LoB-program (Line-of-Business) för Azure Active Directory (AD). Den avsedda målgruppen är globala Active Directory/Office 365-administratörer.

## <a name="overview"></a>Översikt
Om du skapar program som är integrerade med Azure AD får användare i organisationen enkel inloggning med Office 365. Att ha programmet i Azure AD ger dig kontroll över autentiseringsprincipen för programmet. Mer information om villkorlig åtkomst och hur du skyddar appar med MFA (Multifaktorautentisering) finns [i Konfigurera åtkomstregler](../conditional-access/app-based-mfa.md).

Registrera programmet för att använda Azure Active Directory. Registrering av programmet innebär att dina utvecklare kan använda Azure AD för att autentisera användare och begära åtkomst till användarresurser som e-post, kalender och dokument.

Alla medlemmar i din katalog (inte gäster) kan registrera ett program, annars känt som *att skapa ett programobjekt*.

Om du registrerar ett program kan alla användare göra följande:

* Skaffa en identitet för sitt program som Azure AD känner igen
* Få en eller flera hemligheter/nycklar som programmet kan använda för att autentisera sig själv till AD
* Brand programmet i Azure-portalen med ett eget namn, logotyp, etc.
* Använd Azure AD-auktoriseringsfunktioner i sin app, inklusive:

  * Rollbaserad åtkomstkontroll (RBAC)
  * Azure Active Directory som oAuth auktoriseringsserver (skydda ett API som exponeras av programmet)
* Deklarera nödvändiga behörigheter som krävs för att programmet ska fungera som förväntat, inklusive:

     - Appbehörigheter (endast globala administratörer). Till exempel: Rollmedlemskap i ett annat Azure AD-program eller rollmedlemskap i förhållande till en Azure-resurs, resursgrupp eller prenumeration
     - Delegerade behörigheter (alla användare). Till exempel: Azure AD, Inloggning och Läsprofil

> [!NOTE]
> Som standard kan alla medlemmar registrera ett program. Mer information om hur du begränsar behörigheter för att registrera program till specifika medlemmar finns i [Hur program läggs till i Azure AD](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Det här behöver du, den globala administratören, göra för att hjälpa utvecklare att göra sina program redo för produktion:

* Konfigurera åtkomstregler (åtkomstprincip/MFA)
* Konfigurera appen så att den kräver användartilldelning och tilldelar användare
* Undertrycka standardupplevelsen för användarsamtycke

## <a name="configure-access-rules"></a>Konfigurera åtkomstregler
Konfigurera åtkomstregler per program till dina SaaS-appar. Du kan till exempel kräva MFA eller bara tillåta åtkomst till användare i betrodda nätverk. Informationen för detta är tillgänglig i dokumentet [Konfigurera åtkomstregler](../conditional-access/app-based-mfa.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Konfigurera appen så att den kräver användartilldelning och tilldelar användare
Som standard kan användare komma åt program utan att tilldelas. Om programmet exponerar roller eller om du vill att programmet ska visas på en användares åtkomstpanel bör du dock kräva användartilldelning.

Om du prenumererar på Azure AD Premium eller Enterprise Mobility Suite (EMS) rekommenderar vi starkt att du använder grupper. Genom att tilldela grupper till programmet kan du delegera löpande åtkomsthantering till gruppens ägare. Du kan skapa gruppen eller be verksamhetsutövarna i organisationen att skapa gruppen med hjälp av din grupphanteringsanläggning.

[Tilldela användare och grupper till ett program](methods-for-assigning-users-and-groups.md)  


## <a name="suppress-user-consent"></a>Undertrycka användarens medgivande
Som standard går varje användare igenom en medgivandeupplevelse för att logga in. Samtyckesupplevelsen, där användarna uppmanas att bevilja behörigheter till ett program, kan vara oroande för användare som inte känner till att fatta sådana beslut.

För program som du litar på kan du förenkla användarupplevelsen genom att godkänna programmet för din organisations räkning.

Mer information om användarens medgivande och medgivandeupplevelsen i Azure finns i [Integrera program med Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="related-articles"></a>Relaterade artiklar
* [Aktivera säker fjärråtkomst till lokala program med Azure AD Application Proxy](application-proxy.md)
* [Hantera åtkomst till appar med Azure AD](what-is-access-management.md)

