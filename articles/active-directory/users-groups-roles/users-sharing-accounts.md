---
title: Dela konton med hjälp av Azure AD | Microsoft Docs
description: Beskriver hur Azure Active Directory kan organisationer på ett säkert sätt dela konton för lokala appar och konsument-molntjänster.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 10/29/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 58b91388ec81726bd42e57fbf2b83d3d9f368833
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244026"
---
# <a name="sharing-accounts-with-azure-ad"></a>Dela konton med Azure AD
## <a name="overview"></a>Översikt
Ibland behöver organisationer använda ett användarnamn och lösenord för flera personer, som vanligtvis sker i två fall:

* Vid åtkomst till program som kräver en unik inloggningsnamn och lösenord för varje användare, oavsett om lokala appar eller konsument molntjänster (till exempel företagets sociala konton).
* När du skapar miljöer med flera användare. Du kanske har en enda, lokala konto som har upphöjda privilegier och används för att viktiga inställningar, administration och återställning aktiviteter. Till exempel kontot Lokal ”global administratör” för Office 365 eller rotkontot i Salesforce.

Traditionellt har delas dessa konton genom att distribuera autentiseringsuppgifterna (användarnamn och lösenord) till rätt personer eller lagra dem i en delad plats som flera betrodda agenter kan komma åt.

Den traditionella delningsapplikationen modellen har flera nackdelar:

* Aktivera åtkomst till nya program måste du distribuera autentiseringsuppgifterna till alla som behöver åtkomst.
* Varje delad program kan kräva en egen unik uppsättning delade autentiseringsuppgifter, vilket kräver att användarna att komma ihåg flera uppsättningar med autentiseringsuppgifter. När användare behöver komma ihåg många autentiseringsuppgifter, ökar risken att de bästa praxis för riskfyllda. (till exempel skriver ned lösenord).
* Du kan inte se vem som har åtkomst till ett program.
* Du kan inte se vem som har *nås* ett program.
* Om du vill ta bort åtkomsten till ett program kan behöva du uppdatera autentiseringsuppgifterna och distribuera dem till alla som behöver åtkomst till programmet.

## <a name="azure-active-directory-account-sharing"></a>Azure Active Directory-konto och delning
Azure AD tillhandahåller en ny metod till delade konton som eliminerar dessa nackdelar.

Azure AD-administratör konfigurerar vilka program som en användare får åtkomst genom att använda åtkomstpanelen och välja vilken typ av enkel inloggning bäst passar för programmet. En av dessa typer *lösenordsbaserad enkel inloggning på*, kan Azure AD som fungerar som en typ av en ”mellanhand” under inloggning för appen.

Användarna loggar in en gång med organisationskontot. Det här kontot är samma som de regelbundet använder för att komma åt skrivbordet eller e-post. De kan upptäcka och komma åt dessa program som de är tilldelade till. Den här listan över program kan innehålla valfritt antal delade autentiseringsuppgifter med delade konton. Slutanvändaren behöver inte komma ihåg eller Skriv ned de olika kontona som de använder.

Delade konton inte bara öka tillsyn och användbarhet, de också förbättra din säkerhet. Användare med behörighet att använda autentiseringsuppgifterna som finns i inte delade lösenordet, men i stället får behörighet att använda lösenord som en del av en dirigerad autentiseringsflödet. Dessutom kan ger vissa lösenord SSO-program dig alternativet att använda Azure AD för att regelbundet förnya (uppdatera) lösenord. Systemet använder stora och komplexa lösenord, vilket ökar kontosäkerheten. Administratören kan enkelt bevilja eller återkalla åtkomst till ett program, vet vem som har åtkomst till kontot och vem som har åtkomst till den tidigare.

Azure AD stöder delade konton för Enterprise Mobility Suite (EMS), Premium eller Basic licensierade användare, för alla typer av lösenord för enkel inloggning för program. Du kan dela konton för någon av tusentals förintegrerade program i programgalleriet och kan lägga till ditt eget program för autentisering av lösenord med [anpassade SSO-appar](../manage-apps/configure-single-sign-on-portal.md).

Azure AD-funktioner som möjliggör delning av konto:

* [Lösenord för enkel inloggning](../manage-apps/what-is-single-sign-on.md#password-based-single-sign-on)
* Lösenord för enkel inloggning för agenten
* [Grupptilldelning](groups-self-service-management.md)
* Lösenord för anpassade appar
* [App instrumentpanel/användningsrapporter](../active-directory-passwords-get-insights.md)
* Slutanvändarportaler för åtkomst
* [App proxy](../manage-apps/application-proxy.md)
* [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Dela ett konto
Om du vill använda Azure AD för att dela ett konto, måste du:

* Lägga till ett program [appgalleriet](https://azure.microsoft.com/marketplace/active-directory/) eller [anpassade program](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* Konfigurera program för lösenord för enkel inloggning (SSO)
* Använd [gruppbaserad tilldelning](groups-saasapps.md) och välj alternativet för att ange en delad autentiseringsuppgift
* Valfritt: i vissa program, till exempel Facebook, Twitter och LinkedIn, du kan aktivera alternativet för [Azure AD automatiserad lösenord övergången](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/)

Du kan också göra din delade konto säkrare med Multi-Factor Authentication (MFA) (Läs mer om [skydda program med Azure AD](../authentication/concept-mfa-whichversion.md)) och du kan delegera möjligheten att hantera vem som har åtkomst till programmet med [ Azure AD-självbetjäning](groups-self-service-management.md) grupphantering.

## <a name="related-articles"></a>Relaterade artiklar
* [Programhantering i Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Skydda appar med villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md)
* [Självbetjäning management/SSAA](groups-self-service-management.md)
