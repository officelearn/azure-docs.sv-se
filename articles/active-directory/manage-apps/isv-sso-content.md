---
title: Aktivera enkel inloggning för dina program med flera innehavare
description: Vägledning för oberoende programleverantörer för integrering med Azure active Directory
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
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795185"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Aktivera enkel inloggning för dina program med flera innehavare  

När du erbjuder programmet för användning av andra företag via ett köp eller prenumeration gör du ditt program tillgängliga för kunder i sina egna Azure-klienter. Det här är känt som du skapar ett program med flera innehavare. Översikt över detta begrepp finns i [Multitenant-program i Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) och [innehavare i Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>Vad är enkel inloggning

Lägger till säkerhet och bekvämlighet enkel inloggning (SSO) när användare loggar in på program med hjälp av Azure Active Directory och andra identiteter. När ett program är enkel inloggning aktiverat kan behöver användarna inte ange separata autentiseringsuppgifter för att komma åt programmet. En fullständig förklaring av enkel inloggning. [Se enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Varför ska du aktivera enkel inloggning i ditt program?

Det finns många fördelar med att aktivera enkel inloggning i ditt program med flera innehavare. När du aktiverar enkel inloggning för ditt program:

* Programmet kan visas i Azure Marketplace, där din app är kan identifieras av miljontals organisationer som använder Azure Active Directory.
  * Ger kunder möjlighet att snabbt konfigurera programmet med Azure AD.

* Programmet kan vara synliga i Office 365-App-galleriet, Office 365-Appstartaren och inom Microsoft Search på Office.com

* Ditt program kan använda Microsoft Graph REST API för att komma åt data som ökar användarproduktiviteten som är tillgänglig från Microsoft Graph.

* Du kan minska supportkostnaderna genom att göra det enklare för kunderna.
  * Dokumentation om programspecifika coproduced med Azure AD-teamet för våra gemensamma kunder övergångar införande.
  * Om en enkel inloggning är aktiverad, inte dina kunders IT-administratörer att lära dig hur du konfigurerar programmet för användning i deras organisation.

* Du ger kunderna möjlighet att helt hantera sina anställda och Gäst identiteter autentisering och auktorisering.

  * Placera alla konto-hantering och kompatibilitet ansvar med kunden ägaren av dessa identiteter.

  * Ger möjlighet att aktivera eller inaktivera enkel inloggning för specifika identitetsleverantörer, grupper eller användare att uppfylla sina affärsbehov.

* Du kan öka dina likviditet och adoptability. Stora organisationer kräver att (eller strävar efter att) sina anställda har sömlös SSO-upplevelse i alla program. Det är viktigt att göra SSO enkelt.

* Du kan minska friktionen för slutanvändare, vilket kan öka slutanvändarens användning och öka din vinst.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Så här aktiverar du enkel inloggning i ditt publicerade program

1. [Välj rätt federation-protokollet för ditt program för flera innehavare](isv-choose-multi-tenant-federation.md).
1. Implementera enkel inloggning i ditt program
   - Se [vägledning för autentiseringsmönster](../develop/v2-app-types.md)
   - Se [Azure active Directory-kodexempel](../develop/sample-v2-code.md) för OIDC- och OAuth-protokoll
1. [Skapa din Azure-klient](isv-tenant-multi-tenant-app.md) och testa ditt program
1. [Skapa och publicera SSO-dokumentationen på webbplatsen](isv-create-sso-documentation.md).
1. [Skickar din program-registrering](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) och partner med Microsoft för att skapa dokumentation på Microsofts webbplats.
1. [Ansluta till Microsoft Partner Network (kostnadsfri) och skapa din gå till marknaden plan](https://partner.microsoft.com/en-us/explore/commercial#gtm).
