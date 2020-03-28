---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med F5 | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9c5fb47a-1c5d-437a-b4c1-dbf739eaf5e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 984fd0c7946a50922315269c87e08b1c35b74348
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74074765"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med F5

I den här självstudien får du lära dig hur du integrerar F5 med Azure Active Directory (Azure AD). När du integrerar F5 med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till F5.
* Gör att användarna automatiskt loggas in på F5 med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* F5 enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* F5 stöder **SP och IDP** initierad SSO
* F5 SSO kan konfigureras på tre olika sätt.

- [Konfigurera F5 enkel inloggning för Avancerat Kerberos-program](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Konfigurera F5 enkel inloggning för header-baserat program](headerf5-tutorial.md)

- [Konfigurera F5 enkel inloggning för Kerberos-program](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Lägga till F5 från galleriet

Om du vill konfigurera integreringen av F5 i Azure AD måste du lägga till F5 från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **F5** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **F5** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Konfigurera och testa en enkel Azure AD-inloggning för F5

Konfigurera och testa Azure AD SSO med F5 med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i F5.

Så här konfigurerar och testar du Azure AD SSO med F5:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera F5-SSO](#configure-f5-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa F5-testanvändare](#create-f5-test-user)** – om du vill ha en motsvarighet till B.Simon i F5 som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<YourCustomFQDN>.f5.com/`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<YourCustomFQDN>.f5.com/`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [F5 Client support team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera F5** baserat på dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till F5.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **F5**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-f5-sso"></a>Konfigurera F5 SSO

- [Konfigurera F5 enkel inloggning för header-baserat program](headerf5-tutorial.md)

- [Konfigurera F5 enkel inloggning för Kerberos-program](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Konfigurera F5 enkel inloggning för Avancerat Kerberos-program

1. Öppna ett nytt webbläsarfönster och logga in på din F5-företagswebbplats (Advanced Kerberos) som administratör och utför följande steg:

1. Du måste importera metadatacertifikatet till F5 (Advanced Kerberos) som kommer att användas senare i installationsprocessen. Gå till **System > Certifikathantering > trafikcertifikathantering >> SSL-certifikatlista**. Klicka på **Importera** det högra hörnet.

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Om du vill konfigurera SAML IDP går du till **Access > Federation > SAML Service Provider > Skapa > från metadata**.

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure02.png)

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure04.png)

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Ange det certifikat som överförs från aktivitet 3

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure06.png)

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure07.png)

 1. Om du vill konfigurera SAML SP går du till **Access > Federation > SAML Service Federation > Local SP Services > Create**.

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Klicka på **OK**.

1. Välj SP-konfigurationen och klicka på **Bind/UnBind IdP-kopplingar**.

     ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Klicka på **Lägg till ny rad** och välj den externa **IdP-koppling** som skapades i föregående steg.

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure10.png)
 
1. För att konfigurera Kerberos SSO, **Access > Enkel inloggning > Kerberos**

    >[!Note]
    >Du måste skapa och angiven kerberos-delegeringskonto. Se KCD-avsnittet (se tillägg för variabelreferenser)

    • Användarnamn Källa`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    • Källa till användarskapet`session.logon.last.domain`

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure11.png)

1. För att konfigurera **åtkomstprofil, access > profil/principer > åtkomstprofil (per sessionsprinciper)**.

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure12.png)

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure13.png)

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure14.png)

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure15.png)

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session.logon.last.usernameUPN-expr {[mcget {session.saml.last.identity}]}

    * session.ad.lastactualdomain TEXT superdemo.live

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName=%{session.logon.last.usernameUPN})

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure18.png)

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure19.png)

    * session.logon.last.username expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session.logon.last.username}
    * mcget {session.logon.last.password}

1. Om du vill lägga till ny nod går du till **Lokal trafik > noder > nodlista > +**.

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Om du vill skapa en ny pool går du till **Lokal trafik > pooler > poollista > Skapa**.

     ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure22.png)

 1. Om du vill skapa en ny virtuell server går du till **Lokal trafik > virtuella servrar > virtual server list > +**.

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure23.png)

1. Ange den Åtkomstprofil som skapats i föregående steg.

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Ställa in Kerberos-delegering 

>[!Note]
>För mer information se [här](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

* **Steg 1: Skapa ett delegeringskonto**

    * Exempel
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **Steg 2: Ange SPN (på APM-delegeringskontot)**

    *  Exempel
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **Steg 3: SPN-delegering ( för apptjänstkontot)**

    * Ställ in lämplig delegering för F5-kontot för delegering.
    * I exemplet nedan konfigureras APM-delegeringskontot för KCD för FRP-App1.superdemo.live-appen.

        ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure25.png)

1. Lämna de uppgifter som nämns i ovannämnda referensdokument enligt [detta](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

1. Tillägg- SAML - F5 BIG-IP Variabel mappningar som visas nedan:

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure26.png)

    ![F5-konfiguration (Avancerad Kerberos)](./media/advance-kerbf5-tutorial/configure27.png) 

1. Nedan finns hela listan över standard SAML-attribut. GivenName representeras med följande sträng.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| | |
| -- | -- |
| eb46b6b6.session.saml.last.assertionID | `<TENANT ID>` |
| eb46b6b6.session.saml.last.assertionIssueInstant  | `<ID>` |
| eb46b6b6.session.saml.last.assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/displayname | användare0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | användare0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/name | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6.session.saml.last.audience | `https://kerbapp.superdemo.live` |
| eb46b6b6.session.saml.last.authNContextClassRef | urn:oasis:names:tc:SAML:2.0:ac:classes:Password |
| eb46b6b6.session.saml.last.authNInstant | `<ID>` |
| eb46b6b6.session.saml.last.identity | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.inResponseTo | `<TENANT ID>` |
| eb46b6b6.session.saml.efternamn.nameIDValue | `user0@superdemo.live` |
| eb46b6b6.session.saml.efternamn.nameIdFormat | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| eb46b6b6.session.saml.last.responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.responseId | `<TENANT ID>` |
| eb46b6b6.session.saml.last.responseIssueInstant | `<ID>` |
| eb46b6b6.session.saml.last.responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.result | 1 |
| eb46b6b6.session.saml.last.samlVersion | 2.0 |
| eb46b6b6.session.saml.last.sessionIndex | `<TENANT ID>` |
| eb46b6b6.session.saml.last.statusVärdera | urn:oasis:names:tc:SAML:2.0:status:Framgång |
| eb46b6b6.session.saml.last.subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6.session.saml.last.subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.subjectConfirmMethod | urn:oasis:names:tc:SAML:2.0:cm:bärare |
| eb46b6b6.session.saml.last.validityNotBefore | `<ID>` |
| eb46b6b6.session.saml.last.validityNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>Skapa F5-testanvändare

I det här avsnittet skapar du en användare som heter B.Simon i F5. Arbeta med [F5 Client support team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) för att lägga till användarna i F5-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på F5-panelen på åtkomstpanelen ska du automatiskt loggas in på den F5 som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova F5 med Azure AD](https://aad.portal.azure.com/)

- [Konfigurera F5 enkel inloggning för header-baserat program](headerf5-tutorial.md)

- [Konfigurera F5 enkel inloggning för Kerberos-program](kerbf5-tutorial.md)

