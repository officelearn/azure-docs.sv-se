---
title: Så här väljer du vilken programtyp som du använder när du lägger till ett program | Microsoft Docs
description: Förstå typerna som stöds av program som du kan integrera med Azure AD och deras relaterade konfigurationsalternativ
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: celested
ms.openlocfilehash: ec84ea47d7d0d136b744a4f7328a6729d05b0faf
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55961048"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>Välja vilken typ av program när du lägger till ett program i Azure Active Directory
Läs mer om de fyra typerna av program som du kan lägga till Azure Active Directory (AD Azure). När du lägger till ett program i Azure Active Directory, uppmanas du att välja en av fyra programtypen. 

## <a name="what-are-the-types-of-applications"></a>Vilka är typerna av program?

Azure AD stöder fyra huvudsakliga programtyper som du kan lägga till med hjälp av den **Lägg till** funktionen finns under **företagsprogram**. Exempel på dessa är:

-   **Azure AD-Galleriprogram** – ett program som har varit förintegrerade för enkel inloggning med Azure AD.

-   **Application Proxy-program** – ett program som körs i din lokala miljö som du vill ge säker enkel inloggning till externt.

-   **Egenutvecklat program** – ett program som din organisation vill utveckla på det Azure AD plattform, men som kanske inte finns ännu.

-   **Icke-Galleriprogram** – Använd dina egna program! Alla webblänk som du vill eller alla program som återger ett fält för användarnamn och lösenord, har stöd för SAML- eller OpenID Connect-protokoll eller stöder SCIM som du vill integrera för enkel inloggning med Azure AD.

## <a name="features-and-capabilities-supported-by-the-application-types"></a>Funktioner som stöds av programtyperna

Följande funktioner stöds av någon av de föregående fyra programtyperna i Azure AD:

-   **Snabbstart** – komma igång med ett program snabbare genom att följa [steg för enkel distribution](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)

-   **Allmänna egenskaper för management** – hämta en [direkt djuplänk](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) till ett program [anpassa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) för ett program, eller [inaktivera programmet](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) för alla användare.

-   **Användar- och grupphantering** – [tilldela](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) eller [ta bort](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) användare och grupper till ett program, och du kan också tilldela specifika programroller dessa användare och grupper har åtkomst till

-   **Självbetjäningsåtkomsten** – ge användarna begära [självbetjäningsåtkomsten](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) till ett program från sina Åtkomstpaneler för program som antingen genom att lägga till ett program direkt eller [ ansluter till en självbetjäning aktiverade grupp](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)kan du också att kräva godkännande av företag längs vägen

-   **Logga in loggar** – Se [alla inloggningar till ett program](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins), eller alla dina program

-   **Granskningsloggar** – Se [detaljerade granskningsloggar om ändringar av ett program](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs), eller till alla dina program

-   **Villkorlig och riskbaserad åtkomst** – ange kraftfulla [villkor-baserade åtkomstregler](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) som tillämpas när användarna försöker logga in på ett visst program

-   **Visa behörigheter** – visa något av de [OAuth2-behörigheter](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) ett program har åtkomst till i din katalog från en enda placera

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Enkel inloggning och etablera lägen som stöds av specifika programtyper

I följande tabell beskrivs de olika enkla inloggning och etablering lägena stöds av var och en av de föregående programtyperna. Du kan använda den här tabellen för att hjälpa dig att förstå vilket program som du behöver lägga till stöd för ett specifikt mål.

  ![Tabell för App-typer](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Så här väljer du ett läge för enkel inloggning

Följande är de stöds **enkel inloggning** lägen för Azure AD-program.

-   **Azure AD enkel inloggning inaktiverad** – Välj Azure AD enkel inloggning inaktiverad **läge för enkel inloggning** om du ännu inte är redo att integrera programmet med enkel inloggning med Azure AD, eller också kan du bara testar det.

-   **Länkad inloggning** – Välj den [inloggning länkade](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) **läge för enkel inloggning** om du har ett program som redan är kopplad till en befintlig enkel inloggning lösning, eller om du bara vill Publicera en enkel länk för dina användare i deras [Programåtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) eller [startprogrammet för Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

-   **Lösenordsbaserad inloggning** – Välj den [lösenordsbaserad inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) **läge för enkel inloggning** om ditt program visas med ett HTML-användarnamn och lösenord fält och du vill lagra det aktuella användarnamnet och lösenord på ett säkert sätt som ska återupprepas till programmet senare

-   **SAML-baserad inloggning** – Välj den [SAML-baserad inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) enkel inloggning om ditt program har stöd för SAML- eller OpenID Connect-protokoll eller om du vill för att kunna mappa användare till specifika programroller baserat på regler Du definierar i SAML-anspråk *

   >[!NOTE]
   >Det här alternativet är inte tillgängligt när application proxy har konfigurerats för ett program.
   >
   >

-   **Rubrikbaserad inloggning** – Välj det här [rubrikbaserad inloggning](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) enkel inloggningsläge om du har ett program med PingAccess som har stöd för HTTP-huvud-baserad autentisering som du vill utföra enkel inloggning till 

   >[!NOTE]
   >Det här alternativet är bara tillgängligt om application proxy och PingAccess är konfigurerad för ett program.
   >
   >

-   **Integrerad Windows-autentisering** – Välj den [integrerad Windows-autentisering](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) enkel inloggning när exponerar ett lokalt WIA program som du vill utföra enkel inloggning till 

   >[!NOTE]
   >Det här alternativet är bara tillgängligt när application proxy har konfigurerats för ett program.
   >
   >

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Enkel inloggning-lägen för egenutvecklade program

Program som du har anpassade utvecklat genom egenutvecklat program får också stöd för ytterligare enkel inloggning för lägena inte tidigare, bland annat:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) baserad inloggning

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) baserad inloggning

-   [WS-Federation 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) baserad inloggning

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

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigeringsmenyn.

## <a name="how-to-choose-a-provisioning-mode"></a>Så här väljer du en Etableringsläge

-   **Manuell etablering** – Välj den [manuell](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) Etableringsläge om du har befintliga konton eller vill hantera konton för det här programmet utanför Azure AD.

-   **Automatisk etablering** – Välj den [automatisk](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) **Etableringsläge** om du vill aktivera automatisk API-baserad etablering och/eller inaktivering av användarkonton till det här programmet 

   >[!NOTE]
   >Det här alternativet är endast tillgänglig för program i den **aktuellt** kategorin för den [Azure AD-Programgalleriet](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal).
   >
   >

-   **SCIM-baserad Automatisk etablering** – använda [SCIM-baserad Automatisk etablering](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) om programmet stöder SCIM-protokollet för att upptäcka ändringar till användare och grupper som genereras automatiskt för ändringar alla program som integreras med Azure AD 

   >[!NOTE]
   >Det här alternativet visas inte som en specifik Etableringsläge, men är aktiverad som standard för alla program som är integrerade med Azure AD.
   >
   >

## <a name="how-to-set-an-applications-provisioning-mode"></a>Etableringsläge för hur du ställer in ett program

Ange ett programs **etablering** läge, följer du dessa instruktioner:

Ange ett programs **enkel inloggning** läge, följer du dessa instruktioner:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera etablering.

7.  När programmet har lästs in klickar du på **etablering** från programmets vänstra navigeringsmenyn.

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
