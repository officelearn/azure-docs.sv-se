---
title: Så här kontrollerar du vilka enkel inloggning på metod för att använda | Microsoft Docs
description: Förstå enkel inloggning för läget stöds av Azure AD och hur du väljer vilken jag ska välja för programmet som du är intresserad av.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 79fe30fbe0baed9d62e6bc3328e754958516ef70
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "36335415"
---
# <a name="how-to-determine-what-single-sign-on-method-to-use"></a>Så här kontrollerar du vilka enkel inloggning metod som ska användas

Den här artikeln hjälper dig att förstå enkel inloggning för läget stöds av Azure AD och hur du väljer vilken jag ska välja för programmet som du är intresserad av.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Enkel inloggning och etablera lägen som stöds av specifika programtyper

I följande tabell beskrivs de olika enkla inloggning och etablering lägena stöds av var och en av de föregående programtyperna. Du kan använda den här tabellen för att hjälpa dig att förstå vilket program som du behöver lägga till stöd för ett specifikt mål.

  ![Asien och Stillahavsområdet typer tabell](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Så här väljer du ett läge för enkel inloggning

Följande är de stöds **enkel inloggning** lägen för Azure AD-program.

-   **Azure AD enkel inloggning inaktiverad** – Välj Azure AD enkel inloggning inaktiverad **läge för enkel inloggning** om du ännu inte är redo att integrera programmet med enkel inloggning med Azure AD, eller också kan du bara testar det.

-   **Länkad inloggning** – Välj den [inloggning länkade](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **läge för enkel inloggning** om du har ett program som redan är kopplad till en befintlig enkel inloggning lösning, eller om du bara vill Publicera en enkel länk för dina användare i deras [Programåtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) eller [startprogrammet för Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

-   **Lösenordsbaserad inloggning** – Välj den [lösenordsbaserad inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **läge för enkel inloggning** om ditt program visas med ett HTML-användarnamn och lösenord fält och du vill lagra det aktuella användarnamnet och lösenord på ett säkert sätt som ska återupprepas till programmet senare

-   **SAML-baserad inloggning** – Välj den [SAML-baserad inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) enkel inloggning om ditt program har stöd för SAML- eller OpenID Connect-protokoll eller om du vill för att kunna mappa användare till specifika programroller baserat på regler Du definierar i SAML-anspråk *(** Obs:** det här alternativet är inte tillgängligt när application proxy har konfigurerats för ett program) *

-   **Rubrikbaserad inloggning** – Välj det här [rubrikbaserad inloggning](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) enkel inloggningsläge om du har ett program med PingAccess som har stöd för HTTP-huvud-baserad autentisering som du vill utföra enkel inloggning till *(** Obs:** det här alternativet är bara tillgängligt om application proxy och PingAccess är konfigurerade för ett program) *

-   **Integrerad Windows-autentisering** – Välj den [integrerad Windows-autentisering](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) enkel inloggning när exponerar ett lokalt WIA program som du vill utföra enkel inloggning till *(** Obs:** det här alternativet är bara tillgängligt när application proxy har konfigurerats för ett program) *

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Enkel inloggning-lägen för egenutvecklade program

Program som du har anpassat utvecklat genom den [egenutvecklat program](#_Custom-Developed_Applications) upplevelse har också stöd för ytterligare enkel inloggning för lägena inte tidigare, bland annat:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) baserad inloggning

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) baserad inloggning

-   [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) baserad inloggning

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) baserad inloggning

Läs den [Utvecklarhandbok för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) mer information om hur du skapar ett egenutvecklat program som stöder dessa lägen för enkel inloggning.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Hur du ställer in ett programs enkel inloggningsläge

Ange ett programs **enkel inloggning** läge, följer du dessa instruktioner:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning

7.  När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigeringsmenyn.

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

