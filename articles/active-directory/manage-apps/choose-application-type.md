---
title: Så här väljer du vilken program typ som ska användas för att lägga till ett program | Microsoft Docs
description: Förstå de typer av program som stöds som du kan integrera med Azure AD och deras relaterade konfigurations alternativ
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: d5bd2397c345a4f670bde343f751cd69f825ecb9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056064"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>Välja program typ när du lägger till ett program i Azure Active Directory

Lär dig mer om de fyra typerna av program som du kan lägga till i Azure Active Directory (Azure AD). När du lägger till ett program i Azure Active Directory uppmanas du att välja en av de fyra program typerna.

## <a name="what-are-the-types-of-applications"></a>Vilka är typerna av program?

Azure AD stöder fyra huvud program typer som du kan lägga till med hjälp av funktionen **Lägg till** som finns under **företags program**. Exempel på dessa är:

- **Azure AD Gallery-program** – ett program som har integrerats för enkel inloggning med Azure AD.

- Program för **programproxy** – ett program som körs i din lokala miljö som du vill tillhandahålla säker enkel inloggning till externt.

- **Anpassade program** – ett program som din organisation vill utveckla på Azure AD-program utvecklings plattformen, men som kanske ännu inte finns.

- **Program som inte är gallerier** – ta med dina egna program! Alla webb länkar som du vill använda, eller ett program som återger ett användar namn och lösen ord, stöder SAML-eller OpenID Connect-protokoll eller stöder SCIM som du vill integrera för enkel inloggning med Azure AD.

## <a name="features-and-capabilities-supported-by-the-application-types"></a>Funktioner och funktioner som stöds av program typerna

Följande funktioner stöds av någon av de föregående fyra program typerna i Azure AD:

- **Snabb start** – kom igång snabbt med ett program genom att följa [enkla distributions steg](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)

- **Hantering av allmänna egenskaper** – hämta en [direkt djup länk](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) till ett program, [Anpassa anpassningen](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) av ett program eller [inaktivera programmet](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) för alla användare.

- **Hantering av användare och** grupper – [tilldela](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) eller [ta bort](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) användare och grupper till ett program och eventuellt tilldela specifika program roller som användare och grupper har åtkomst till

- Självbetjäning för **program åtkomst** – gör det möjligt för användarna att begära [åtkomst till självbetjänings program](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) till ett program från sina program åtkomst paneler genom att lägga till ett program direkt eller [ansluta till en självbetjänings aktive rad grupp ](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), om du behöver godkänna affärs godkännande på vägen

- **Inloggnings loggar** – se [alla inloggningar till ett program](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)eller alla dina program

- **Gransknings loggar** – se [detaljerade gransknings loggar om ändringar i ett program](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)eller till alla dina program

- **Villkorlig och riskfylld åtkomst** – Ställ in kraftfulla regler för villkorlig [åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) som tillämpas när användare försöker logga in till ett särskilt program

- **Vyn behörigheter** – Visa någon av de [OAuth2-behörigheter](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) som ett program har åtkomst till i din katalog från en enda plats

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Enkel inloggning och etablerings lägen som stöds av specifika program typer

I följande tabell beskrivs de olika lägena för enkel inloggning och etablering som stöds av var och en av de föregående program typerna. Du kan använda den här tabellen för att hjälpa dig att förstå vilket program som du behöver lägga till för att stödja ett speciellt mål.

  ![Partitionstabell Olika SSO-och etablerings lägen som stöds av varje typ av app](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Så här väljer du ett läge för enkel inloggning

Följande är de **enkel inloggnings** lägen som stöds för Azure AD-program.

- **Enkel inloggning i Azure AD har inaktiverats** – Välj Azure AD enkel inloggning inaktiverat **läge för enkel inloggning** om du inte är redo att integrera programmet med enkel inloggning med Azure AD, eller om du bara vill testa det

- **Länkad inloggning** – Välj läget [länkat](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) **läge för enkel** inloggning om du har ett program som redan är anslutet till en befintlig lösning för enkel inloggning, eller om du bara vill publicera en enkel länk för dina användare [ Program åtkomst panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) eller [Office 365 Application Launcher](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

- **Lösenordsbaserad inloggning** – Välj det [lösenordsbaserade](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) inloggnings **läget för enkel** inloggning om ditt program återger ett fält för HTML-användarnamn och-lösen ord och du vill lagra användar namnet och lösen ordet på ett säkert sätt för att kunna spelas upp i program senare

- **SAML-baserad inloggning** – Välj det [SAML-baserade inloggnings](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) läget för enkel inloggning om ditt program stöder SAML-eller OpenID Connect-protokoll, eller om du vill kunna mappa användare till specifika program roller baserat på regler som du definierar i SAML gällande

  >[!NOTE]
  >Det här alternativet är inte tillgängligt när programproxyfilen har kon figurer ATS för ett program.

- **Rubrikbaserade inloggning** – Välj det här [huvudbaserade inloggnings läget för](application-proxy-configure-single-sign-on-with-ping-access.md) enkel inloggning om du har ett program som använder PingAccess som stöder http-huvudbaserad autentisering som du vill utföra enkel inloggning på

  >[!NOTE]
  >Det här alternativet är bara tillgängligt om programproxyn och PingAccess har kon figurer ATS för ett program.

- **Integrerad Windows-autentisering** – Välj läget för enkel inloggning med [integrerad Windows-autentisering](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) när du exponerar ett lokalt WIA-program som du vill utföra enkel inloggning på

  >[!NOTE]
  >Det här alternativet är bara tillgängligt när programproxyn har kon figurer ATS för ett program.

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Läge för enkel inloggning för anpassade program som har utvecklats

Program som du har skapat med den anpassade program upplevelsen har också stöd för ytterligare läge för enkel inloggning som inte tidigare har listats, bland annat:

- [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) -baserad inloggning

- [OpenID Connect 1,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) -baserad inloggning

- [WS-Federation 1,2-](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) baserad inloggning

- [SAML 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) -baserad inloggning

Läs [Azure Active Directory Developer ' s guide](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) för att lära dig mer om hur du skapar ett anpassat, utvecklat program som stöder dessa läge för enkel inloggning.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Så här ställer du in ett programmets läge för enkel inloggning

Följ dessa instruktioner om du vill ställa in ett programs läge för enkel inloggning:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller medadministratör **.**
1. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.
1. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.
1. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.
1. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

1. Välj det program som du vill konfigurera enkel inloggning för.
1. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

## <a name="how-to-choose-a-provisioning-mode"></a>Så här väljer du ett etablerings läge

- **Manuell etablering** – Välj [manuellt](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) etablerings läge om du har befintliga konton eller vill hantera konton för det här programmet utanför Azure AD.

- **Automatisk etablering** – Välj det [automatiska](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) **etablerings läget** om du vill aktivera automatisk API-baserad etablering och/eller avetablering av användar konton för det här programmet 

- **Scim automatisk etablering** – Använd [scim automatisk etablering](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) om ditt program stöder scim-protokollet för att identifiera ändringar av användare och grupper, som automatiskt genereras för ändringar i program som är integrerade med Azure AD 

  >[!NOTE]
  >Det här alternativet visas inte som ett speciellt etablerings läge, men är aktiverat som standard för alla program som är integrerade med Azure AD.

## <a name="how-to-set-an-applications-provisioning-mode"></a>Ange etablerings läget för ett program

Följ dessa instruktioner om du vill ställa in ett programs **etablerings** läge:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller medadministratör **.**
1. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.
1. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.
1. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.
1. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

1. Välj det program som du vill konfigurera etableringen för.
1. När programmet har lästs in klickar du på **etablering** från programmets vänstra navigerings meny.

## <a name="next-steps"></a>Nästa steg

[Hantera program med Azure Active Directory](what-is-application-management.md)
