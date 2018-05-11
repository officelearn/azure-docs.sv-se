---
title: Så här väljer du vilken typ som programmet ska använda när du lägger till ett program | Microsoft Docs
description: Förstå typerna som stöds av program som du kan integrera med Azure AD och deras relaterade konfigurationsalternativ
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
ms.openlocfilehash: a34c3343b669cb80ad88c1b09fe95b1b1d9b5275
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-choose-which-application-type-to-use-when-adding-an-application"></a>Så här väljer du vilken typ som programmet ska använda när du lägger till ett program

Den här artikeln hjälper dig att förstå de fyra huvudsakliga typerna av program som du kan integrera med Azure AD:

* Vad som stöds av var och en av dem.
* Varför du kan välja vilket program
* Hur du konfigurerar dessa program core egenskaper, t.ex. hur användare kan **etableras**, eller vad **enkel inloggning** teknik som ska användas.

## <a name="supported-application-types-in-azure-ad"></a>Typer av program som stöds i Azure AD

Azure AD stöder fyra huvudsakliga programtyper som du kan lägga till med hjälp av den **Lägg till** funktion hittar du under **företagsprogram**. Exempel på dessa är:

-   **Azure AD-galleriet program** – ett program som har varit förintegrerade för enkel inloggning med Azure AD.

-   **Application Proxy program** – ett program som körs i din lokala miljö som du vill ge säker enkel inloggning till externt.

-   **Anpassad utvecklat program** – ett program som din organisation vill utveckla på den Azure AD plattform, men som kanske inte finns ännu.

-   **Icke-galleriet program** – ta med egna program! En länk som du vill eller alla program som visas med ett användarnamn och lösenord fält har stöd för SAML- eller OpenID Connect-protokoll och stöder SCIM som du vill integrera för enkel inloggning med Azure AD.

## <a name="features-and-capabilities-supported-by-all-the-preceding-application-types"></a>Funktioner som stöds av de föregående programtyperna

Följande funktioner stöds av någon av de föregående fyra programtyperna i Azure AD:

-   **Snabbstart** – komma igång med ett program snabbare genom att följa [steg för enkel distribution](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)

-   **Allmänna egenskaper för management** – hämta en [direkt djuplänken](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users) till ett program [anpassa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) för ett program eller [inaktivera programmet](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) för alla användare.

-   **Hantering av användare och grupp** – [tilldela](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) eller [ta bort](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) användare och grupper till ett program, och du kan också tilldela programroller för specifika dessa användare och grupper har åtkomst till

-   **Självbetjäning programåtkomst** – att användarna kan begära [självbetjäning programåtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) till ett program från sina program åtkomst paneler antingen genom att lägga till ett program direkt eller [ ansluter till en aktiverad grupp med självbetjäning](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), kan du alternativt som kräver godkännande av företag på vägen

-   **Logga in loggar** – Se [alla inloggningarna till ett program](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins), eller alla program

-   **Granskningsloggar** – Se [detaljerad granskningsloggar om ändringar i ett program](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs), eller för alla program

-   **Villkorlig och risker-baserad åtkomst** – Ställ kraftfulla [villkor-baserade åtkomstregler](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) som tillämpas när användarna försöker logga in på ett visst program

-   **Visa behörigheter** – ta del av den [OAuth2 behörigheter](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) ett program har åtkomst till i din katalog från en enda placera

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Enkel inloggning och provisioning lägen som stöds av specifika programtyper

I följande tabell beskrivs de olika enkla inloggning och provisioning-lägena stöds av varje föregående programtyper. Du kan använda den här tabellen hjälper dig att förstå vilket program som du behöver lägga till stöd för ett specifikt mål.

  ![Tabell för App-typer](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Hur du väljer ett läge för enkel inloggning

Följande är de stöds **enkel inloggning** lägen för Azure AD-program.

-   **Azure AD enkel inloggning inaktiverat** – Välj Azure AD enkel inloggning inaktiverad **läget för enkel inloggning** om du ännu inte är redo att integrera programmet med enkel inloggning med Azure AD eller bara testar det.

-   **Länkad inloggning** – Välj den [inloggning länkade](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **läget för enkel inloggning** om du har ett program som redan är kopplad till en befintlig enkel inloggning lösning, eller om du bara vill Publicera en enkel länk för användarna i deras [programmet åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) eller [startprogrammet för Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

-   **Lösenordsbaserade inloggning** – Välj den [lösenordsbaserade inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **läget för enkel inloggning** om ditt program visas med ett HTML-fält för användarnamn och lösenord och du vill lagra det användarnamnet och lösenord på ett säkert sätt ska återupprepas till programmet senare

-   **SAML-baserade inloggning** – Välj den [SAML-baserade inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) enkel inloggning på läge om programmet stöder SAML- eller OpenID Connect-protokoll eller om du vill kunna mappa användare till programroller för specifika baserat på regler Du definierar i SAML-anspråk *

   >[!NOTE]
   >Det här alternativet är inte tillgängligt när application proxy har konfigurerats för ett program.
   >
   >

-   **Rubrik-baserade inloggning** – Välj det här [huvud-baserade inloggning](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) enkel inloggning läge om du har ett program med PingAccess som stöder HTTP-huvud-baserad autentisering som du vill utföra enkel inloggning till 

   >[!NOTE]
   >Det här alternativet är bara tillgänglig när programproxy och PingAccess konfigureras för ett program.
   >
   >

-   **Integrerad Windows-autentisering** – Välj den [integrerad Windows-autentisering](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) enkel inloggning på läge när exponera ett lokalt WIA program som du vill utföra enkel inloggning till 

   >[!NOTE]
   >Det här alternativet är bara tillgänglig när application proxy har konfigurerats för ett program.
   >
   >

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Enkel inloggning lägen för anpassad utvecklade program

Program som du har anpassat utvecklas genom den [anpassad utvecklat program](#_Custom-Developed_Applications) upplevelse stöder också ytterligare enkel inloggning lägen visas inte tidigare, bland annat:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) baserat inloggning

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) baserat inloggning

-   [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) baserat inloggning

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) baserat inloggning

Läs den [Utvecklarhandbok för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) vill veta mer om hur du skapar en anpassad utvecklade program som stöder dessa lägen för enkel inloggning.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Hur du ställer in ett program enkel inloggning läge

Så här anger du ett program **enkel inloggning** läge, följer du dessa anvisningar:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet läses in klickar du på **enkel inloggning** från programmenyn vänstra navigeringsfönstret.

## <a name="how-to-choose-a-provisioning-mode"></a>Så här väljer du ett Etableringsläge

-   **Manuell etablering** – Välj den [manuell](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) Etableringsläge om du har befintliga konton eller om du vill hantera konton för det här programmet utanför Azure AD.

-   **Automatisk etablering** – Välj den [automatisk](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) **Etableringsläge** om du vill aktivera automatisk API-baserad etablering och/eller ta bort etableringen av användarkonton till det här programmet 

   >[!NOTE]
   >Det här alternativet är bara tillgängligt för program i den **aktuella** kategorin för den [Azure AD Application Gallery](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal#the-new-and-improved-application-gallery).
   >
   >

-   **SCIM-baserade Automatisk etablering** – använda [SCIM-baserade Automatisk etablering](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) om programmet stöder SCIM-protokollet för att upptäcka ändringar av användare och grupper som släpps automatiskt för att ändringarna ska alla program som är integrerade med Azure AD 

   >[!NOTE]
   >Det här alternativet anges inte som en specifik Etableringsläge, men är aktiverad som standard för alla program som är integrerade med Azure AD.
   >
   >

## <a name="how-to-set-an-applications-provisioning-mode"></a>Etableringsläge för hur du ställer in ett program

Så här anger du ett program **etablering** läge, följer du dessa anvisningar:

Så här anger du ett program **enkel inloggning** läge, följer du dessa anvisningar:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill konfigurera etablering.

7.  När programmet läses in klickar du på **etablering** från programmenyn vänstra navigeringsfönstret.

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](manage-apps/what-is-application-management.md)
