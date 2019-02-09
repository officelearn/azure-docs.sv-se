---
title: Kom igång med att integrera Azure AD med appar | Microsoft Docs
description: Den här artikeln är en komma igång-guide för att integrera Azure Active Directory (AD) med dina lokala program och molnprogram.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: celested
ms.reviewer: asteen
ms.openlocfilehash: a412627378d574e28cc1a41fa0e5f9059ab62761
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960708"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Integrera Azure Active Directory med komma igång-guide

Det här avsnittet sammanfattar processen för att integrera program med Azure Active Directory (AD). Vart och ett av avsnitten nedan innehåller en kort sammanfattning av en mer detaljerad avsnittet så att du kan identifiera vilka delar av den här komma igång-guiden som är relevanta för dig.

För att ladda ned djupgående distribution planer [nästa steg](#next-steps).

## <a name="take-inventory"></a>Inventera
Det är viktigt att veta var du befinner dig och där du vill gå innan integrera program med Azure AD.  Följande frågor är avsedda att hjälpa dig att tänka på det Azure AD-integrering projektet.

### <a name="application-inventory"></a>Programinventering
* Var finns alla dina program? Vem som äger dem?
* Vilken typ av autentisering kräver dina program?
* Vilka som behöver åtkomst till vilka program?
* Vill du distribuera ett nytt program?
  * Du skapar den interna och distribuera den på en Azure compute-instans?
  * Ska du använda ett som är tillgängliga i Azure-Programgalleriet?

### <a name="user-and-group-inventory"></a>Användar- och programvaruinventering
* Där finns dina användarkonton?
  * Lokalt Active Directory
  * Azure AD
  * I en separat program-databas som du äger
  * I osanktionerade program
  * Inget av alternativen ovan
* Vilka behörigheter och rolltilldelningar enskilda användare har för närvarande? Behöver du granska åtkomsten eller är du säker på att dina användare åtkomst och rollen tilldelningar är lämpliga nu?
* Grupper redan är etablerade i din lokala Active Directory?
  * Hur organiseras grupper?
  * Vilka är medlemmarna i gruppen?
  * Vilka behörigheter/rolltilldelningar grupperna har för närvarande?
* Du måste rensa upp användare/grupp databaser innan du integrera?  (Detta är en ganska viktigt fråga. Skräpinsamling i skräpinsamling ut).

### <a name="access-management-inventory"></a>Inventering av åtkomst
* Hur hanterar du åtkomst till program just nu? Behöver som ändrar?  Har du funderat andra sätt att hantera åtkomst, till exempel med [RBAC](../../role-based-access-control/role-assignments-portal.md) till exempel?
* Vilka som behöver åtkomst till vad?

Du kanske har inte svar på alla dessa frågor direkt, men det är okej.  Den här guiden hjälper dig att besvara några av dessa frågor och göra vissa välgrundade beslut.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Hitta osanktionerade molnprogram med Cloud Discovery

Som nämnts ovan är kan det finnas program som inte har hanterats av din organisation fram till nu.  Som en del av processen för inventering går det att hitta osanktionerade molnprogram. Se [konfigurera Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrera program med Azure AD
I följande artiklar beskrivs de olika sätten program integrera med Azure AD och ger lite vägledning.

* [Bestämma vilka Active Directory för att använda](../fundamentals/active-directory-administer.md)
* [Med hjälp av program i Azure-programgalleriet](what-is-single-sign-on.md)
* [Integrera SaaS programlistan självstudier](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>Autentiseringstyper
Var och en av dina program kan ha olika autentiseringskrav. Med Azure AD användas signerar certifikat med program som använder SAML 2.0, WS-Federation, eller OpenID Connect-protokoll samt lösenord för enkel inloggning. Mer information om programmet autentiseringstyper för användning med Azure AD finns [hantera certifikat för federerad enkel inloggning i Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) och [lösenord baserat enkel inloggning på](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Aktivera enkel inloggning med Azure AD App Proxy
Med Microsoft Azure AD Application Proxy kan du ge åtkomst till program som finns i ditt privata nätverk på ett säkert sätt, från var som helst och på alla enheter. När du har installerat en application proxy connector i din miljö kan konfigureras det enkelt med Azure AD.

### <a name="integrating-custom-applications"></a>Integrera anpassade program
Om du skriver ett nytt program och vill att hjälpa utvecklare utnyttjar Azure AD finns i [Guiding utvecklare](../active-directory-applications-guiding-developers-for-lob-applications.md).

Om du vill lägga till ditt anpassade program till Azure-Programgalleriet [”ta med din egen app” med Azure AD Self-Service SAML-konfiguration](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="managing-access-to-applications"></a>Hantera åtkomst till program
I följande artiklar beskrivs sätt som du kan hantera åtkomst till program när de har integrerats med Azure AD med Azure AD-Anslutningsappar och Azure AD.

* [Hantera åtkomst till appar med hjälp av Azure AD](what-is-access-management.md)
* [Automatisera med Azure AD-Anslutningsappar](user-provisioning.md)
* [Tilldela användare till ett program](../active-directory-applications-guiding-developers-assigning-users.md)
* [Tilldela grupper till ett program](../active-directory-applications-guiding-developers-assigning-groups.md)
* [Dela konton](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>Nästa steg
Detaljerad information kan du hämta planer för Azure Active Directory-distribution från [GitHub](https://aka.ms/deploymentplans). För galleriprogram, du kan ladda ned distribution planer för enkel inloggning, villkorlig åtkomst och användaretablering via den [Azure-portalen](https://portal.azure.com). 

Så här hämtar du en distributionsplan från Azure portal:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **företagsprogram** | **Välj en App** | **distributionsplan**.

Ge feedback på planer för distributionen genom att utföra den [distribution plan undersökningen](https://aka.ms/DeploymentPlanFeedback).
