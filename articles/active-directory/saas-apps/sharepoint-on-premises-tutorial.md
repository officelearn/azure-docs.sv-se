---
title: 'Tutorial: Azure Active Directory integration with SharePoint on-premises | Microsoft Docs'
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
ms.openlocfilehash: 7feb62bb3e38452a441c505107569457d7c90a3f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233455"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Tutorial: Azure Active Directory integration with SharePoint on-premises

I den här självstudien får du lära dig hur du integrerar lokal SharePoint med Azure Active Directory (Azure AD).
Om du integrerar lokal SharePoint med Azure AD så får du följande fördelar:

* Du kan styra i Azure AD vem som har åtkomst till lokal SharePoint.
* Du kan låta dina användare loggas in automatiskt på lokal SharePoint (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med lokal SharePoint så behöver du följande objekt:

* En Azure AD-prenumeration. If you don't have an Azure AD environment, you can get a [free account](https://azure.microsoft.com/free/)
* Lokal SharePoint-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Lokal SharePoint-stöder **SP** -initierad enkel inloggning

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Lägga till lokal SharePoint från galleriet

Om du vill konfigurera integrering av lokal SharePoint i Azure AD så behöver du lägga till lokal SharePoint från galleriet i din lista över hanterade SaaS-appar.

**Lägg till lokal SharePoint från galleriet genom att utföra följande steg:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

    > [!NOTE]   
    > If the element should not be available, it can also be opened through the fixed **All services** link at the top of the left navigation panel. In the following overview, the **Azure Active Directory** link is located in the **Identity** section or it can be searched for by using the filter text box.

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
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Create an Azure AD Security Group in the Azure portal](#create-an-azure-ad-security-group-in-the-azure-portal)** - to enable a new security group in Azure AD for single sign-on.
5. **[Grant access to SharePoint on-premises Security Group](#grant-access-to-sharepoint-on-premises-security-group)** - grant access for particular group to Azure AD.
6. **[Assign the Azure AD Security Group in the Azure portal](#assign-the-azure-ad-security-group-in-the-azure-portal)** - to assign the particular group to Azure AD for authentication.
7. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med lokal SharePoint:

1. I [Azure Portal](https://portal.azure.com/), på programintegreringssidan för **lokal SharePoint** så väljer du **Enkel inloggning**.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

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

1. In a different web browser window, sign in to your SharePoint on-premises company site as an administrator.

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

    e. Klicka på **OK**

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
  
    b. In the **User name** field type `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Create an Azure AD Security Group in the Azure portal

1. Click on **Azure Active Directory > All Groups**.

    ![Create an Azure AD Security Group](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Click **New group**:

    ![Create an Azure AD Security Group](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Fill in **Group type**, **Group name**, **Group description**, **Membership type**. Click on the arrow to select members, then search for or click on the member you will like to add to the group. Click on **Select** to add the selected members, then click on **Create**.

    ![Create an Azure AD Security Group](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > In order to assign Azure Active Directory Security Groups to SharePoint on-premises, it will be necessary to install and configure [AzureCP](https://yvand.github.io/AzureCP/) in the on-premises SharePoint farm OR develop and configure an alternative custom claims provider for SharePoint.  See the more information section at the end of the document for creating your own custom claims provider, if you don’t use AzureCP.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Grant access to SharePoint on-premises Security Group

**Configure Security Groups and Permissions on the App Registration**

1. In the Azure portal, select **Azure Active Directory**, then select **App registrations**.

    ![Bladet Företagsprogram](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. In the search box, type and select **SharePoint on-premises**.

    ![Lokal SharePoint i resultatlistan](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Click on **Manifest**.

    ![Manifest option](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Modify `groupMembershipClaims`: `NULL`, To `groupMembershipClaims`: `SecurityGroup`. Then, click on Save

    ![Edit Manifest](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Click on **Settings**, then click on **Required permissions**.

    ![Nödvändiga behörigheter](./media/sharepoint-on-premises-tutorial/settings.png)

6. Click on **Add** and then **Select an API**.

    ![API Access](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Add both **Windows Azure Active Directory** and **Microsoft Graph API**, but it’s only possible to select one at a time.

    ![API Select](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Select Windows Azure Active Directory, check Read directory data and click on Select. Go back and add Microsoft Graph and select Read directory data for it, as well.  Click on Select and click on Done.

    ![Enable Access](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Now, under Required Settings, click on **Grant permissions** and then Click Yes to Grant permissions.

    ![Grant Permissions](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Check under notifications to determine if the permissions were successfully granted.  If they are not, then the AzureCP will not work properly and it won’t be possible to configure SharePoint on-premises with Azure Active Directory Security Groups.

10. Configure the AzureCP on the SharePoint on-premises farm or an alternative custom claims provider solution.  In this example, we are using AzureCP.

    > [!NOTE]
    > Please note that AzureCP is not a Microsoft product or supported by Microsoft Technical Support. Download, install and configure AzureCP on the on-premises SharePoint farm per https://yvand.github.io/AzureCP/ 

11. **Grant access to the Azure Active Directory Security Group in the on-premises SharePoint** :- The groups must be granted access to the application in SharePoint on-premises.  Use the following steps to set the permissions to access the web application.

12. In Central Administration, click on Application Management, Manage web applications, then select the web application to activate the ribbon and click on User Policy.

    ![Central Administration](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. Under Policy for Web Application, click on Add Users, then select the zone, click on Next.  Click on the Address Book.

    ![Policy for Web application](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Then, search for and add the Azure Active Directory Security Group and click on OK.

    ![Adding Security group](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Select the Permissions, then click on Finish.

    ![Adding Security group](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. See under Policy for Web Application, the Azure Active Directory Group is added.  The group claim shows the Azure Active Directory Security Group Object ID for the User Name.

    ![Adding Security group](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Browse to the SharePoint site collection and add the Group there, as well. Click on Site Settings, then click Site permissions and Grant Permissions.  Search for the Group Role claim, assign the permission level and click Share.

    ![Adding Security group](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurera en betrodd identitetsprovider för flera webbprogram

Konfigurationen fungerar för en enkel webbapp, men ytterligare konfiguration krävs om du planerar att använda samma betrodda identitetsprovider för flera webbprogram. Anta exempelvis att vi hade utökat ett webbprogram till att använda URL:en `https://portal.contoso.local` och nu också vill autentisera användare till `https://sales.contoso.local`. För att göra det så måste vi uppdatera identitetsprovider för att respektera parametern WReply och uppdatera programregistreringen i Azure AD för att lägga till en svars-URL.

1. In the Azure portal, open the Azure AD directory. Klicka på **Appregistreringar** och därefter på **Visa alla program**. Klicka på det program som du skapade tidigare (SharePoint SAML-integrering).

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

Users can now sign into SharePoint 2016 using identities from Azure AD, but there are still opportunities for improvement to the user experience. Om du till exempel söker efter en användare så visas flera sökresultat i personväljaren. Det finns ett sökresultat för var och en av de 3 anspråkstyperna som skapades i anspråksmappningen. Om du vill välja en användare med personväljaren så måste du ange deras användarnamn exakt och välja anspråksresultatet **Name**.

![Anspråkssökresultat](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Det finns ingen validering på de värden som du söker efter, vilket kan leda till felstavningar eller att användare väljer fel anspråkstyp att tilldela som anspråket **SurName**. Det kan förhindra att användare får åtkomst till resurserna.

För att hjälpa till vid det här scenariot så finns det en öppen källkodslösning som heter [AzureCP](https://yvand.github.io/AzureCP/) som ger en anpassad anspråksprovider för SharePoint 2016. Den använder Azure AD Graph för att matcha vad användare skriver in och utföra validering. Läs mer på [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Assign the Azure AD Security Group in the Azure portal

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och därefter **lokal SharePoint**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan skriver och väljer du **lokal SharePoint**.

    ![Länken till lokal SharePoint i programlistan](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Click the **Add user**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. Search for the Security Group you want to use, then click on the group to add it to the Select members section. Click **Select**, then click **Assign**.

    ![Search Security Group](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Check the notifications in the menu bar to be notified that the Group was successfully assigned to the Enterprise application in the Azure portal.

### <a name="create-sharepoint-on-premises-test-user"></a>Skapa lokal SharePoint-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i lokal SharePoint. Arbeta med [supportteamet för lokal SharePoint](https://support.office.com/) för att lägga till användarna i den lokala SharePoint-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på lokal SharePoint-panelen i Åtkomstpanelen så bör du automatiskt loggas in på den lokala SharePoint för vilken du konfigurerade enkel inloggning. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
