---
title: 'Självstudiekurs: Azure Active Directory-integrering med SharePoint lokalt | Microsoft-dokument'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och lokal SharePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 22b673ef481593247b6ee1007c13390a498c66be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048616"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Lokala SharePoint

I den här självstudien får du lära dig hur du integrerar SharePoint lokalt med Azure Active Directory (Azure AD). När du integrerar Lokalt SharePoint med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SharePoint lokalt.
* Aktivera dina användare så att de automatiskt loggas in på SharePoint lokalt med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* SharePoint lokalt enkel inloggning (SSO) aktiverat prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Lokal SharePoint-stöder **SP** -initierad enkel inloggning
* När du har konfigurerat SharePoint lokalt kan du framtvinga sessionskontroller som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
* Se den här [länken](https://docs.microsoft.com/archive/blogs/kaevans/sharepoint-2013-user-profile-sync-for-claims-users) om du vill lära dig hur du synkroniserar användarprofiler från Lokalt sharepoint till Azure AD

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Lägga till lokal SharePoint från galleriet

Om du vill konfigurera integrering av lokal SharePoint i Azure AD så behöver du lägga till lokal SharePoint från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.

    > [!NOTE]
    > Om elementet inte ska vara tillgängligt kan det också öppnas via länken **Alla tjänster** högst upp på den vänstra navigeringspanelen. I följande översikt finns **Azure Active Directory-länken** i avsnittet **Identitet** eller genom att använda filtertextrutan.

1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **lokalt SharePoint i** sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Lokalt SharePoint från** resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sharepoint-on-premises"></a>Konfigurera och testa azure AD-enkel inloggning för lokalt SharePoint

Konfigurera och testa Azure AD SSO med SharePoint lokalt med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i SharePoint lokalt.

Om du vill konfigurera och testa Azure AD SSO med sharepoint lokalt slutför du följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för lokal SharePoint](#configure-sharepoint-on-premises-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Skapa en Azure AD-säkerhetsgrupp i Azure-portalen](#create-an-azure-ad-security-group-in-the-azure-portal)** – för att aktivera en ny säkerhetsgrupp i Azure AD för enkel inloggning.
5. **[Bevilja åtkomst till SharePoint lokalt säkerhetsgrupp](#grant-access-to-sharepoint-on-premises-security-group)** – bevilja åtkomst för en viss grupp till Azure AD.
6. **[Tilldela Azure AD-säkerhetsgruppen i Azure-portalen](#assign-the-azure-ad-security-group-in-the-azure-portal)** - för att tilldela den specifika gruppen till Azure AD för autentisering.
7. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med lokal SharePoint:

1. I [Azure Portal](https://portal.azure.com/), på programintegreringssidan för **lokal SharePoint** så väljer du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:


    a. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Skriv en URL med följande mönster i rutan **Identifierare:**`urn:sharepoint:federation`

    c. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [supportteamet för den lokala SharePoint-klienten](https://support.office.com/) för att få de här värdena. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

    > [!Note]
    > Skriv ned sökvägen till filen som du har hämtat certifikatfilen för eftersom du behöver använda den senare i PowerShell-skriptet för konfigurationen.

6. I avsnittet **Konfigurera lokal SharePoint** kopierar du en eller flera lämpliga URL:er efter behov. Som **URL för enkel inloggningstjänsten** använder du ett värde med följande mönster: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ är klient-id för Azure Ad-prenumerationen.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

    > [!NOTE]
    > Lokal SharePoint-programmet använder en SAML 1.1-token så Azure AD förväntar sig en WS Fed-begäran från SharePoint-servern och efter autentisering så utfärdar den SAML 1.1. token.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>Konfigurera enkel inloggning för lokal SharePoint

1. I ett annat webbläsarfönster loggar du in på din lokala SharePoint-webbplats som administratör.

2. **Konfigurera en ny betrodd identitetsprovider i SharePoint Server 2016**

    Logga in på SharePoint Server 2016-servern och öppna hanteringsgränssnittet för SharePoint 2016. Fyll i värdena för $realm (Identifierarvärde från den lokala SharePoint-domänen och URL:er-avsnittet i Azure portal), $wsfedurl (URL för enkel inloggningstjänsten) och $filepath (sökväg som du har hämtat certifikatfilen till) från Azure-portalen och kör följande kommandon för att konfigurera en ny betrodd identitetsprovider.

    > [!TIP]
    > Om PowerShell är nytt för dig eller om du vill veta mer om hur PowerShell fungerar, se [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4,$map5 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Därefter följer du de här stegen för att aktivera den betrodda identitetsprovidern för ditt program:

    a. I Central Administration går du till **Hantera webbprogram** och väljer det webbprogram som du vill skydda med Azure AD.

    b. I menyfliksområdet klickar du på **Autentiseringsproviders** och väljer den zon som du vill använda.

    c. Välj **Betrodd identitetsprovider** och välj den identitetsprovider som du just registrerade med namnet *AzureAD*.

    d. På inloggningssidans URL-inställningen väljer du **Anpassad inloggningssida** och anger värdet ”/_trust/”.

    e. Klicka på **OK**.

    ![Konfigurera din autentiseringsprovider](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Vissa av de externa användarna kommer inte att kunna använda den här integreringen för enkel inloggning eftersom deras UPN kommer att ha ett manglat värde som liknar `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Snart kommer vi att tillåta kunders appkonfiguration om hur UPN ska hanteras beroende på användartyp. Efter det borde alla dina gästanvändare kunna använda enkel inloggning sömlöst som anställda inom organisationen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Skapa en Azure AD-säkerhetsgrupp i Azure-portalen

1. Klicka på **Azure Active Directory > Alla grupper**.

    ![Skapa en Azure AD-säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Klicka på **Ny grupp:**

    ![Skapa en Azure AD-säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Fyll i **grupptyp,** **Gruppnamn,** **Gruppbeskrivning**, **Medlemstyp**. Klicka på pilen för att välja medlemmar, sedan söka efter eller klicka på den medlem du vill lägga till i gruppen. Klicka på **Välj** om du vill lägga till de markerade medlemmarna och klicka sedan på **Skapa**.

    ![Skapa en Azure AD-säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > För att tilldela Azure Active Directory Security Groups till SharePoint lokalt, är det nödvändigt att installera och konfigurera [AzureCP](https://yvand.github.io/AzureCP/) i den lokala SharePoint-servergruppen ELLER utveckla och konfigurera en alternativ anpassad anspråksprovider för SharePoint.  Se avsnittet mer information i slutet av dokumentet för att skapa din egen anpassade anspråksleverantör, om du inte använder AzureCP.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Bevilja åtkomst till den lokala säkerhetsgruppen SharePoint

**Konfigurera säkerhetsgrupper och behörigheter för appregistreringen**

1. I Azure-portalen väljer du **Azure Active Directory**och väljer sedan **Appregistreringar**.

    ![Bladet Företagsprogram](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. Skriv och välj Lokalt **SharePoint i**sökrutan .

    ![Lokal SharePoint i resultatlistan](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Klicka på **Manifest .**

    ![Alternativet Manifest](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Ändra `groupMembershipClaims` `NULL`: `groupMembershipClaims`, `SecurityGroup`Till : . Klicka sedan på Spara

    ![Redigera manifest](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Klicka på **Inställningar**och klicka sedan på **Obligatoriska behörigheter**.

    ![Nödvändiga behörigheter](./media/sharepoint-on-premises-tutorial/settings.png)

6. Klicka på **Lägg till** och välj sedan **ett API**.

    ![API-åtkomst](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Lägg till både **Windows Azure Active Directory** och Microsoft Graph **API**, men det är bara möjligt att välja en i taget.

    ![API-val](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Välj Windows Azure Active Directory, kontrollera Läs katalogdata och klicka på Välj. Gå tillbaka och lägg till Microsoft Graph och välj Läs katalogdata för det också.  Klicka på Välj och klicka på Klar.

    ![Aktivera åtkomst](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Klicka nu på Bevilja **behörigheter** under Obligatoriska inställningar och klicka sedan på Ja för att bevilja behörigheter.

    ![Bevilja behörigheter](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Kontrollera under meddelanden för att avgöra om behörigheterna har beviljats.  Om de inte är det kommer AzureCP inte att fungera korrekt och det går inte att konfigurera SharePoint lokalt med Azure Active Directory-säkerhetsgrupper.

10. Konfigurera AzureCP i den lokala SharePoint-servergruppen eller en alternativ anpassad anspråksproviderlösning.  I det här exemplet använder vi AzureCP.

    > [!NOTE]
    > Observera att AzureCP inte är en Microsoft-produkt eller stöds av Microsofts tekniska support. Ladda ned, installera och konfigurera AzureCP i den lokala SharePoint-servergruppen per[https://yvand.github.io/AzureCP/](https://yvand.github.io/AzureCP/) 

11. **Bevilja åtkomst till Azure Active Directory Security Group i den lokala SharePoint** :- Grupperna måste beviljas åtkomst till programmet i SharePoint lokalt.  Använd följande steg för att ange behörigheter för åtkomst till webbprogrammet.

12. I Central Administration klickar du på Application Management, Hantera webbprogram, väljer sedan webbprogrammet för att aktivera menyfliksområdet och klicka på Användarprincip.

    ![Central Administration](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. Under Princip för webbprogram klickar du på Lägg till användare och väljer sedan zonen och klickar på Nästa.  Klicka på adressboken.

    ![Princip för webbprogram](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Sök sedan efter och lägg till Azure Active Directory Security Group och klicka på OK.

    ![Lägga till säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Markera behörigheterna och klicka sedan på Slutför.

    ![Lägga till säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Se under Princip för webbprogram läggs Azure Active Directory-gruppen till.  Gruppanspråket visar Azure Active Directory Security Group Object ID för användarnamnet.

    ![Lägga till säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Bläddra till SharePoint-webbplatssamlingen och lägg till gruppen där också. Klicka på Webbplatsinställningar och sedan på Webbplatsbehörigheter och bevilja behörigheter.  Sök efter anspråk på grupproll, tilldela behörighetsnivån och klicka på Dela.

    ![Lägga till säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurera en betrodd identitetsprovider för flera webbprogram

Konfigurationen fungerar för en enkel webbapp, men ytterligare konfiguration krävs om du planerar att använda samma betrodda identitetsprovider för flera webbprogram. Anta exempelvis att vi hade utökat ett webbprogram till att använda URL:en `https://portal.contoso.local` och nu också vill autentisera användare till `https://sales.contoso.local`. För att göra det så måste vi uppdatera identitetsprovider för att respektera parametern WReply och uppdatera programregistreringen i Azure AD för att lägga till en svars-URL.

1. Öppna Azure AD-katalogen i Azure-portalen. Klicka på **Appregistreringar** och därefter på **Visa alla program**. Klicka på det program som du skapade tidigare (SharePoint SAML-integrering).

2. Klicka på **Inställningar**.

3. På inställningar-bladet, klickar du på **Svars-URL:er**.

4. Lägg till URL:en för ytterligare webbprogram med `/_trust/default.aspx` tillagt till URL:en (till exempel `https://sales.contoso.local/_trust/default.aspx`) och klicka på **Spara**.

5. På SharePoint-servern öppnar du **Hanteringsgränssnittet för SharePoint 2016** och kör följande kommandon med namnet på den betrodda identitetstokenutfärdare som du använde tidigare.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```

6. I Central Administration går du till webbprogrammet och aktiverar den befintliga betrodda identitetsprovidern. Kom ihåg att även konfigurera inloggningssidans URL som en anpassad inloggningssida `/_trust/`.

7. I Central Administration, klickar du på webbprogrammet och väljer **Användarprincip**. Lägg till en användare med lämplig behörighet som det visas tidigare i den här artikeln.

### <a name="fixing-people-picker"></a>Åtgärda personväljaren

Användare kan nu logga in på SharePoint 2016 med identiteter från Azure AD, men det finns fortfarande möjligheter till förbättringar av användarupplevelsen. Om du till exempel söker efter en användare så visas flera sökresultat i personväljaren. Det finns ett sökresultat för var och en av de 3 anspråkstyperna som skapades i anspråksmappningen. Om du vill välja en användare med personväljaren så måste du ange deras användarnamn exakt och välja anspråksresultatet **Name**.

![Anspråkssökresultat](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Det finns ingen validering på de värden som du söker efter, vilket kan leda till felstavningar eller att användare väljer fel anspråkstyp att tilldela som anspråket **SurName**. Det kan förhindra att användare får åtkomst till resurserna.

För att hjälpa till vid det här scenariot så finns det en öppen källkodslösning som heter [AzureCP](https://yvand.github.io/AzureCP/) som ger en anpassad anspråksprovider för SharePoint 2016. Det kommer att använda Microsoft Graph API för att lösa vad användare anger och utför validering. Läs mer på [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Tilldela Azure AD-säkerhetsgruppen i Azure-portalen

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och därefter **lokal SharePoint**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan skriver och väljer du **lokal SharePoint**.

    ![Länken till lokal SharePoint i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på **Lägg till användare**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. Sök efter den säkerhetsgrupp som du vill använda och klicka sedan på gruppen för att lägga till den i avsnittet Välj medlemmar. Klicka på **Markera**och sedan på **Tilldela**.

    ![Sök i säkerhetsgruppen](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Kontrollera meddelandena i menyraden för att få ett meddelande om att gruppen har tilldelats Enterprise-programmet i Azure-portalen.

### <a name="create-sharepoint-on-premises-test-user"></a>Skapa lokal SharePoint-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i lokal SharePoint. Arbeta med [sharepoint lokalt supportteam](https://support.office.com/) för att lägga till användarna i den lokala SharePoint-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på lokal SharePoint-panelen i Åtkomstpanelen så bör du automatiskt loggas in på den lokala SharePoint för vilken du konfigurerade enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)