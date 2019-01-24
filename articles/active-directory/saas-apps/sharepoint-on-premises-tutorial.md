---
title: 'Självstudier: Azure Active Directory-integrering med lokal SharePoint | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och lokal SharePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: bfba45018ff15864e6a1e9a745358dc1233b8104
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825501"
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

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Lokal SharePoint-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Lokal SharePoint-stöder **SP** -initierad enkel inloggning

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Lägga till lokal SharePoint från galleriet

Om du vill konfigurera integrering av lokal SharePoint i Azure AD så behöver du lägga till lokal SharePoint från galleriet i din lista över hanterade SaaS-appar.

**Lägg till lokal SharePoint från galleriet genom att utföra följande steg:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

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
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Bevilja åtkomst till lokal SharePoint-testanvändare](#grant-access-to-sharepoint-on-premises-test-user)** – för att få en motpart till Britta Simon i lokal SharePoint som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

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
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en, identifierare och svars-URL. Kontakta [supportteamet för den lokala SharePoint-klienten](https://support.office.com/) för att få de här värdena. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

    > [!Note]
    > Notera sökvägen till filen där du laddade ned certifikatfilen. Du behöver filen senare i PowerShell-skriptet för konfigurationen.

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

1. I ett annat webbläsarfönster loggar du in på din företagswebbplats för lokal SharePoint som en administratör.

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
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Därefter följer du de här stegen för att aktivera den betrodda identitetsprovidern för ditt program:

    a. I Central Administration går du till **Hantera webbprogram** och väljer det webbprogram som du vill skydda med Azure AD.

    b. I menyfliksområdet klickar du på **Autentiseringsproviders** och väljer den zon som du vill använda.

    c. Välj **Betrodd identitetsprovider** och välj den identitetsprovider som du just registrerade med namnet *AzureAD*.

    d. På inloggningssidans URL-inställningen väljer du **Anpassad inloggningssida** och anger värdet ”/_trust/”.

    e. Klicka på **OK**.

    ![Konfigurera din autentiseringsprovider](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Vissa externa användare kommer inte att kunna använda den här integreringen för enkel inloggning eftersom deras UPN kommer att ha ett manglat värde som `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Snart kommer vi att tillåta anpassad appkonfiguration för att hantera UPN beroende på användartyp. Efter det borde alla dina gästanvändare kunna använda enkel inloggning sömlöst som anställda inom organisationen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="grant-access-to-sharepoint-on-premises-test-user"></a>Bevilja åtkomst till lokal SharePoint-testanvändare

De användare som ska logga in på Azure AD och få åtkomst till SharePoint måste beviljas åtkomst till programmet. Använd följande steg för att ange behörigheter för att komma åt webbprogrammet.

1. I Central Administration klickar du på **Programhantering**.

2. På sidan **Programhantering** i avsnittet **Webbprogram**, klickar du på **Hantera webbprogram**.

3. Klicka på lämpligt webbprogram och klicka sedan på **Användarprincip**.

4. I Princip för webbprogram, klickar du på **Lägg till användare**.

    ![Söka efter en användare med deras namnanspråk](./media/sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. I dialogrutan **Lägg till användare**, klickar du på den aktuella zonen i **Zoner** och klickar sedan på **Nästa**.

6. I dialogrutan **Princip för webbprogram** i avsnittet **Välj användare**, klickar du på **Bläddra**-ikonen.

7. I textrutan **Hitta**, skriver du in värdet för **användarens huvudnamn (UPN)** som du har konfigurerat det lokala SharePoint-programmet i Azure AD med och klickar på **Sök**. </br>Exempel: *brittasimon@contoso.com*.

8. Under AzureAD-rubriken i listvyn så väljer du namnegenskapen och klickar på **Lägg till** och därefter på **OK** för att stänga dialogrutan.

9. I behörigheter, klickar du på **Fullständig kontroll**.

    ![Bevilja fullständig kontroll till en anspråksanvändare](./media/sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

10. Klicka på **Slutför** och klicka sedan på **OK**.

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurera en betrodd identitetsprovider för flera webbprogram

Konfigurationen fungerar för en enkel webbapp, men ytterligare konfiguration krävs om du planerar att använda samma betrodda identitetsprovider för flera webbprogram. Anta exempelvis att vi hade utökat ett webbprogram till att använda URL:en `https://portal.contoso.local` och nu också vill autentisera användare till `https://sales.contoso.local`. För att göra det så måste vi uppdatera identitetsprovider för att respektera parametern WReply och uppdatera programregistreringen i Azure AD för att lägga till en svars-URL.

1. Öppna Azure AD-katalogen i Azure Portal. Klicka på **Appregistreringar** och därefter på **Visa alla program**. Klicka på det program som du skapade tidigare (SharePoint SAML-integrering).

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

Användare kan nu logga in på SharePoint 2016 med identiteter från Azure AD, men det finns fortfarande utrymme för förbättringar av användarupplevelsen. Om du till exempel söker efter en användare så visas flera sökresultat i personväljaren. Det finns ett sökresultat för var och en av de 3 anspråkstyperna som skapades i anspråksmappningen. Om du vill välja en användare med personväljaren så måste du ange deras användarnamn exakt och välja anspråksresultatet **Name**.

![Anspråkssökresultat](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Det finns ingen validering på de värden som du söker efter, vilket kan leda till felstavningar eller att användare väljer fel anspråkstyp att tilldela som anspråket **SurName**. Det kan förhindra att användare får åtkomst till resurserna.

För att hjälpa till vid det här scenariot så finns det en öppen källkodslösning som heter [AzureCP](https://yvand.github.io/AzureCP/) som ger en anpassad anspråksprovider för SharePoint 2016. Den använder Azure AD Graph för att matcha vad användare skriver in och utföra validering. Läs mer på [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet så låter du Britta Simon använda enkel inloggning med Azure genom att ge åtkomst till lokal SharePoint.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och därefter **lokal SharePoint**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan skriver och väljer du **lokal SharePoint**.

    ![Länken till lokal SharePoint i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sharepoint-on-premises-test-user"></a>Skapa lokal SharePoint-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i lokal SharePoint. Arbeta med [supportteamet för lokal SharePoint](https://support.office.com/) för att lägga till användarna i den lokala SharePoint-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på lokal SharePoint-panelen i Åtkomstpanelen så bör du automatiskt loggas in på den lokala SharePoint för vilken du konfigurerade enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
