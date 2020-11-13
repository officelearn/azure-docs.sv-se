---
title: Registrera ditt program för att använda Azure Active Directory | Microsoft Docs
description: Den här artikeln innehåller rikt linjer för att integrera Azure-program med Active Directory för IT-proffs.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: da1b56e3818d2d9701ecb0252328746dc39bb260
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578322"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Utveckla branschspecifika appar för Azure Active Directory
Den här guiden ger en översikt över hur du utvecklar branschspecifika program (LoB) för Azure Active Directory (AD). Den avsedda mål gruppen är Active Directory/Microsoft 365 globala administratörer.

## <a name="overview"></a>Översikt
Att skapa program som är integrerade med Azure AD ger användare i din organisation enkel inloggning med Microsoft 365. Med programmet i Azure AD får du kontroll över autentiseringsprincip för programmet. Mer information om villkorlig åtkomst och hur du skyddar appar med Multi-Factor Authentication (MFA) finns i [Konfigurera åtkomst regler](../conditional-access/app-based-mfa.md).

Registrera ditt program för att använda Azure Active Directory. Att registrera programmet innebär att utvecklarna kan använda Azure AD för att autentisera användare och begära åtkomst till användar resurser som e-post, kalender och dokument.

Alla medlemmar i din katalog (inte gäster) kan registrera ett program, annars kallas att *skapa ett program objekt*. Om du inte kan registrera ett program innebär det att den globala administratören för din katalog har begränsat den här funktionen och du kan behöva kontakta dem för att [få rätt behörighet](https://docs.microsoft.com/azure/active-directory/roles/delegate-app-roles#assign-built-in-application-admin-roles) för att kunna registrera programmet. Om du vill veta mer om hur du begränsar användarens se [delegera program registrerings behörigheter i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/roles/delegate-app-roles#restrict-who-can-create-applications).

När du registrerar ett program kan alla användare göra följande:

* Få en identitet för deras program som Azure AD känner igen
* Hämta en eller flera hemligheter/nycklar som programmet kan använda för att autentisera sig till AD
* Anpassa programmet i Azure Portal med ett anpassat namn, en logo typ osv.
* Använd Azure AD Authorization-funktioner i appen, inklusive:

  * Rollbaserad åtkomstkontroll (RBAC)
  * Azure Active Directory som oAuth-auktoriseringsbegäran (skydda ett API som exponeras av programmet)
* Deklarera nödvändiga behörigheter som krävs för att programmet ska fungera som förväntat, inklusive:

     - App-behörigheter (endast globala administratörer). Exempel: roll medlemskap i ett annat Azure AD-program eller roll medlemskap i förhållande till en Azure-resurs, resurs grupp eller prenumeration
     - Delegerade behörigheter (alla användare). Till exempel: Azure AD, inloggning och Läs profil

> [!NOTE]
> Som standard kan alla medlemmar registrera ett program. Information om hur du begränsar behörigheter för att registrera program till särskilda medlemmar finns i [hur program läggs till i Azure AD](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Det här är vad du, den globala administratören, behöver göra för att hjälpa utvecklare att göra programmet redo för produktion:

* Konfigurera åtkomst regler (åtkomst princip/MFA)
* Konfigurera appen så att den kräver användar tilldelning och tilldela användare
* Förhindra användning av standard användar medgivande

## <a name="configure-access-rules"></a>Konfigurera åtkomst regler
Konfigurera åtkomst regler per program till dina SaaS-appar. Du kan till exempel kräva MFA eller bara tillåta åtkomst till användare i betrodda nätverk. Informationen för detta finns i dokumentet [Konfigurera åtkomst regler](../conditional-access/app-based-mfa.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Konfigurera appen så att den kräver användar tilldelning och tilldela användare
Som standard kan användare komma åt program utan att tilldelas. Men om programmet visar roller eller om du vill att programmet ska visas i användarens Mina appar, bör du kräva användar tilldelning.

Om du är en Azure AD Premium-eller EMS-prenumerant (Enterprise Mobility Suite) rekommenderar vi starkt att du använder grupper. Genom att tilldela grupper till programmet kan du delegera kontinuerlig åtkomst hantering till ägare av gruppen. Du kan skapa gruppen eller be den ansvariga parten i din organisation att skapa gruppen med hjälp av din grupp hanterings funktion.

[Tilldela användare och grupper till ett program](methods-for-assigning-users-and-groups.md)  


## <a name="suppress-user-consent"></a>Förhindra användar medgivande
Som standard går varje användare igenom en medgivande upplevelse för att logga in. Medgivande upplevelsen, som ber användarna att bevilja behörighet till ett program, kan vara samordnade för användare som inte är bekanta med att fatta sådana beslut.

För program som du litar på kan du förenkla användar upplevelsen genom att samtycka till programmet på uppdrag av din organisation.

Mer information om användar medgivande och medgivande upplevelsen i Azure finns i [integrera program med Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="related-articles"></a>Relaterade artiklar
* [Aktivera säker fjärråtkomst till lokala program med Azure AD-programproxy](application-proxy.md)
* [Hantera åtkomst till appar med Azure AD](what-is-access-management.md)

