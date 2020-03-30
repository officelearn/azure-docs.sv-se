---
title: Komma igång med att integrera Azure AD med appar | Microsoft-dokument
description: Den här artikeln är en komma igång guide för att integrera Azure Active Directory (AD) med lokala program och molnprogram.
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
ms.openlocfilehash: 89b16a8479f8975d101b8a4e26dcb1885d9730bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063388"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Integrera Azure Active Directory med program som kommer igång

Det här avsnittet sammanfattar processen för att integrera program med Azure Active Directory (AD). Vart och ett av avsnitten nedan innehåller en kort sammanfattning av ett mer detaljerat ämne så att du kan identifiera vilka delar av den här komma igång-guiden som är relevanta för dig.

Information om hur du hämtar detaljerade distributionsplaner finns i [Nästa steg](#next-steps).

## <a name="take-inventory"></a>Inventera
Innan du integrerar program med Azure AD är det viktigt att veta var du är och vart du vill gå.  Följande frågor är avsedda att hjälpa dig att tänka på ditt Azure AD-programintegreringsprojekt.

### <a name="application-inventory"></a>Applikationsinventering
* Var är alla dina program? Vem äger dem?
* Vilken typ av autentisering kräver dina program?
* Vem behöver tillgång till vilka program?
* Vill du distribuera ett nytt program?
  * Kommer du att bygga den internt och distribuera den på en Azure-beräkningsinstans?
  * Kommer du att använda en som är tillgänglig i Azure Application Gallery?

### <a name="user-and-group-inventory"></a>Användar- och grupplager
* Var finns dina användarkonton?
  * Lokalt Active Directory
  * Azure AD
  * I en separat programdatabas som du äger
  * I osanktionerade program
  * Samtliga av ovanstående
* Vilka behörigheter och rolltilldelningar har enskilda användare för närvarande? Behöver du granska deras åtkomst eller är du säker på att dina användaråtkomst- och rolltilldelningar är lämpliga nu?
* Är grupper redan etablerade i din lokala Active Directory?
  * Hur är dina grupper organiserade?
  * Vilka är gruppmedlemmarna?
  * Vilka behörigheter/rolltilldelningar har grupperna för närvarande?
* Måste du rensa användar-/gruppdatabaser innan du integrerar?  (Detta är en ganska viktig fråga. Skräpa in, skräpa ut.)

### <a name="access-management-inventory"></a>Lager för åtkomsthantering
* Hur hanterar du för närvarande användaråtkomst till program? Behöver det förändras?  Har du övervägt andra sätt att hantera åtkomst, till exempel med [RBAC?](../../role-based-access-control/role-assignments-portal.md)
* Vem behöver tillgång till vad?

Kanske du inte har svar på alla dessa frågor på framsidan men det är okej.  Den här guiden kan hjälpa dig att svara på några av dessa frågor och fatta några välgrundade beslut.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Hitta osanktionerade molnprogram med Cloud Discovery

Som nämnts ovan kan det finnas program som inte har hanterats av din organisation förrän nu.  Som en del av lagerprocessen är det möjligt att hitta osanktionerade molnprogram. Se [Konfigurera Molnidentifiering](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrera program med Azure AD
I följande artiklar beskrivs de olika sätt som program integreras med Azure AD och ger viss vägledning.

* [Bestämma vilken Active Directory som ska användas](../fundamentals/active-directory-administer.md)
* [Använda program i Azure-programgalleriet](what-is-single-sign-on.md)
* [Integrera Självstudielistan för SaaS-program](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>Autentiseringstyper
Var och en av dina program kan ha olika autentiseringskrav. Med Azure AD kan signeringscertifikat användas med program som använder SAML 2.0, WS-Federation eller OpenID Connect-protokoll samt lösenord enkel inloggning. Mer information om programautentiseringstyper för användning med Azure AD finns i [Hantera certifikat för federerad enkel inloggning i Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) och lösenord baserat enkel [inloggning](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Aktivera SSO med Azure AD-appproxy
Med Microsoft Azure AD Application Proxy kan du ge åtkomst till program som finns i ditt privata nätverk på ett säkert sätt, var som helst och på vilken enhet som helst. När du har installerat en programproxyanslutning i din miljö kan den enkelt konfigureras med Azure AD.

### <a name="integrating-custom-applications"></a>Integrera anpassade program
Om du skriver ett nytt program och vill hjälpa utvecklare att utnyttja kraften i Azure AD läser du [Vägleda utvecklare](../active-directory-applications-guiding-developers-for-lob-applications.md).

Om du vill lägga till ditt anpassade program i Azure Application Gallery läser du ["Ta med din egen app" med Azure AD-saml-konfiguration med självbetjäning](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="managing-access-to-applications"></a>Hantera åtkomst till program
I följande artiklar beskrivs hur du kan hantera åtkomst till program när de har integrerats med Azure AD med Azure AD-kopplingar och Azure AD.

* [Hantera åtkomst till appar med Azure AD](what-is-access-management.md)
* [Automatisera med Azure AD-kopplingar](../app-provisioning/user-provisioning.md)
* [Tilldela användare till ett program](../active-directory-applications-guiding-developers-assigning-users.md)
* [Tilldela grupper till ett program](../active-directory-applications-guiding-developers-assigning-groups.md)
* [Dela konton](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>Nästa steg
Om du vill ha detaljerad information kan du hämta Azure Active Directory-distributionsplaner från [GitHub](https://aka.ms/deploymentplans). För galleriprogram kan du hämta distributionsplaner för enkel inloggning, villkorlig åtkomst och användaretablering via [Azure-portalen](https://portal.azure.com). 

Så här hämtar du en distributionsplan från Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Företagsprogram** | Välj en**appdistributionsplan****Pick an App** | .

Ge feedback om distributionsplaner genom att göra [undersökningen om distributionsplanen](https://aka.ms/DeploymentPlanFeedback).
