---
title: Aktivera enkel inloggning för ditt program för flera innehavare
description: Vägledning för oberoende program varu leverantörer för integrering med Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26fa2b82b89c7b9ce1e05062718f2c4d0c93197c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763286"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Aktivera enkel inloggning för ditt program med flera klienter  

När du erbjuder ditt program för användning av andra företag via ett köp eller en prenumeration gör du ditt program tillgängligt för kunder i sina egna Azure-klienter. Detta kallas att skapa ett program med flera innehavare. En översikt över det här konceptet finns i [program med flera klient organisationer i Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) och [innehav i Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>Vad är enkel inloggning

Enkel inloggning (SSO) lägger till säkerhet och bekvämlighet när användare loggar in på program med hjälp av Azure Active Directory och andra identiteter. När ett program är SSO-aktiverat behöver användarna inte ange separata autentiseringsuppgifter för att få åtkomst till programmet. En fullständig förklaring av enkel inloggning. [Se enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Varför ska jag aktivera enkel inloggning i ditt program?

Det finns många fördelar med att aktivera SSO i ditt program för flera innehavare. När du aktiverar SSO för ditt program:

* Ditt program kan visas på Azure Marketplace, där din app kan upptäckas av miljon tals organisationer som använder Azure Active Directory.
  * Gör det möjligt för kunder att snabbt konfigurera programmet med Azure AD.

* Ditt program kan vara synligt i Office 365 App Gallery, Office 365-appens start och inom Microsoft Search på Office.com

* Ditt program kan använda Microsoft Graph REST API för att komma åt data som driver användar produktivitet som är tillgängligt från Microsoft Graph.

* Du minskar support kostnaderna genom att göra det enklare för dina kunder.
  * Programspecifik dokumentation som skapats med Azure AD-teamet för våra ömsesidiga kunder underlättar implementeringen.
  * Om enkel inloggning är aktive rad behöver kundernas IT-administratörer inte lära sig hur man konfigurerar programmet för användning i organisationen.

* Du ger kunderna möjlighet att fullständigt hantera sin autentisering och auktorisering av personal identiteter.

  * Placera alla konto hanterings-och efterlevnads ansvar med kundens ägare av dessa identiteter.

  * Tillhandahålla möjlighet att aktivera eller inaktivera SSO för specifika identitets leverantörer, grupper eller användare för att uppfylla företagets behov.

* Du ökar din marknadsmässighet och dina antagande. Många stora organisationer kräver att (eller strävar efter till) sina anställda har sömlös SSO-upplevelse i alla program. Enkel inloggning är viktigt.

* Du minskar friktionen för slutanvändare, vilket kan öka slut användar användningen och öka intäkterna.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Så här aktiverar du enkel inloggning i ditt publicerade program

1. [Välj rätt Federations protokoll för ditt program för flera innehavare](isv-choose-multi-tenant-federation.md).
1. Implementera SSO i ditt program
   - Se [rikt linjer för autentiserings mönster](../develop/v2-app-types.md)
   - Se [kod exempel för Azure Active Directory](../develop/sample-v2-code.md) för OIDC-och OAuth-protokoll
1. [Skapa din Azure-klient](isv-tenant-multi-tenant-app.md) och testa ditt program
1. [Skapa och publicera SSO-dokumentation på din webbplats](isv-create-sso-documentation.md).
1. [Skicka in din program lista](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) och partner med Microsoft för att skapa dokumentation på Microsofts webbplats.
1. [Gå med i Microsoft Partner Network (kostnads fri) och skapa en marknads plan](https://partner.microsoft.com/en-us/explore/commercial#gtm).
