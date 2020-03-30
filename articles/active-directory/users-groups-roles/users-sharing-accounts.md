---
title: Dela konton och autentiseringsuppgifter – Azure Active Directory | Microsoft-dokument
description: Beskriver hur Azure Active Directory gör det möjligt för organisationer att på ett säkert sätt dela konton för lokala appar och molntjänster för konsumenter.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 987c3ee7c90eb0bb793b96eb2771efbb258f16a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565510"
---
# <a name="sharing-accounts-with-azure-ad"></a>Dela konton med Azure AD

## <a name="overview"></a>Översikt

Ibland måste organisationer använda ett enda användarnamn och lösenord för flera personer, vilket vanligtvis sker i två fall:

* När du öppnar program som kräver en unik inloggning och lösenord för varje användare, oavsett om det är lokala appar eller molntjänster för konsumenter (till exempel företagskonton för sociala medier).
* När du skapar miljöer med flera användare. Du kan ha ett enda lokalt konto som har förhöjda privilegier och används för att utföra grundläggande inställningar, administration och återställningsaktiviteter. Det lokala kontot för den globala administratören för Office 365 eller rotkontot i Salesforce.

Traditionellt delas dessa konton genom att distribuera autentiseringsuppgifterna (användarnamn och lösenord) till rätt personer eller lagra dem på en delad plats där flera betrodda agenter kan komma åt dem.

Den traditionella delningsmodellen har flera nackdelar:

* För att aktivera åtkomst till nya program måste du distribuera autentiseringsuppgifter till alla som behöver åtkomst.
* Varje delat program kan kräva sin egen unika uppsättning delade autentiseringsuppgifter, vilket kräver att användarna kommer ihåg flera uppsättningar autentiseringsuppgifter. När användare måste komma ihåg många referenser, ökar risken att de tar till riskfyllda metoder. (till exempel skriva ner lösenord).
* Du kan inte avgöra vem som har åtkomst till ett program.
* Du kan inte se vem som har *åtkomst till* ett program.
* När du vill ta bort åtkomsten till ett program måste du uppdatera autentiseringsuppgifterna och distribuera dem till alla som behöver åtkomst till programmet.

## <a name="azure-active-directory-account-sharing"></a>Azure Active Directory-kontodelning

Azure AD tillhandahåller en ny metod för att använda delade konton som eliminerar dessa nackdelar.

Azure AD-administratören konfigurerar vilka program en användare kan komma åt med hjälp av åtkomstpanelen och välja vilken typ av enkel inloggning som passar bäst för det programmet. En av dessa typer, *lösenordsbaserad enkel inloggning,* låter Azure AD fungera som ett slags "mäklare" under inloggningsprocessen för den appen.

Användare loggar in en gång med sitt organisationskonto. Det här kontot är det som de regelbundet använder för att komma åt sin stationära eller e-post. De kan bara identifiera och komma åt de program som de har tilldelats. Med delade konton kan den här listan med program innehålla valfritt antal delade autentiseringsuppgifter. Slutanvändaren behöver inte komma ihåg eller skriva ner de olika konton som de kanske använder.

Delade konton ökar inte bara tillsynen och förbättrar användbarheten, de ökar också din säkerhet. Användare med behörighet att använda autentiseringsuppgifterna ser inte det delade lösenordet, utan får i stället behörighet att använda lösenordet som en del av ett iscensatt autentiseringsflöde. Dessutom ger vissa lösenord SSO-program dig möjlighet att använda Azure AD för att regelbundet rulla över (uppdatera) lösenord. Systemet använder stora, komplexa lösenord, vilket ökar kontosäkerheten. Administratören kan enkelt bevilja eller återkalla åtkomst till ett program, veta vem som har åtkomst till kontot och vem som har åtkomst till det tidigare.

Azure AD stöder delade konton för alla EMS-licenser (Enterprise Mobility Suite) eller Azure AD Premium-licensplan, för alla typer av lösenordsbaserade inloggningsprogram. Du kan dela konton för tusentals förintegrerade program i programgalleriet och lägga till ditt eget lösenordsautentiseringsprogram med [anpassade SSO-appar](../manage-apps/configure-single-sign-on-non-gallery-applications.md).

Azure AD-funktioner som aktiverar kontodelning inkluderar:

* [Lösenord enkel inloggning](../manage-apps/what-is-single-sign-on.md#password-based-sso)
* Lösenord enkel inloggning agent
* [Grupptilldelning](groups-self-service-management.md)
* Appar med anpassat lösenord
* [Instrumentpanelen/rapporterna för appanvändning](../active-directory-passwords-get-insights.md)
* Åtkomstportaler för slutanvändare
* [Appproxy](../manage-apps/application-proxy.md)
* [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Dela ett konto

Om du vill använda Azure AD för att dela ett konto måste du:

* Lägga till ett [programappgalleri](https://azure.microsoft.com/marketplace/active-directory/) eller [ett anpassat program](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* Konfigurera programmet för lösenord Enkel inloggning (SSO)
* Använd [gruppbaserad tilldelning](groups-saasapps.md) och välj alternativet för att ange en delad autentiseringsuppgift

Du kan också göra ditt delade konto säkrare med MFA (Multi-Factor Authentication) (läs mer om [hur du skyddar program med Azure AD)](../authentication/concept-mfa-whichversion.md)och du kan delegera möjligheten att hantera vem som har åtkomst till programmet med hjälp av Azure [AD-självbetjäningsgrupphantering.](groups-self-service-management.md)

## <a name="next-steps"></a>Nästa steg

* [Programhantering i Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Skydda appar med villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md)
* [Gruppledning för självbetjäning/SSAA](groups-self-service-management.md)
