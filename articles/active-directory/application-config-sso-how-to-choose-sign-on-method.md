---
title: Hur du avgör vilken enkel inloggning på metod för att använda | Microsoft Docs
description: Förstå enkel inloggning lägen stöds av Azure AD och hur du väljer vilka som ska användas för programmet som du är intresserad av.
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 4363f308f9bfa685b0f4e946cdd97d34b6ece197
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-determine-what-single-sign-on-method-to-use"></a>Hur du avgör vilken enkel inloggning på metod som ska användas

Den här artikeln hjälper dig att förstå enkel inloggning lägen stöds av Azure AD och hur du väljer vilka som ska användas för programmet som du är intresserad av.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Enkel inloggning och provisioning lägen som stöds av specifika programtyper

I följande tabell beskrivs de olika enkla inloggning och provisioning-lägena stöds av varje föregående programtyper. Du kan använda den här tabellen hjälper dig att förstå vilket program som du behöver lägga till stöd för ett specifikt mål.

  ![Asien/Stillahavsområdet register](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Hur du väljer ett läge för enkel inloggning

Följande är de stöds **enkel inloggning** lägen för Azure AD-program.

-   **Azure AD enkel inloggning inaktiverat** – Välj Azure AD enkel inloggning inaktiverad **läget för enkel inloggning** om du ännu inte är redo att integrera programmet med enkel inloggning med Azure AD eller bara testar det.

-   **Länkad inloggning** – Välj den [inloggning länkade](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **läget för enkel inloggning** om du har ett program som redan är kopplad till en befintlig enkel inloggning lösning, eller om du bara vill Publicera en enkel länk för användarna i deras [programmet åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) eller [startprogrammet för Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

-   **Lösenordsbaserade inloggning** – Välj den [lösenordsbaserade inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **läget för enkel inloggning** om ditt program visas med ett HTML-fält för användarnamn och lösenord och du vill lagra det användarnamnet och lösenord på ett säkert sätt ska återupprepas till programmet senare

-   **SAML-baserade inloggning** – Välj den [SAML-baserade inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) enkel inloggning på läge om programmet stöder SAML- eller OpenID Connect-protokoll eller om du vill kunna mappa användare till programroller för specifika baserat på regler Du definierar i SAML-anspråk *(** Obs:** det här alternativet är inte tillgängligt när application proxy har konfigurerats för ett program) *

-   **Rubrik-baserade inloggning** – Välj det här [huvud-baserade inloggning](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) enkel inloggning läge om du har ett program med PingAccess som stöder HTTP-huvud-baserad autentisering som du vill utföra enkel inloggning till *(** Obs:** det här alternativet är endast tillgängligt när programproxy och PingAccess konfigureras för ett program) *

-   **Integrerad Windows-autentisering** – Välj den [integrerad Windows-autentisering](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) enkel inloggning på läge när exponera ett lokalt WIA program som du vill utföra enkel inloggning till *(** Obs:** det här alternativet är endast tillgängligt när application proxy har konfigurerats för ett program) *

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Enkel inloggning lägen för anpassad utvecklade program

Program som du har anpassat utvecklas genom den [anpassad utvecklat program](#_Custom-Developed_Applications) upplevelse stöder också ytterligare enkel inloggning lägen visas inte tidigare, bland annat:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) baserat inloggning

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) baserat inloggning

-   [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) baserat inloggning

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) baserat inloggning

Läs den [Utvecklarhandbok för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) vill veta mer om hur du skapar en anpassad utvecklat program som stöder dessa lägen för enkel inloggning.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Hur du ställer in ett program enkel inloggning läge

Så här anger du ett program **enkel inloggning** läge, följer du dessa anvisningar:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning

7.  När programmet läses in klickar du på **enkel inloggning** från programmenyn vänstra navigeringsfönstret.

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](active-directory-application-proxy-sso-using-kcd.md)

