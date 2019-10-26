---
title: Kom igång med att integrera Azure AD med appar | Microsoft Docs
description: Den här artikeln är en komma igång-guide för att integrera Azure Active Directory (AD) med lokala program och moln program.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1291db8cd895a1104183ae2b5388eb6a23305874
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895939"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Att integrera Azure Active Directory med program komma igång-guide

I det här avsnittet sammanfattas processen för att integrera program med Azure Active Directory (AD). Vart och ett av avsnitten nedan innehåller en kort sammanfattning av ett mer detaljerat avsnitt, så att du kan identifiera vilka delar av den här komma igång-guiden som är relevanta för dig.

Information om hur du hämtar djupgående distributions planer finns i [Nästa steg](#next-steps).

## <a name="take-inventory"></a>Ta inventering
Innan du integrerar program med Azure AD är det viktigt att veta var du är och var du vill gå.  Följande frågor är avsedda att hjälpa dig att tänka på ditt Azure AD Application Integration-projekt.

### <a name="application-inventory"></a>Program inventering
* Var finns alla dina program? Vem äger dem?
* Vilken typ av autentisering kräver dina program?
* Vem behöver åtkomst till vilka program?
* Vill du distribuera ett nytt program?
  * Kommer du att bygga den internt och distribuera den på en Azure Compute-instans?
  * Kommer du att använda en som är tillgänglig i Azure Application galleriet?

### <a name="user-and-group-inventory"></a>Användar-och grupp inventering
* Var finns dina användar konton?
  * Lokalt Active Directory
  * Azure AD
  * I en separat program databas som du äger
  * I program som inte är sanktionerade
  * Alla ovanstående
* Vilka behörigheter och roll tilldelningar har enskilda användare för närvarande? Behöver du granska åtkomsten eller är du säker på att användarnas åtkomst-och roll tilldelningar är lämpliga nu?
* Är grupper redan etablerade i din lokala Active Directory?
  * Hur organiseras dina grupper?
  * Vilka är grupp medlemmarna?
  * Vilka behörigheter/roll tilldelningar har grupperna för närvarande?
* Behöver du rensa användare/grupp-databaser innan du integrerar?  (Det här är en ganska viktig fråga. Skräp in, skräp ut.)

### <a name="access-management-inventory"></a>Åtkomst hanterings inventering
* Hur hanterar du användar åtkomst för program? Vill du ändra?  Har du funderat på andra sätt att hantera åtkomst, t. ex. med [RBAC](../../role-based-access-control/role-assignments-portal.md) till exempel?
* Vem behöver åtkomst till vad?

Du kanske inte har svar på alla dessa frågor fram, men det är OK.  Den här guiden hjälper dig att besvara några av dessa frågor och fatta några välgrundade beslut.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Hitta ej sanktionerade moln program med Cloud Discovery

Som nämnts ovan kan det finnas program som inte har hanterats av din organisation förrän nu.  Som en del av inventerings processen är det möjligt att hitta ej sanktionerade moln program. Se [konfigurera Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrera program med Azure AD
I följande artiklar diskuteras de olika sätten att integrera med Azure AD och ge lite vägledning.

* [Bestämma vilken Active Directory som ska användas](../fundamentals/active-directory-administer.md)
* [Använda program i Azures program Galleri](what-is-single-sign-on.md)
* [Integrerings lista för integrering av SaaS-program](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>Autentiseringstyper
Varje program kan ha olika autentiseringskrav. Med Azure AD kan du använda signerings certifikat med program som använder SAML 2,0, WS-Federation eller OpenID Connect-protokoll samt lösen ords enkel inloggning. Mer information om programautentiserings typer som används med Azure AD finns i [Hantera certifikat för federerad enkel inloggning i Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) och [lösen ords baserad enkel inloggning](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Aktivera SSO med Azure AD App proxy
Med Microsoft Azure AD Application Proxy kan du ge åtkomst till program som finns i ditt privata nätverk på ett säkert sätt, från valfri plats och på valfri enhet. När du har installerat en Application Proxy-koppling i din miljö kan den enkelt konfigureras med Azure AD.

### <a name="integrating-custom-applications"></a>Integrera anpassade program
Om du skriver ett nytt program och vill hjälpa utvecklare att använda kraften i Azure AD, se [GUID-utvecklare](../active-directory-applications-guiding-developers-for-lob-applications.md).

Om du vill lägga till ett anpassat program i Azure Application galleriet, se ["ta med din egen app" med Azure AD-tjänsten för självbetjänings-och konfiguration](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="managing-access-to-applications"></a>Hantera åtkomst till program
I följande artiklar beskrivs hur du kan hantera åtkomst till program när de har integrerats med Azure AD med hjälp av Azure AD-anslutningar och Azure AD.

* [Hantera åtkomst till appar med hjälp av Azure AD](what-is-access-management.md)
* [Automatisera med Azure AD-anslutningar](user-provisioning.md)
* [Tilldela användare till ett program](../active-directory-applications-guiding-developers-assigning-users.md)
* [Tilldela grupper till ett program](../active-directory-applications-guiding-developers-assigning-groups.md)
* [Dela konton](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>Nästa steg
För detaljerad information kan du hämta Azure Active Directory distributions planer från [GitHub](https://aka.ms/deploymentplans). För Galleri program kan du hämta distributions planer för enkel inloggning, villkorlig åtkomst och användar etablering via [Azure Portal](https://portal.azure.com). 

Så här hämtar du en distributions plan från Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **företags program** | **välj en app** | **distributions plan**.

Ge feedback om distributions planer genom att ta [undersökningen om distributions planen](https://aka.ms/DeploymentPlanFeedback).
