---
title: Aktivera enkel inloggning för ditt program för flera innehavare
description: Vägledning för oberoende programvaruleverantörer om integrering med Azure active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67795185"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Aktivera enkel inloggning för ditt program med flera innehavare  

När du erbjuder ditt program för användning av andra företag via ett köp eller en prenumeration gör du ditt program tillgängligt för kunder inom sina egna Azure-klienter. Detta kallas att skapa ett program med flera innehavare. Översikt över det här konceptet finns [i Multitenant-program i Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) och [Hyresrätt i Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>Vad är enkel inloggning

Enkel inloggning (SSO) lägger till säkerhet och bekvämlighet när användare loggar in på program med hjälp av Azure Active Directory och andra identiteter. När ett program är SSO aktiverat behöver användarna inte ange separata autentiseringsuppgifter för att komma åt programmet. För en fullständig förklaring av Single sign-on. [Se Enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Varför aktivera enkel inloggning i ditt program?

Det finns många fördelar med att aktivera SSO i ditt program för flera innehavare. När du aktiverar SSO för ditt program:

* Ditt program kan visas på Azure Marketplace, där din app kan identifieras av miljontals organisationer som använder Azure Active Directory.
  * Gör det möjligt för kunder att snabbt konfigurera programmet med Azure AD.

* Ditt program kan identifieras i Office 365 App Gallery, Office 365 App Launcher och i Microsoft Search på Office.com

* Programmet kan använda MICROSOFT Graph REST API för att komma åt data som driver användarens produktivitet som är tillgänglig från Microsoft Graph.

* Du minskar supportkostnaderna genom att göra det enklare för dina kunder.
  * Programspecifik dokumentation som samförts med Azure AD-teamet för våra gemensamma kunder underlättar implementeringen.
  * Om SSO med ett klick är aktiverat behöver kundernas IT-administratörer inte lära sig hur du konfigurerar programmet för användning i organisationen.

* Du ger dina kunder möjlighet att helt hantera sina anställdas och gästidentiteters autentisering och auktorisering.

  * Placera all kontohantering och efterlevnadsansvar hos kundens ägare av dessa identiteter.

  * Ger möjlighet att aktivera eller inaktivera SSO för specifika identitetsleverantörer, grupper eller användare för att uppfylla sina affärsbehov.

* Du ökar din säljbarhet och adoptivbarhet. Många stora organisationer kräver att (eller strävar efter) att deras anställda har sömlösa SSO-upplevelser i alla program. Att göra SSO enkelt är viktigt.

* Du minskar slutanvändarens friktion, vilket kan öka slutanvändarens användning och öka dina intäkter.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Så här aktiverar du enkel inloggning i din publicerade applikation

1. [Välj rätt federationsprotokoll för ditt program för flera innehavare](isv-choose-multi-tenant-federation.md).
1. Implementera SSO i din applikation
   - Se [vägledning om autentiseringsmönster](../develop/v2-app-types.md)
   - Se [Azure active Directory-kodexempel](../develop/sample-v2-code.md) för OIDC- och OAuth-protokoll
1. [Skapa din Azure-klient och](isv-tenant-multi-tenant-app.md) testa ditt program
1. [Skapa och publicera SSO-dokumentation på din webbplats](isv-create-sso-documentation.md).
1. [Skicka in din programbeskrivning](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) och samarbeta med Microsoft för att skapa dokumentation på Microsofts webbplats.
1. [Gå med i Microsoft Partner Network (gratis) och skapa din gå till marknadsplan](https://partner.microsoft.com/en-us/explore/commercial#gtm).
