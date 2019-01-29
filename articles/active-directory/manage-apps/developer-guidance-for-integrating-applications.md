---
title: Registrera ditt program att använda Azure Active Directory | Microsoft Docs
description: Avsedd för IT-proffs, innehåller den här artikeln riktlinjer för att integrera Azure-program med Active Directory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 10/30/2018
ms.author: barbkess
ms.custom: seohack1
ms.openlocfilehash: d307b0f5f855b48d13b3c302a62f154b9df7aa48
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155093"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Utveckla line-of-business-appar för Azure Active Directory
Den här guiden innehåller en översikt över utveckling line-of-business (LoB)-program för Azure Active Directory (AD). Målgruppen är globala administratörer för Active Directory/Office 365.

## <a name="overview"></a>Översikt
Att skapa program som är integrerade med Azure AD ger användare i din organisation enkel inloggning med Office 365. Att låta programmet i Azure AD ger dig kontroll över autentiseringsprincip för programmet. Mer information om villkorlig åtkomst och hur du skyddar appar med multifaktorautentisering (MFA) i avsnittet [konfigurera åtkomstregler](../conditional-access/app-based-mfa.md).

Registrera din app att använda Azure Active Directory. Registrera programmet innebär att utvecklarna kan använda Azure AD för att autentisera användare och begära åtkomst till användarresurser, till exempel e-post, kalender och dokument.

Alla medlemmar i din katalog (inte gäster) kan registrera ett program, även kallat *skapar ett programobjekt*.

Registrerar ett program kan alla användare att göra följande:

* Hämta en identitet för sina program som Azure AD upptäcker
* Hämta en eller flera hemligheter/nycklar som programmet kan använda för att autentisera sig själv till AD
* Varumärke programmet i Azure portal med ett anpassat namn, logotyp, osv.
* Gäller Azure AD-funktioner för auktorisering för appen, inklusive:

  * Rollbaserad åtkomstkontroll (RBAC)
  * Azure Active Directory som oAuth auktoriseringsservern (skydda en API som visas av programmet)
* Deklarera behörighet krävs för programmet ska fungera som förväntat, inklusive:

     - App-behörigheter (endast globala administratörer). Exempel: Medlemskap i en annan Azure AD-program eller roll medlemskap i förhållande till en Azure-resurs, resursgrupp, eller en prenumeration
     - Delegerade behörigheter (alla användare). Exempel: Azure AD-inloggningen, och Läs profil

> [!NOTE]
> Som standard kan alla medlemmar registrera ett program. Läs hur du begränsar behörigheter för att registrera program till särskilda medlemmar i [hur program läggs till Azure AD](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Här är vad du som global administratör, behöver göra för att hjälpa utvecklare göra sina program som är redo för produktion:

* Konfigurera regler för åtkomst (åtkomst princip/MFA)
* Konfigurera appen så att den kräver Användartilldelning och tilldela användare
* Ignorera standardgränssnittet för medgivande

## <a name="configure-access-rules"></a>Konfigurera regler för åtkomst
Konfigurera regler för programspecifika åtkomst till SaaS-appar. Du kan till exempel kräva MFA eller bara tillåta åtkomst till användare på betrodda nätverk. Information om detta finns i dokumentet [konfigurera åtkomstregler](../conditional-access/app-based-mfa.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Konfigurera appen så att den kräver Användartilldelning och tilldela användare
Användarna kan nå program utan att de tilldelas som standard. Om programmet exponerar roller eller om du vill att programmet ska visas på åtkomstpanelen för en användare, bör du kräva Användartilldelning.

Om du abonnerar på Azure AD Premium eller Enterprise Mobility Suite (EMS), rekommenderar vi starkt med hjälp av grupper. Tilldela grupper till programmet kan du delegera pågående hantering till ägare av gruppen. Du kan skapa gruppen eller be ansvarig part i din organisation att skapa gruppen med din grupp management anläggning.

[Tilldela användare och grupper till ett program](methods-for-assigning-users-and-groups.md)  


## <a name="suppress-user-consent"></a>Ignorera användargodkännande
Varje användare går igenom en godkännande-upplevelse för att logga in som standard. Samtycke upplevelsen, genom att be användarna att bevilja behörigheter till ett program kan vara förvirrande för användare som inte är bekant med sådana beslutsfattande.

För program som du litar på, kan du förenkla användarupplevelsen genom principer för programmet för din organisation.

Mer information om användarens medgivande och samtycke upplevelse i Azure, finns i [integrera program med Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="related-articles"></a>Relaterade artiklar
* [Aktivera säker fjärråtkomst till lokala program med Azure AD Application Proxy](application-proxy.md)
* [Hantera åtkomst till appar med Azure AD](what-is-access-management.md)

