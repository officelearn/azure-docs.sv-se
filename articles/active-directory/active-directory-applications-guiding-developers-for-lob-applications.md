---
title: "Utveckla appar för Azure AD | Microsoft Docs"
description: "Den här artikeln innehåller skrivs för IT-proffs riktlinjer för integrera Azure-program med Active Directory."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: dd69f2bc-37c5-457c-857d-27acb84267fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6b119be9c06d8c1ccc8e747168429e6c2d2e7a8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Utveckla av branschspecifika appar för Azure Active Directory
Den här guiden innehåller en översikt över utvecklar line-of-business (LoB)-program för Azure Active Directory (AD). Målgruppen är globala administratörer för Active Directory/Office 365.

## <a name="overview"></a>Översikt
Skapa program som är integrerade med Azure AD ger användare i din organisation enkel inloggning med Office 365. Med programmet i Azure AD-ger dig kontroll över autentiseringsprincipen för programmet. Mer information om hur villkorlig åtkomst och skydda appar med multifaktorautentisering (MFA) Se [konfigurera åtkomstregler](active-directory-conditional-access-azuread-connected-apps.md).

Registrera ditt program att använda Azure Active Directory. Registrera programmet innebär att utvecklarna kan använda Azure AD för att autentisera användare och begära åtkomst till resurser som e-post, kalender och dokument för användare.

Alla medlemmar i din katalog (inte gäster) kan registrera ett program, även kallat *skapar ett programobjekt*.

Registrera ett program kan alla användare att göra följande:

* Hämta en identitet för de program som kan identifieras av Azure AD
* Hämta en eller flera hemligheter/nycklar som programmet kan använda för att autentisera sig till AD
* Lägga till programmet i Azure-portalen med ett anpassat namn, logotyp och så vidare.
* Gäller Azure AD-funktioner för auktorisering för appen, inklusive:

  * Rollbaserad åtkomstkontroll (RBAC)
  * Azure Active Directory som server för oAuth-auktorisering (skydda en API som exponeras av programmet)
* Deklarera behörigheter som krävs för nödvändiga för att programmet ska fungera som förväntat, inklusive:

      - Appbehörigheter (endast globala administratörer). Till exempel: medlemskap i en annan Azure AD-program eller roll medlemskap i förhållande till en Azure resurs, resursgrupp eller prenumeration
      - Delegerad behörighet (alla användare). Till exempel: Azure AD-inloggning och Läs profil

> [!NOTE]
> Som standard kan alla medlemmar registrera ett program. Information om hur du begränsar behörigheter för att registrera program till specifika medlemmar finns [hur program läggs till Azure AD](develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Här är vad du den globala administratören behöver för att hjälpa utvecklare att förbereda sina program för produktion:

* Konfigurera åtkomstregler (åtkomst princip/MFA)
* Konfigurera appen för att kräva Användartilldelning och tilldela användare
* Ignorera standardgränssnittet för godkännande

## <a name="configure-access-rules"></a>Konfigurera åtkomstregler
Konfigurera regler för programspecifika åtkomst till SaaS-appar. Du kan till exempel kräva MFA eller bara tillåta åtkomst till användare i betrodda nätverk. Information om detta finns i dokumentet [konfigurera åtkomstregler](active-directory-conditional-access-azuread-connected-apps.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Konfigurera appen för att kräva Användartilldelning och tilldela användare
Som standard kan användare komma åt program utan att ha tilldelats. Om programmet visar roller eller om du vill att programmet ska visas på åtkomstpanelen för en användare, bör du kräva Användartilldelning.

[Kräv användartilldelning](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Om du är en Azure AD Premium eller Enterprise Mobility Suite (EMS) prenumerant, rekommenderar vi starkt med hjälp av grupper. Tilldela grupper till programmet kan du delegera hanteringen av pågående åtkomst till ägare av gruppen. Du kan skapa gruppen eller be ansvarig part i din organisation att skapa gruppen med din grupp management anläggning.

[Tilldela användare till ett program](active-directory-applications-guiding-developers-assigning-users.md)  
[Tilldela grupper till ett program](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>Ignorera tillstånd
Varje användare genomgår en medgivande upplevelse att logga in som standard. Medgivande upplevelse, genom att be användarna att tilldela behörigheter till ett program kan vara förvirrande för användare som inte är bekant med att fatta detta beslut.

För program som du litar på, kan du förenkla användarupplevelsen av principer för program uppdrag av din organisation.

Mer information om tillstånd och samtycke uppstår i Azure finns [integrera program med Azure Active Directory](active-directory-integrating-applications.md).

## <a name="related-articles"></a>Relaterade artiklar
* [Aktivera säker fjärråtkomst till lokala program med Azure AD Application Proxy](active-directory-application-proxy-get-started.md)
* [Förhandsgranska Azure villkorlig åtkomst för SaaS-appar](active-directory-conditional-access-azuread-connected-apps.md)
* [Hantera åtkomst till appar med Azure AD](active-directory-managing-access-to-apps.md)
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
