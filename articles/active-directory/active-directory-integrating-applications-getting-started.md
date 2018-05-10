---
title: Komma igång med att integrera Azure AD med appar | Microsoft Docs
description: Den här artikeln är en Kom igång-guide för att integrera Azure Active Directory (AD) med lokala program och molnprogram.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: b8b17e731757c2b17f312fa86208254de7ab104c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Integrera Azure Active Directory med program komma igång
## <a name="overview"></a>Översikt
Det här avsnittet är avsett att ge dig en översikt över för att integrera program med Azure Active Directory (AD). Varje avsnitt nedan innehåller en kort sammanfattning av en mer detaljerad avsnittet så att du kan identifiera vilka delar av den här Kom igång-guide är relevanta för dig.  Följ länkarna för en mer grundlig genomgång på varje område.

## <a name="before-you-begin-take-inventory"></a>Innan du kan inventera
Det är viktigt att veta var du är och där du vill gå innan du hoppa i att integrera program med Azure AD.  Följande frågor är avsedda att hjälpa dig att tänka på att Azure AD application integration-projekt.

### <a name="application-inventory"></a>Applikationsinventering
* Var finns alla dina program? Vem äger dem?
* Vilken typ av autentisering kräver dina program?
* Som behöver åtkomst till vilka program?
* Vill du distribuera ett nytt program?
  * Du skapar den interna och distribuera den på en instans av Azure-beräkning?
  * Ska du använda ett som är tillgängliga i Azure-galleriet program?

### <a name="user-and-group-inventory"></a>Användar- och programvaruinventering
* Där finns dina användarkonton?
  * Lokalt Active Directory
  * Azure AD
  * I en separat program-databas som du äger
  * I ej sanktionerade program
  * Samtliga av ovanstående
* Vilka behörigheter och rolltilldelningar enskilda användare för närvarande har? Behöver du granska deras åtkomst eller är du säker på att dina användare åtkomst och rollen tilldelningar är lämpliga nu?
* Grupper redan upprättas i din lokala Active Directory?
  * Hur organiseras grupper?
  * Vilka är gruppmedlemmarna?
  * Vilka behörigheter/rolltilldelningar grupperna för närvarande har?
* Du måste rensa användare/grupp databaser före integrera?  (Detta är ett ganska viktig fråga. Skräpinsamling i skräp ut).

### <a name="access-management-inventory"></a>Access management inventering
* Hur hanterar du användaråtkomst till program just nu? Behöver som ändrar?  Har du funderat andra sätt att hantera åtkomst, som med [RBAC](../role-based-access-control/role-assignments-portal.md) till exempel?
* Som behöver åtkomst till vad?

Kanske du har inte svaren på alla frågor direkt, men det är OK.  Den här guiden hjälper dig besvara vissa av dessa frågor och göra vissa välgrundade beslut.

## <a name="prerequisites"></a>Förutsättningar
* En Azure-prenumeration och en Azure Active Directory-katalog.  Om du inte redan har en Azure-prenumeration, kan du prova Azure kostnadsfritt i 30 dagar. [Prova!](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Programmet integrering med Azure AD
### <a name="finding-unsanctioned-cloud-applications-with-cloud-app-discovery"></a>Hitta ej sanktionerad molnprogram med Cloud App Discovery
Som nämnts ovan är kan det finnas program som inte har hanterats av din organisation fram till nu.  Som en del av processen inventory går det att hitta ej sanktionerade molnappar. Se [hitta ej sanktionerade molnappar med Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).

### <a name="authentication-types"></a>Typer av autentisering
Var och en av dina program kan ha olika krav. Med Azure AD, kan signera certifikat användas med program som använder SAML 2.0, WS-Federation, eller OpenID Connect protokoll samt lösenordet enkel inloggning. Mer information om programmet autentiseringstyper för användning med Azure AD finns [hantera certifikat för federerad enkel inloggning i Azure Active Directory](active-directory-sso-certs.md) och [lösenord utifrån för enkel inloggning](active-directory-appssoaccess-whatis.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Aktivera enkel inloggning med Azure AD App-Proxy
Du kan ge åtkomst till program som finns i ditt privata nätverk på ett säkert sätt, från var som helst och på alla enheter med Microsoft Azure AD Application Proxy. När du har installerat en application proxy connector i din miljö, kan den enkelt konfigureras med Azure AD.

### <a name="integrating-applications-with-azure-ad"></a>Integrera program med Azure AD
I följande artiklar beskrivs olika sätt program integrera med Azure AD och innehåller vägledning.

* [När du fastställer vilka Active Directory för att använda](active-directory-administer.md)
* [Med hjälp av program i Azure-programgalleriet](active-directory-appssoaccess-whatis.md)
* [Integrera SaaS-program självstudier lista](active-directory-saas-tutorial-list.md)

## <a name="managing-access-to-applications"></a>Hantera åtkomst till program
Följande artiklar beskriver hur du kan hantera åtkomst till program när de har integrerats med Azure AD med hjälp av Azure AD-anslutningar och Azure AD.

* [Hantera åtkomst till appar med Azure AD](active-directory-managing-access-to-apps.md)
* [Automatisera med Azure AD-kopplingar](active-directory-saas-app-provisioning.md)
* [Tilldela användare till ett program](active-directory-applications-guiding-developers-assigning-users.md)
* [Tilldela grupper till ett program](active-directory-applications-guiding-developers-assigning-groups.md)
* [Dela konton](active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>Integrera anpassade program
Om du skriver ett nytt program och vill utnyttja kraften Azure AD hjälpa utvecklare finns [Guiding utvecklare](active-directory-applications-guiding-developers-for-lob-applications.md).

Om du vill lägga till anpassade program till Azure-Programgalleriet [”anpassa din egen app” Azure AD Self-Service SAML-konfiguration](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="see-also"></a>Se också
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)

