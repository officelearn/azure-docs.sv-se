---
title: Steg för att konfigurera ett OpenID/OAuth-program från Azure AD App-galleriet | Microsoft Docs
description: Steg för att konfigurera ett OpenID/OAuth-program från Azure AD App-galleriet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 176af41197810059a17daf5ab09d29e0169e9640
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225015"
---
# <a name="steps-to-configure-an-openidoauth-application-from-azure-ad-app-gallery"></a>Steg för att konfigurera ett OpenID/OAuth-program från Azure AD App-galleriet

## <a name="process-of-open-id-application-addition-from-gallery"></a>Processen för att öppna ID programmet tillägg från galleriet

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen](./media/openidoauth-tutorial/tutorial_general_01.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program](./media/openidoauth-tutorial/tutorial_general_02.png)

3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](./media/openidoauth-tutorial/tutorial_general_03.png)

4. I sökrutan skriver **programnamn**, Välj den **önskade programmet** från resultatet panelen och logga upp till programmet.

    ![Lägger till program](./media/openidoauth-tutorial/addfromgallery.png)

    > [!NOTE]
    > Knappen är inaktiverad som standard för öppna ID Connect och OAuth appar Lägg till. Här klient administratören klickar på den **anmälan** knappen och ge medgivande till programmet. Klient behöver du inte uttryckligen, lägga till och göra konfigurationerna med att programmet ska hämta lagts till kunden.

    ![Knappen Lägg till](./media/openidoauth-tutorial/addbutton.png)

5. När du klickar på länken-registrering omdirigeras till sidan för Azure AD för autentiseringsuppgifter för inloggning.

6. Användare måste acceptera medgivande från medgivande sida och därefter, program startsidan ska visas efter en lyckad autentisering.

    > [!NOTE]
    > Kunder kan bara lägga till en instans av programmet. Om du redan har lagt till ett och försökte ange samtycke igen läggs det inte igen i klienten. De kan därför logiskt använda endast en app-instansen i klienten.

## <a name="authentication-flow-using-openid-connect"></a>Autentiseringsflödet med OpenID Connect

Det mest grundläggande flödet inloggning innehåller följande steg - dem beskrivs i detalj nedan.

![Autentiseringsflödet med OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

* **Flera innehavare programmet** -ett program med flera innehavare är avsedd att användas i många organisationer är inte en organisation. Detta är vanligtvis programvara som en-tjänst (SaaS)-program som skrivits av en oberoende programvaruleverantör (ISV). Program med flera klienter måste tillhandahållas i varje katalog där de kommer att användas, vilket kräver att användaren eller administratören tillstånd att registrera dem. Medgivande processen startas när ett program som har registrerats i katalogen och ges åtkomst till Graph API eller kanske en annan webb-API. När en användare eller administratör från en annan organisation registrera dig för att använda programmet, visas en dialogruta som visar de behörigheter som programmet kräver. Användaren eller administratören kan sedan samtycka till programmet, vilket ger programmet tillgång till den angivna data och slutligen registrerar programmet i sina kataloger.

    > [!NOTE]
    > Om du gör ditt program tillgängliga för användare i flera kataloger, behöver du en mekanism för att avgöra vilken de inte finns i. En enskild klient behöver bara söka i en egen katalog för en användare när ett program med flera innehavare behöver identifiera en viss användare från alla kataloger i Azure AD. Om du vill utföra den här uppgiften, innehåller Azure AD en gemensam autentisering ändpunkt där alla program med flera innehavare kan dirigera inloggning-förfrågningar, i stället för en klient-specifika slutpunkt. Den här slutpunkten är [ https://login.microsoftonline.com/common ](https://login.microsoftonline.com/common) för alla kataloger i Azure AD kan vara en slutpunkt för klient-specifika [ https://login.microsoftonline.com/contoso.onmicrosoft.com ](https://login.microsoftonline.com/contoso.onmicrosoft.com). Vanliga slutpunkten är särskilt viktigt att tänka på när du utvecklar programmet eftersom du behöver den nödvändiga logiken för att hantera flera klienter under inloggning, utloggning, och token-verifiering.

Azure AD-teamet som standard befordrar flera innehavare program som enkelt kan användas i olika organisationer och är enkel att använda när du godkänt samtycke.

## <a name="what-is-consent-framework"></a>Vad är medgivande Framework?

Azure AD medgivande framework gör det enkelt att utveckla flera innehavare webb- och interna klientprogram. Dessa program Tillåt inloggning av användarkonton från en Azure AD-klient, som skiljer sig från den där programmet har registrerats. De kan också behöva åtkomst till webb-API: er, till exempel Microsoft Graph API (för att få åtkomst till Azure Active Directory, Intune och tjänster i Office 365) och andra Microsoft-tjänster API: er, utöver egna web API: er. Ramen är baserad på en användare eller administratör ge medgivande till ett program som begär registreras i katalogen, vilket kan innebära att få åtkomst till katalogdata. Efter tillstånd ges kommer klientprogrammet att kunna anropa Microsoft Graph API för användarens räkning och använda informationen efter behov.

Den [Microsoft Graph API](https://graph.microsoft.io/) ger åtkomst till data i Office 365 (till exempel kalendrar och meddelanden från Exchange, platser och listor från SharePoint, dokument från OneDrive, uppgifter från Planner arbetsböcker från Excel, OneNote-anteckningsböcker osv), samt användare och grupper från Azure AD och andra dataobjekt från flera Microsoft-molntjänster.

Följande steg visar hur samtycke uppleva fungerar för både programutvecklaren och användare.

1. Anta att du har ett webbprogram för klienten som behöver begära särskild behörighet för att komma åt en resurs/API. Azure-portalen för att deklarera behörighetsbegäranden konfiguration för närvarande. Precis som andra konfigurationsinställningar blir de en del av programmets Azure AD-registrering:

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Överväg att ditt program behörighet har uppdaterats programmet körs och en användare kommer att använda den för första gången. Programmet måste först att hämta en Auktoriseringskoden från Azure AD / godkänna endpoint. Auktoriseringskoden kan sedan användas för att få en ny tillgång och uppdatera token.

3. Om användaren inte redan är autentiserad, Azure AD'S / godkänna endpoint frågar efter inloggning.

    ![Autentisering](./media/openidoauth-tutorial/authentication.png)

4. När användaren har loggat in avgör Azure AD om användaren behöver visas en sida medgivande. Detta baseras på om användaren (eller organisationens administratör) har redan beviljats programmet samtycke. Om ditt medgivande inte redan har fått, Azure AD efterfrågar medgivande och visar de behörigheter som krävs som behövs för att fungera. En uppsättning behörigheter som visas i dialogrutan medgivande överensstämmer med de som valts i den delegerade behörigheter i Azure-portalen.

    ![Medgivande sida](./media/openidoauth-tutorial/consentpage.png)

Vissa behörigheter kan vara godkänt för av en vanlig användare, medan andra kräver en Innehavaradministratör medgivande.

## <a name="whats-the-difference-between-admin-consent-and-user-consent"></a>Vad är skillnaden mellan Admin medgivande och användargodkännande?

Som administratör kan samtycker du också till att ett program delegerade behörigheter för alla användare i din klient. Administrativa samtycke förhindrar medgivande dialogrutan visas för varje användare i klienten och kan göras av användare med administratörsrollen i Azure-portalen. Klicka på nödvändiga behörigheter och klicka på knappen bevilja behörigheter på inställningssidan för ditt program.

![Bevilja behörighet](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Ge medgivande med knappen ge behörighet krävs för närvarande för enstaka sida program (SPA) som använder ADAL.js. Annars misslyckas programmet när åtkomsttoken begärs.

Behörigheter endast appen kräver alltid en Innehavaradministratör medgivande. Om ditt program begär en endast app-behörighet och en användare försöker logga in på programmet, visas ett felmeddelande om användaren inte kunna medgivande.

Om programmet använder behörigheter som kräver godkännande av administratören, som du behöver ha en gest, till exempel en knapp eller en länk där administratören kan starta åtgärden. Begäran som programmet skickar för den här åtgärden är vanliga OAuth2/OpenID Connect auktoriseringsbegäran som också innehåller uppmaningen = admin_consent frågesträngparametern. När administratören har godkänt och tjänstens huvudnamn skapas i kundens klient, efterföljande inloggning förfrågningar behöver inte uppmaningen = admin_consent-parametern. Eftersom administratören har valt de begärda behörigheterna accepteras, tillfrågas inga andra användare i klienten om samtycke från den tidpunkten och framåt. En Innehavaradministratör kan inaktivera möjligheten för vanliga användare att samtycka till program. Om den här funktionen är inaktiverad krävs alltid admin medgivande för programmet som ska användas i klienten. Om du vill testa programmet med slutanvändarens godkännande inaktiverad, hittar du konfigurationsväxel i i [Azure-portalen](https://portal.azure.com/) i den [användarinställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) avsnittet **Enterprise program**

Uppmaningen = admin_consent parameter kan även användas av program som begär behörighet som inte kräver godkännande av administratören. Ett exempel på när det skulle användas är om programmet kräver en upplevelse som där tenant admin ”registrerar sig” en gång och ingen användare tillfrågas om samtycke från den punkten på.

Om ett program kräver godkännande av administratören och en administratör loggar in utan uppmaningen = admin_consent parameter som skickas när administratören godkänner har till programmet som det gäller endast för sitt användarkonto. Vanliga användare kommer fortfarande inte att kunna logga in eller samtycka till programmet. Den här funktionen är användbart om du vill ge innehavaradministratören möjlighet att utforska dina program innan andra användare åtkomst.