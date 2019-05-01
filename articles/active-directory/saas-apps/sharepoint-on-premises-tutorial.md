---
title: 'Självstudier: Azure Active Directory-integrering med lokal SharePoint | Microsoft Docs'
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
ms.devlang: na
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: jeedes
ms.openlocfilehash: 8e85f390ee5ff74f02cb95fa4dcf1dfc1a35dad1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699865"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Självstudier: Azure Active Directory-integrering med SharePoint lokalt

I den här självstudien får du lära dig hur du integrerar lokal SharePoint med Azure Active Directory (Azure AD).
Om du integrerar lokal SharePoint med Azure AD så får du följande fördelar:

* Du kan styra i Azure AD vem som har åtkomst till lokal SharePoint.
* Du kan låta dina användare loggas in automatiskt på lokal SharePoint (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med lokal SharePoint så behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Lokal SharePoint-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Lokal SharePoint-stöder **SP** -initierad enkel inloggning

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Lägga till lokal SharePoint från galleriet

Om du vill konfigurera integrering av lokal SharePoint i Azure AD så behöver du lägga till lokal SharePoint från galleriet i din lista över hanterade SaaS-appar.

**Lägg till lokal SharePoint från galleriet genom att utföra följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du in **lokal SharePoint**, väljer **lokal SharePoint-** från resultatpanelen och klickar därefter på **Lägg till** för att lägga till programmet.

    ![Lokal SharePoint i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med lokal SharePoint baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation etableras mellan en Azure AD-användare och den relaterade användaren i lokal SharePoint.

Om du vill konfigurera och testa Azure AD enkel inloggning med lokal SharePoint så måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för lokal SharePoint](#configure-sharepoint-on-premises-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Skapa en Azure AD-säkerhetsgrupp i Azure-portalen](#create-an-azure-ad-security-group-in-the-azure-portal)**  – om du vill aktivera en ny säkerhetsgrupp i Azure AD för enkel inloggning.
5. **[Bevilja åtkomst till SharePoint-lokal säkerhetsgrupp](#grant-access-to-sharepoint-on-premises-security-group)**  – bevilja åtkomst för en viss grupp till Azure AD.
6. **[Tilldela Azure AD-säkerhetsgruppen i Azure-portalen](#assign-the-azure-ad-security-group-in-the-azure-portal)**  – om du vill tilldela gruppen till Azure AD för autentisering.
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

    ![Enkel inloggningsinformation för lokal SharePoint-domän och URL:er](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. I rutan **Identifierare** skriver du en URL med följande mönster: `urn:sharepoint:federation`

    c. I textrutan **svars-URL** skriver du en URL med följande mönster: `https://<YourSharePointServerURL>/_trust/default.aspx`

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

1. I ett annat webbläsarfönster, loggar du in till SharePoint-webbplatsen lokala företag som administratör.

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

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Skapa en Azure AD-säkerhetsgrupp i Azure portal

1. Klicka på **Azure Active Directory > alla grupper**.

    ![Skapa en Azure AD-säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Klicka på **ny grupp**:

    ![Skapa en Azure AD-säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Fyll i **grupptyp**, **gruppnamn**, **Gruppbeskrivning**, **Medlemskapstyp**. Klicka på pilen för att välja medlemmar, och sedan söka efter eller klicka på medlemmen som du vill lägga till i gruppen. Klicka på **Välj** för att lägga till de markerade medlemmarna, klicka sedan på **skapa**.

    ![Skapa en Azure AD-säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > För att kunna tilldela Azure Active Directory-säkerhetsgrupper till SharePoint lokalt, kan det vara nödvändigt att installera och konfigurera [AzureCP](https://yvand.github.io/AzureCP/) i en lokal SharePoint-servergrupp eller utveckla och konfigurera en alternativ anpassade anspråk Provider för SharePoint.  Se avsnittet med mer information i slutet av dokumentet för att skapa dina egna anpassade anspråksprovidern om du inte använder AzureCP.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Bevilja åtkomst till SharePoint-lokal säkerhetsgrupp

**Konfigurera säkerhetsgrupper och behörigheter på Appregistreringen**

1. I Azure-portalen väljer du **Azure Active Directory**och välj sedan **appregistreringar**.

    ![Bladet Företagsprogram](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. Skriv i sökrutan och välj **lokal SharePoint-**.

    ![Lokal SharePoint i resultatlistan](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Klicka på **Manifest**.

    ![Manifest alternativet](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Ändra `groupMembershipClaims`: `NULL`till `groupMembershipClaims`: `SecurityGroup`. Klicka på Spara

    ![Redigera Manifest](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Klicka på **inställningar**, klicka sedan på **behörigheter som krävs för**.

    ![Nödvändiga behörigheter](./media/sharepoint-on-premises-tutorial/settings.png)

6. Klicka på **lägga till** och sedan **Välj en API**.

    ![API-åtkomst](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Lägga till båda **Windows Azure Active Directory** och **Microsoft Graph API**, men det går bara att välja en i taget.

    ![Välj API](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Välj Windows Azure Active Directory, kontrollera läsa kataloginformation och klicka på Välj. Gå tillbaka och lägga till Microsoft Graph och välja läsa kataloginformation för den, samt.  Klicka på Välj och klicka på klar.

    ![Aktivera åtkomst](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Nu under krävs inställningar klickar du på **bevilja** och sedan klicka på Ja om du vill bevilja behörigheter.

    ![Bevilja behörigheter](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Kontrollera under meddelanden att avgöra om behörigheterna som har beviljats.  Om de inte är AzureCP fungerar inte och det går inte att konfigurera lokal SharePoint med Azure Active Directory-säkerhetsgrupper.

10. Konfigurera AzureCP på en lokal SharePoint-servergruppen eller alternativa anpassade anspråk providern lösning.  I det här exemplet använder vi AzureCP.

    > [!NOTE]
    > Observera att AzureCP är inte en Microsoft-produkt eller stöds av Microsofts tekniska Support. Hämta, installera och konfigurera AzureCP på en lokal SharePoint-servergruppen per https://yvand.github.io/AzureCP/ 

11. **Bevilja åtkomst till den Azure Active Directory-säkerhetsgrupp i en lokal SharePoint** :-gruppen måste beviljas åtkomst till programmet i lokal SharePoint.  Använd följande steg för att ange behörighet för att få åtkomst till webbprogrammet.

12. Klicka på programhantering, hantera webbprogram och välj webbprogram för att aktivera menyfliksområdet och klicka på användarprincip i Central Administration.

    ![Central Administration](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. Under princip för webbprogram, klickar du på Lägg till användare och sedan välja zonen, klicka på Nästa.  Klicka på adressboken.

    ![Princip för webbprogram](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Sedan söka efter och lägga till den Azure Active Directory-säkerhetsgrupp och klicka på OK.

    ![Lägger till säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Välj behörigheterna och klicka på Slutför.

    ![Lägger till säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Se under princip för webbprogram, Azure Active Directory-grupper har lagts till.  Gruppanspråket visas i Azure Active Directory Security gruppobjekt-ID för användarnamnet.

    ![Lägger till säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Bläddra till SharePoint-webbplatssamling och Lägg till gruppen det, samt. Klicka på inställningar och sedan på plats och bevilja behörigheter.  Sök efter grupp rollanspråk, tilldela behörighetsnivån och klicka på Dela.

    ![Lägger till säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurera en betrodd identitetsprovider för flera webbprogram

Konfigurationen fungerar för en enkel webbapp, men ytterligare konfiguration krävs om du planerar att använda samma betrodda identitetsprovider för flera webbprogram. Anta exempelvis att vi hade utökat ett webbprogram till att använda URL:en `https://portal.contoso.local` och nu också vill autentisera användare till `https://sales.contoso.local`. För att göra det så måste vi uppdatera identitetsprovider för att respektera parametern WReply och uppdatera programregistreringen i Azure AD för att lägga till en svars-URL.

1. Öppna Azure AD-katalog i Azure-portalen. Klicka på **Appregistreringar** och därefter på **Visa alla program**. Klicka på det program som du skapade tidigare (SharePoint SAML-integrering).

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

Användare kan nu logga in på SharePoint 2016 med hjälp av identiteter från Azure AD, men det finns fortfarande möjligheter till förbättring av användarupplevelsen. Om du till exempel söker efter en användare så visas flera sökresultat i personväljaren. Det finns ett sökresultat för var och en av de 3 anspråkstyperna som skapades i anspråksmappningen. Om du vill välja en användare med personväljaren så måste du ange deras användarnamn exakt och välja anspråksresultatet **Name**.

![Anspråkssökresultat](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Det finns ingen validering på de värden som du söker efter, vilket kan leda till felstavningar eller att användare väljer fel anspråkstyp att tilldela som anspråket **SurName**. Det kan förhindra att användare får åtkomst till resurserna.

För att hjälpa till vid det här scenariot så finns det en öppen källkodslösning som heter [AzureCP](https://yvand.github.io/AzureCP/) som ger en anpassad anspråksprovider för SharePoint 2016. Den använder Azure AD Graph för att matcha vad användare skriver in och utföra validering. Läs mer på [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Tilldela Azure AD-säkerhetsgruppen i Azure portal

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och därefter **lokal SharePoint**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan skriver och väljer du **lokal SharePoint**.

    ![Länken till lokal SharePoint i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på den **Lägg till användare**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. Sök efter den säkerhetsgrupp som du vill använda, och sedan klicka på gruppen för att lägga till den i avsnittet väljer medlemmar. Klicka på **Välj**, klicka sedan på **tilldela**.

    ![Sök säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Kontrollera meddelandena på menyraden meddelas att gruppen har tilldelats till företagsprogram i Azure-portalen.

### <a name="create-sharepoint-on-premises-test-user"></a>Skapa lokal SharePoint-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i lokal SharePoint. Arbeta med [supportteamet för lokal SharePoint](https://support.office.com/) för att lägga till användarna i den lokala SharePoint-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på lokal SharePoint-panelen i Åtkomstpanelen så bör du automatiskt loggas in på den lokala SharePoint för vilken du konfigurerade enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
