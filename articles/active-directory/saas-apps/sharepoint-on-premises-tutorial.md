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
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 7e47891a74feb60b0f3e4594bd1621e8b62d64d1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867103"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Lokala SharePoint

I den här självstudien får du lära dig hur du integrerar SharePoint lokalt med Azure Active Directory (Azure AD). När du integrerar Lokalt SharePoint med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SharePoint lokalt.
* Aktivera dina användare så att de automatiskt loggas in på SharePoint lokalt med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure Active Directory-integrering med SharePoint lokalt behöver du följande objekt:

* En Azure Active Directory-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* En SharePoint 2013-grupp eller nyare.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel Azure Active Directory-inloggning i en testmiljö. Användare från Azure Active Directory kan komma åt din lokala Sharepoint-resurs.

## <a name="create-the-enterprise-applications-in-azure-portal"></a>Skapa Enterprise Applications i Azure-portalen

Om du vill konfigurera integrering av lokal SharePoint i Azure AD så behöver du lägga till lokal SharePoint från galleriet i din lista över hanterade SaaS-appar.

Lägg till lokal SharePoint från galleriet genom att utföra följande steg:

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

 > [!NOTE]
 > Om elementet inte ska vara tillgängligt kan det också öppnas via länken **Alla tjänster** högst upp på den vänstra navigeringspanelen. I följande översikt finns **Azure Active Directory-länken** i avsnittet **Identitet** eller genom att använda filtertextrutan.

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

4. Skriv **lokalt SharePoint**i sökrutan och välj **Lokalt SharePoint** från resultatpanelen.

    <kbd>![Lokal SharePoint i resultatlistan](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Ange ett namn på SharePoint OnPrem och klicka på knappen **Lägg** till för att lägga till programmet.

1. I det nya företagsprogrammet klickar du på Egenskaper och kontrollerar värdet för **Användartilldelning som krävs**

   <kbd>![Lokal SharePoint i resultatlistan](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

i vårt scenario är det här värdet inställt på **Nr**.

## <a name="configure-and-test-azure-ad"></a>Konfigurera och testa Azure AD

I det här avsnittet konfigurerar du azure AD enkel inloggning med SharePoint lokalt.
För att enkel inloggning ska fungera måste en länkrelation etableras mellan en Azure AD-användare och den relaterade användaren i lokal SharePoint.

Om du vill konfigurera och testa en enkel Azure Active Directory-inloggning med SharePoint lokalt måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Konfigurera Lokalt SharePoint](#configure-sharepoint-on-premises)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
1. **[Skapa en Azure AD-testanvändare i Azure-portalen](#create-an-azure-ad-test-user-in-the-azure-portal)** – skapa ny användare i Azure AD för enkel inloggning.
1. **[Skapa en Azure AD-säkerhetsgrupp i Azure-portalen](#create-an-azure-ad-security-group-in-the-azure-portal)** – skapa en ny säkerhetsgrupp i Azure AD för enkel inloggning.
1. **[Bevilja behörigheter till Azure Active Directory-konto i SharePoint lokalt](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** – ge behörighet till Azure AD-användare.
1. **[Bevilja behörigheter till Azure AD-grupp i SharePoint lokalt](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** – ge behörighet till Azure AD-gruppen.
1. **[Bevilja åtkomst till ett gästkonto till SharePoint lokalt i Azure-portalen](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** – ge behörighet till gästkontot i Azure AD för SharePoint lokalt.
1. **[Konfigurera den betrodda identitetsprovidern för flera webbprogram](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** – så här använder du samma betrodda identitetsprovider för flera webbprogram

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med lokal SharePoint:

1. Öppna Azure AD-katalogen i [Azure-portalen,](https://portal.azure.com/)klicka på **Enterprise-program,** klicka på det **tidigare skapade företagsprogramnamnet** och klicka på **Enkel inloggning**.

2. Klicka på **SAML-läge** i dialogrutan **Välj en enda inloggningsmetod** för att aktivera enkel inloggning.
 
3. På Konfigurera enkel inloggning med SAML klickar du på ikonen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration.** **Set up Single Sign-On with SAML**

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Enkel inloggningsinformation för lokal SharePoint-domän och URL:er](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Skriv en URL med följande mönster i rutan **Identifierare:**`urn:<sharepointFarmName>:<federationName>`

    1. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<YourSharePointSiteURL>/_trust/`

    1. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<YourSharePointSiteURL>/`
    1. Klicka på spara.

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. I avsnittet **Konfigurera lokal SharePoint** kopierar du en eller flera lämpliga URL:er efter behov.
    
    1. **Inloggnings-URL**
    
        Kopiera inloggningsadress ersätta **/ saml2** i slutet med / **https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed** **wsfed,** skulle det se ut (denna url är inte korrekt)

    1. **Azure AD-identifierare**
    1. **Utloggnings-URL**
    > [!NOTE]
    > Den här URL:en kan inte användas som i SharePoint: du måste ersätta **/saml2** med **/wsfed**. Lokal SharePoint-programmet använder en SAML 1.1-token så Azure AD förväntar sig en WS Fed-begäran från SharePoint-servern och efter autentisering så utfärdar den SAML 1.1. token.

### <a name="configure-sharepoint-on-premises"></a>Konfigurera lokalt sharepoint

1. **Skapa en ny betrodd identitetsprovider i SharePoint Server 2016**

    Logga in på SharePoint-servern och öppna SharePoint-hanteringsskalet. Fyll i värdena:
    1. **$realm** (Identifierare värde från SharePoint lokala domän och webbadresser avsnitt i Azure-portalen).
    1. **$wsfedurl** (URL för enkel inloggningstjänst).
   1. **$filepath** (filsökväg som du har hämtat certifikatfilen till) från Azure-portalen.

    Kör följande kommandon för att konfigurera en ny betrodd identitetsprovider.

    > [!TIP]
    > Om PowerShell är nytt för dig eller om du vill veta mer om hur PowerShell fungerar, se [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. **Aktivera den betrodda identitetsprovidern för ditt program**

    a. I Central Administration går du till **Hantera webbprogram** och väljer det webbprogram som du vill skydda med Azure AD.

    b. I menyfliksområdet klickar du på **Autentiseringsproviders** och väljer den zon som du vill använda.

    c. Välj **Betrodd identitetsprovider** och välj den identitetsprovider som du just registrerade med namnet *AzureAD*.

    d. Klicka på **OK**.

    ![Konfigurera din autentiseringsprovider](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Skapa en Azure AD-testanvändare i Azure-portalen

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory**i Den vänstra rutan i **Hantera** panorera väljer **Användare**.

2. Välj sedan **Alla användare** följt av **Ny användare** högst upp på skärmen.

3. Välj alternativet **Skapa användare** och utför följande steg i egenskaperna Användare.  
   Du kanske kan skapa användare i din Azure AD med hjälp av ditt klientsuffix eller någon verifierad domän. 

    a. I fältet **Namn** anger användarnamnet använde vi **TestUser**.
  
    b. I fältet **Användarnamn** anger du `TestUser@yourcompanydomain.extension`  
    Till exempel, TestUser@contoso.com

    ![Dialogrutan Användare](./media/sharepoint-on-premises-tutorial/user-properties.png)

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

    e. Du kan nu dela TestUser@contoso.com webbplatsen med och tillåta den här användaren att komma åt den.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Skapa en Azure AD-säkerhetsgrupp i Azure-portalen

1. Klicka på **Azure Active Directory > Groups**.

2. Klicka på **Ny grupp:**

3. Fyll i **grupptyp,** **Gruppnamn,** **Gruppbeskrivning**, **Medlemstyp**. Klicka på pilen för att välja medlemmar, sedan söka efter eller klicka på den medlem du vill lägga till i gruppen. Klicka på **Välj** om du vill lägga till de markerade medlemmarna och klicka sedan på **Skapa**.

![Skapa en Azure AD-säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>Bevilja behörigheter till Azure Active Directory-konto i lokalt SharePoint

Om du vill bevilja åtkomst till Azure Active Directory-användaren i den lokala SharePoint-databasen måste du dela webbplatssamlingen eller lägga till Azure Active Directory-användaren i en av webbplatssamlingens grupp. Användare kan nu logga in på SharePoint 201x med identiteter från Azure AD, men det finns fortfarande möjligheter till förbättringar av användarupplevelsen. Om du till exempel söker efter en användare så visas flera sökresultat i personväljaren. Det finns ett sökresultat för var och en av anspråkstyperna som skapas i anspråksmappningen. Om du vill välja en användare med personväljaren så måste du ange deras användarnamn exakt och välja anspråksresultatet **Name**.

![Anspråkssökresultat](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Det finns ingen validering av de värden du söker efter, vilket kan leda till felstavningar eller användare av misstag välja fel anspråkstyp. Det kan förhindra att användare får åtkomst till resurserna.

**För att åtgärda personväljaren** med det här scenariot finns det en lösning med öppen källkod som heter [AzureCP](https://yvand.github.io/AzureCP/) som tillhandahåller en anpassad anspråksleverantör för SharePoint 2013, 2016 och 2019. Det kommer att använda Microsoft Graph API för att lösa vad användare anger och utför validering. Läs mer på [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Utan AzureCP kan du lägga till grupper genom att lägga till Azure AD-gruppens ID men det här är inte användarens användarvänliga och tillförlitliga. Det är hur det ser ut:  
  >   
  >![Lägga till Azure AD-grupp i Sharepoint Group](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>Bevilja behörigheter till Azure AD-grupp i lokalt SharePoint

För att tilldela Azure Active Directory-säkerhetsgrupper till SharePoint lokalt måste du använda en anpassad anspråksprovider för SharePoint Server. I vårt exempel har vi använt AzureCP.

 > [!NOTE]
 > Observera att AzureCP inte är en Microsoft-produkt eller stöds av Microsofts tekniska support. Hämta, installera och konfigurera AzureCP i den https://yvand.github.io/AzureCP/lokala SharePoint-servergruppen per . 

1. Konfigurera AzureCP i den lokala SharePoint-servergruppen eller en alternativ anpassad anspråksproviderlösning. AzureCP-konfigurationens steg är tillgängliga påhttps://yvand.github.io/AzureCP/Register-App-In-AAD.html

1. Öppna Azure AD-katalogen i Azure-portalen. Klicka på **Enterprise-program,** klicka på det **tidigare skapade företagsprogrammets namn** och klicka på Enkel **inloggning**.

1. På sidan **Konfigurera Enkel inloggning med SAM**redigerar du avsnittet **Användarattribut & anspråk.**

1. Klicka på **Lägg till ett gruppanspråk**.

1. Välj vilka grupper som är associerade med användaren ska returneras i anspråket, välj i vårt fall **Alla grupper** och i attributavsnittet Källa väljer du **Grupp-ID** och klickar på **Spara**.

Om du vill bevilja åtkomst till Azure Active Directory Security Group i den lokala SharePoint-delningsgruppen måste du dela webbplatssamlingen eller lägga till Azure Active Directory Security Group i en av webbplatssamlingens grupp.

1. Bläddra till SharePoint-webbplatssamlingen under Webbplatsinställningar för webbplatssamlingen och klicka på "Personer och grupper". Välj sharepoint-gruppen och klicka sedan på Ny, "Lägg till användare i den här gruppen" och börja skriva namnet på din grupp som personväljaren visar Azure Active Directory Security Group.

    ![Lägga till Azure AD-grupp i Sharepoint Group](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Bevilja åtkomst till ett gästkonto till SharePoint lokalt i Azure-portalen

Det är nu möjligt att ge åtkomst till din SharePoint-webbplats till ett gästkonto på ett konsekvent sätt. Det händer att UPN ändras. Användare jdoe@outlook.com med kommer att `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`representeras som . Det är möjligt att få en sömlös upplevelse när du delar din webbplats med externa användare, det skulle vara nödvändigt att lägga till några ändringar i **användarattribut & anspråk** avsnitt i Azure-portalen.

1. Öppna Azure AD-katalogen i Azure-portalen. Klicka på **Enterprise-program,** klicka på det **tidigare skapade företagsprogrammets namn** och klicka på Enkel **inloggning**.

1. På sidan **Konfigurera Enkel inloggning med SAM**redigerar du avsnittet **Användarattribut & anspråk.**

1. i zonen **Obligatoriskt anspråk** klickar du på **Unik användaridentifierare (namn-ID).**

1. Ändra egenskapen **Source Attribute** till value **user.localuserprincipalname** och **spara**.

    ![Användarattribut & anspråk initial](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Använda menyfliksområdet gå tillbaka till **SAML-baserade Sign-on** nu **användarattribut & fordringar** avsnitt skulle se ut så här: 

    ![Användarattribut & anspråk slutgiltiga](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > Efternamn och förnamn krävs inte i den här inställningen.

1. I Den vänstra rutan i Azure-portalen väljer du **Azure Active Directory**och väljer **Användare**.

1. Klicka på **Ny gästanvändare**

1. Välj alternativet **Bjud in användare** och fyll i användarens egenskaper och klicka på Bjud **in**.

1. Du kan nu dela MyGuestAccount@outlook.com webbplatsen med och tillåta den här användaren att komma åt den.

    ![Dela webbplats med gästkonto](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurera den betrodda identitetsprovidern för flera webbprogram

Konfigurationen fungerar för en enkel webbapp, men ytterligare konfiguration krävs om du planerar att använda samma betrodda identitetsprovider för flera webbprogram. Anta exempelvis att vi hade utökat ett webbprogram till att använda URL:en `https://sales.contoso.com` och nu också vill autentisera användare till `https://marketing.contoso.com`. För att göra det så måste vi uppdatera identitetsprovider för att respektera parametern WReply och uppdatera programregistreringen i Azure AD för att lägga till en svars-URL.

1. Öppna Azure AD-katalogen i Azure-portalen. Klicka på **Enterprise-program,** klicka på det **tidigare skapade företagsprogrammets namn** och klicka på Enkel **inloggning**.

2. På sidan Konfigurera Enkel inloggning med SAM redigerar du **den grundläggande SAML-konfigurationen**. **Set up Single Sign-On with SAM**

    ![redigera grundläggande SAML-konfiguration](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. I **Svars-URL (Url för kontroll konsumenttjänst)** lägger du till URL:en för de ytterligare webbprogrammen och klickar på **Spara**.

    ![redigera grundläggande SAML-konfiguration](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. Öppna **SharePoint 201x Management Shell** på SharePoint-servern och kör följande kommandon med namnet på den betrodda identitetstokenutfärdaren som du använde tidigare.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. I Central Administration går du till webbprogrammet och aktiverar den befintliga betrodda identitetsprovidern.

Du kanske har andra scenarier där du vill ge åtkomst till din lokala Sharepoint för dina interna användare, för det här scenariot måste du distribuera Microsoft Azure Active Directory Connect som gör det möjligt att synkronisera dina lokala användare med Azure Active Directory, den här inställningen skulle vara en del av en annan artikel. 

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vad är hybrididentitet med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
