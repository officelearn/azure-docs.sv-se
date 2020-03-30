---
title: Slutanvändarens upplevelser för program - Azure Active Directory
description: Azure Active Directory (Azure AD) innehåller flera anpassningsbara sätt att distribuera program till slutanvändare i organisationen.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72b3e37a423442194b81e3d10ecc7157201ff8be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266629"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Slutanvändarens upplevelser för program i Azure Active Directory

Azure Active Directory (Azure AD) innehåller flera anpassningsbara sätt att distribuera program till slutanvändare i organisationen:

* Åtkomstpanelen för Azure AD
* Startprogram för Office 365
* Direkt inloggning till federerade appar
* Djuplänkar till federerade, lösenordsbaserade eller befintliga appar

Vilken eller vilka metoder du väljer att distribuera i din organisation är din diskretion.

## <a name="azure-ad-access-panel"></a>Åtkomstpanelen för Azure AD

Åtkomstpanelen https://myapps.microsoft.com på är en webbaserad portal som gör att en slutanvändare med ett organisationskonto i Azure Active Directory kan visa och starta program som de har beviljats åtkomst till av Azure AD-administratören. Om du är slutanvändare med [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/)kan du också använda grupphanteringsfunktioner för självbetjäning via åtkomstpanelen.

![Skärmbild visar Portalen för Azure AD Access Panel](media/what-is-single-sign-on/azure-ad-access-panel.png)

Som standard visas alla program tillsammans på en enda sida. Men du kan använda samlingar för att gruppera relaterade program och presentera dem på en separat flik, vilket gör dem lättare att hitta. Du kan till exempel använda samlingar för att skapa logiska grupperingar av program för specifika jobbroller, uppgifter, projekt och så vidare. Mer information finns i [Så här använder du Mina appar-samlingar för att anpassa användaråtkomstpaneler](access-panel-collections.md). 

Åtkomstpanelen är skild från Azure-portalen och kräver inte att användare har en Azure-prenumeration eller Office 365-prenumeration.

Mer information finns i åtkomstpanelen för Azure AD finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="office-365-application-launcher"></a>Startprogram för Office 365

För organisationer som har distribuerat Office 365 visas även program som tilldelats användare via [https://portal.office.com/myapps](https://portal.office.com/myapps)Azure AD i Office 365-portalen på . Detta gör det enkelt och bekvämt för användare i en organisation att starta sina appar utan att behöva använda en andra portal och är den rekommenderade appstartlösningen för organisationer som använder Office 365.

![Skärmbild visar Office 365-portalen](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Mer information om programstartprogrammet för Office 365 finns [i Låta appen visas i startprogrammet för Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

## <a name="direct-sign-on-to-federated-apps"></a>Direkt inloggning till federerade appar

De flesta federerade program som stöder SAML 2.0, WS-Federation eller OpenID connect stöder också möjligheten för användare att starta i programmet och sedan logga in via Azure AD antingen genom automatisk omdirigering eller genom att klicka på en länk för att logga in. Detta kallas tjänstprovider -initierad inloggning och de flesta federerade program i Azure AD-programgalleriet stöder detta (se dokumentationen som länkas från appens enda inloggningskonfigurationsguide i Azure-portalen för mer information).

![Exempel på inloggningssida för mobilapp](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Direkt inloggningslänkar

Azure AD stöder också direkta enkel inloggningslänkar till enskilda program som stöder lösenordsbaserad enkel inloggning, länkad enkel inloggning och alla former av federerade enkel inloggning.

Dessa länkar är särskilt utformade URL:er som skickar en användare via Azure AD-inloggningsprocessen för ett visst program utan att användaren kräver att användaren startar dem från Azure AD-åtkomstpanelen eller Office 365. Dessa **användaråtkomstadresser** finns under egenskaperna för tillgängliga företagsprogram. I Azure-portalen väljer du **Azure Active Directory** > **Enterprise-program**. Markera programmet och välj sedan **Egenskaper**.

![Exempel på url för användaråtkomst i Twitter-egenskaper](media/end-user-experiences/direct-sign-on-link.png)

Dessa länkar kan kopieras och klistras in var du vill ge en inloggningslänk till det valda programmet. Detta kan finnas i ett e-postmeddelande eller i en anpassad webbaserad portal som du har konfigurerat för åtkomst till användarprogram. Här är ett exempel på en Azure AD direkt enda inloggningsadress för Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

I likhet med organisationsspecifika url:er för åtkomstpanelen kan du anpassa webbadressen ytterligare genom att lägga till en av de aktiva eller verifierade domänerna för katalogen efter myapps.microsoft.com domänen. Detta säkerställer att alla organisationsmärken läses in omedelbart på inloggningssidan utan att användaren behöver ange sitt användar-ID först:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

När en behörig användare klickar på en av dessa programspecifika länkar ser de först sin organisationsinloggningssida (förutsatt att de inte redan är inloggade) och efter inloggning omdirigeras till sin app utan att stanna vid åtkomstpanelen först. Om användaren saknar förutsättningar för att komma åt programmet, till exempel det lösenordsbaserade webbläsartillägget med ett tecken, uppmanas användaren att installera tillägget som saknas. Länk-URL:en förblir också konstant om den enda inloggningskonfigurationen för programmet ändras.

Dessa länkar använder samma åtkomstkontrollmekanismer som åtkomstpanelen och Office 365, och endast de användare eller grupper som har tilldelats programmet i Azure-portalen kan autentiseras. Alla användare som är obehöriga kommer dock att se ett meddelande som förklarar att de inte har beviljats åtkomst och får en länk för att läsa in åtkomstpanelen för att visa tillgängliga program som de har åtkomst till.

## <a name="next-steps"></a>Nästa steg

Information om distributionsplaner finns i [Azure Active Directory-distributionsplaner](../fundamentals/active-directory-deployment-plans.md)
