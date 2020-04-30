---
title: 'Självstudie: Azure Active Directory integrering med SharePoint lokalt | Microsoft Docs'
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81867103"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med SharePoint lokalt

I den här självstudien får du lära dig hur du integrerar SharePoint lokalt med Azure Active Directory (Azure AD). När du integrerar SharePoint lokalt med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SharePoint lokalt.
* Gör det möjligt för användarna att logga in automatiskt till SharePoint lokalt med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure Active Directory-integrering med SharePoint lokalt behöver du följande objekt:

* En Azure Active Directory-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* En SharePoint 2013-Server grupp eller senare.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure Active Directory enkel inloggning i en test miljö. Användare från Azure Active Directory kan komma åt din SharePoint lokalt.

## <a name="create-the-enterprise-applications-in-azure-portal"></a>Skapa företags program i Azure Portal

Om du vill konfigurera integrering av lokal SharePoint i Azure AD så behöver du lägga till lokal SharePoint från galleriet i din lista över hanterade SaaS-appar.

Lägg till lokal SharePoint från galleriet genom att utföra följande steg:

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

 > [!NOTE]
 > Om elementet inte ska vara tillgängligt kan det också öppnas via länken fasta **alla tjänster** överst i den vänstra navigerings panelen. I följande översikt finns **Azure Active Directory** länken i avsnittet **identitet** eller också kan den sökas i med hjälp av text rutan filter.

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

4. I rutan Sök skriver du **SharePoint lokalt**och väljer **SharePoint lokalt** från resultat panelen.

    <kbd>![Lokal SharePoint i resultatlistan](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Ange ett namn för din SharePoint-OnPrem och klicka på knappen **Lägg** till för att lägga till programmet.

1. I det nya företags programmet klickar du på egenskaper och kontrollerar värdet för **användar tilldelning krävs**

   <kbd>![Lokal SharePoint i resultatlistan](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

i vårt scenario är det här värdet inställt på **Nej**.

## <a name="configure-and-test-azure-ad"></a>Konfigurera och testa Azure AD

I det här avsnittet konfigurerar du enkel inloggning med Azure AD med SharePoint lokalt.
För att enkel inloggning ska fungera måste en länkrelation etableras mellan en Azure AD-användare och den relaterade användaren i lokal SharePoint.

Om du vill konfigurera och testa Azure Active Directory enkel inloggning med SharePoint lokalt, måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Konfigurera SharePoint lokalt](#configure-sharepoint-on-premises)** – om du vill konfigurera inställningar för enkel inloggning på program sidan.
1. **[Skapa en Azure AD test-användare i Azure Portal](#create-an-azure-ad-test-user-in-the-azure-portal)** – skapa en ny användare i Azure AD för enkel inloggning.
1. **[Skapa en Azure AD-säkerhetsgrupp i Azure Portal](#create-an-azure-ad-security-group-in-the-azure-portal)** – skapa en ny säkerhets grupp i Azure AD för enkel inloggning.
1. **[Bevilja behörighet till Azure Active Directory konto i SharePoint lokalt](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** – ge Azure AD-användare behörigheter.
1. Bevilja behörighet till Azure **[AD-gruppen i SharePoint lokalt](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** – ge Azure AD-gruppen behörighet.
1. **[Bevilja åtkomst till ett gäst konto till SharePoint lokalt i Azure Portal](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** -ge gäst kontot i Azure AD för SharePoint lokalt.
1. **[Konfigurera den betrodda identitets leverantören för flera webb program](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** – så här använder du samma betrodda identitets leverantör för flera webb program

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med lokal SharePoint:

1. Öppna Azure AD-katalogen i [Azure Portal](https://portal.azure.com/), klicka på **företags program**, klicka på det **tidigare skapade företags program namnet** och klicka på **enkel inloggning**.

2. I dialog rutan **Välj en enkel inloggnings metod** klickar du på **SAML** mode för att aktivera enkel inloggning.
 
3. Klicka på ikonen **Redigera** för att öppna dialog rutan för enkel **SAML-konfiguration** på sidan **Konfigurera enkel inloggning med SAML** .

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Enkel inloggningsinformation för lokal SharePoint-domän och URL:er](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. I rutan **identifierare** anger du en URL med följande mönster:`urn:<sharepointFarmName>:<federationName>`

    1. Skriv en URL i text rutan **svars-URL** med följande mönster:`https://<YourSharePointSiteURL>/_trust/`

    1. I text rutan **inloggnings-URL** skriver du en URL med följande mönster:`https://<YourSharePointSiteURL>/`
    1. Klicka på Spara.

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. I avsnittet **Konfigurera lokal SharePoint** kopierar du en eller flera lämpliga URL:er efter behov.
    
    1. **Inloggnings-URL**
    
        Kopiera inloggnings webb adress Ersätt **/SAML2** i slutet med **/wsfed**, det skulle se **https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed** ut (denna URL är inte korrekt)

    1. **Azure AD-identifierare**
    1. **Utloggnings-URL**
    > [!NOTE]
    > URL: en kan inte användas i SharePoint: du måste ersätta **/SAML2** med **/wsfed**. Lokal SharePoint-programmet använder en SAML 1.1-token så Azure AD förväntar sig en WS Fed-begäran från SharePoint-servern och efter autentisering så utfärdar den SAML 1.1. token.

### <a name="configure-sharepoint-on-premises"></a>Konfigurera SharePoint lokalt

1. **Skapa en ny betrodd identitets leverantör i SharePoint Server 2016**

    Logga in på SharePoint-servern och öppna hanterings gränssnittet för SharePoint. Fyll i värdena:
    1. **$Realm** (ID-värde från avsnittet SharePoint-lokal domän och URL: er i Azure Portal).
    1. **$wsfedurl** (enkel inloggnings-URL).
   1. **$filepath** (fil Sök väg som du har laddat ned certifikat filen till) från Azure Portal.

    Kör följande kommandon för att konfigurera en ny betrodd identitets leverantör.

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
1. **Aktivera den betrodda identitets leverantören för ditt program**

    a. I Central Administration går du till **Hantera webbprogram** och väljer det webbprogram som du vill skydda med Azure AD.

    b. I menyfliksområdet klickar du på **Autentiseringsproviders** och väljer den zon som du vill använda.

    c. Välj **Betrodd identitetsprovider** och välj den identitetsprovider som du just registrerade med namnet *AzureAD*.

    d. Klicka på **OK**.

    ![Konfigurera din autentiseringsprovider](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Skapa en Azure AD test-användare i Azure Portal

Syftet med det här avsnittet är att skapa en test användare i Azure Portal.

1. I Azure Portal i det vänstra fönstret väljer du **Azure Active Directory**i **Hantera** panorering Välj **användare**.

2. Välj sedan **alla användare** följt av **ny användare** längst upp på skärmen.

3. Välj alternativet **skapa användare** och i användar egenskaperna, utför följande steg.  
   Du kanske kan skapa användare i din Azure AD med hjälp av ditt klient certifikat eller en verifierad domän. 

    a. I fältet **namn** anger du användar namnet, vi använde **TestUser**.
  
    b. I fältet **Användarnamn** anger du `TestUser@yourcompanydomain.extension`  
    Till exempel, TestUser@contoso.com

    ![Dialogrutan Användare](./media/sharepoint-on-premises-tutorial/user-properties.png)

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

    e. Du kan nu dela platsen med TestUser@contoso.com och ge den här användaren åtkomst till den.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Skapa en Azure AD-säkerhetsgrupp i Azure Portal

1. Klicka på **Azure Active Directory > grupper**.

2. Klicka på **ny grupp**:

3. Fyll i **grupp typ**, **grupp namn**, **grupp Beskrivning**, **medlemskaps typ**. Klicka på pilen för att välja medlemmar och Sök sedan efter eller klicka på den medlem som du vill lägga till i gruppen. Klicka på **Välj** för att lägga till de valda medlemmarna och klicka sedan på **skapa**.

![Skapa en Azure AD-säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>Bevilja behörigheter till Azure Active Directory konto i SharePoint lokalt

Om du vill bevilja åtkomst till Azure Active Directory användare i den lokala SharePoint-platsen måste du dela webbplats samlingen eller lägga till Azure Active Directory användaren till en av webbplats samlingens grupp. Användare kan nu logga in på SharePoint-201x med identiteter från Azure AD, men det finns fortfarande möjligheter att förbättra användar upplevelsen. Om du till exempel söker efter en användare så visas flera sökresultat i personväljaren. Det finns ett Sök Resultat för var och en av de anspråks typer som skapas i anspråks mappningen. Om du vill välja en användare med personväljaren så måste du ange deras användarnamn exakt och välja anspråksresultatet **Name**.

![Anspråkssökresultat](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Det finns ingen validering av de värden som du söker efter, vilket kan leda till fel stavningar eller användare av misstag väljer fel anspråks typ. Det kan förhindra att användare får åtkomst till resurserna.

**För att åtgärda person väljaren** med det här scenariot finns en lösning med öppen källkod som heter [AzureCP](https://yvand.github.io/AzureCP/) som tillhandahåller en anpassad anspråks leverantör för SharePoint 2013, 2016 och 2019. Den använder Microsoft Graph-API: et för att lösa vad användare anger och utför verifiering. Läs mer på [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Utan AzureCP kan du lägga till grupper genom att lägga till Azure AD-gruppens ID, men det är inte användarens egna och tillförlitliga. Det ser ut så här:  
  >   
  >![Lägg till Azure AD-grupp till SharePoint-grupp](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>Bevilja behörighet till Azure AD-gruppen i SharePoint lokalt

För att kunna tilldela Azure Active Directory säkerhets grupper till SharePoint lokalt, är det nödvändigt att använda en anpassad anspråks leverantör för SharePoint Server. I vårt exempel har vi använt AzureCP.

 > [!NOTE]
 > Observera att AzureCP inte är en Microsoft-produkt eller som stöds av Microsofts tekniska support. Hämta, installera och konfigurera AzureCP på den lokala SharePoint-servergruppen per https://yvand.github.io/AzureCP/. 

1. Konfigurera AzureCP på den lokala SharePoint-gruppen eller en annan lösning för anspråks leverantörer. konfigurations stegen för AzureCP finns påhttps://yvand.github.io/AzureCP/Register-App-In-AAD.html

1. Öppna Azure AD-katalogen i Azure Portal. Klicka på **företags program**, klicka på namnet på den **tidigare skapade företags applikationen** och klicka på **enkel inloggning**.

1. Redigera avsnittet **användarattribut &-anspråk** på sidan **Konfigurera enkel inloggning med Sam**.

1. Klicka på **Lägg till ett grupp anspråk**.

1. Välj vilka grupper som är associerade med användaren som ska returneras i anspråket, i vårt fall väljer du **alla grupper** och i avsnittet källattribut väljer du **grupp-ID** och klickar på **Spara**.

Om du vill bevilja åtkomst till Azure Active Directory säkerhets grupp i den lokala SharePoint-gruppen måste du dela webbplats samlingen eller lägga till Azure Active Directory säkerhets gruppen i en av webbplats samlingens grupp.

1. Bläddra till SharePoint-webbplatssamling, under webbplats inställningar för webbplats samlingen, klickar du på "personer och grupper". Välj SharePoint-gruppen och klicka sedan på ny, "Lägg till användare i den här gruppen" och börja skriva in namnet på gruppen som visas i person väljaren Azure Active Directory säkerhets gruppen.

    ![Lägg till Azure AD-grupp till SharePoint-grupp](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Bevilja åtkomst till ett gäst konto till SharePoint lokalt i Azure Portal

nu är det möjligt att ge åtkomst till din SharePoint-webbplats till ett gäst konto på ett konsekvent sätt. Det händer att UPN ändras. Användaren med jdoe@outlook.com kommer att visas som `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`. Det är möjligt att få en sömlös upplevelse medan du delar din webbplats med externa användare. det är nödvändigt att lägga till vissa ändringar i avsnittet **användarattribut &-anspråk** i Azure Portal.

1. Öppna Azure AD-katalogen i Azure Portal. Klicka på **företags program**, klicka på namnet på den **tidigare skapade företags applikationen** och klicka på **enkel inloggning**.

1. Redigera avsnittet **användarattribut &-anspråk** på sidan **Konfigurera enkel inloggning med Sam**.

1. i den **obligatoriska anspråks** zonen klickar du på **unik användar identifierare (namn-ID)**.

1. Ändra egenskapen **Source Attribute** till värdet **User. localuserprincipalname** och **Save**.

    ![Användarattribut & anspråk initialt](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Använd menyfliksområdet gå tillbaka till **SAML-baserad inloggning** nu. avsnittet **användarattribut &-anspråk** skulle se ut så här: 

    ![Användarattribut & anspråk slutligt](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > Efter namn och tilldelat namn krävs inte i den här installationen.

1. I Azure Portal väljer du **Azure Active Directory**i den vänstra rutan, väljer **användare**.

1. Klicka på **ny gäst användare**

1. Välj alternativet **Bjud in användare** och fyll i användarens egenskaper och klicka på **Bjud in**.

1. Du kan nu dela platsen med MyGuestAccount@outlook.com och ge den här användaren åtkomst till den.

    ![Dela webbplats med gäst konto](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurera den betrodda identitets leverantören för flera webb program

Konfigurationen fungerar för en enkel webbapp, men ytterligare konfiguration krävs om du planerar att använda samma betrodda identitetsprovider för flera webbprogram. Anta exempelvis att vi hade utökat ett webbprogram till att använda URL:en `https://sales.contoso.com` och nu också vill autentisera användare till `https://marketing.contoso.com`. För att göra det så måste vi uppdatera identitetsprovider för att respektera parametern WReply och uppdatera programregistreringen i Azure AD för att lägga till en svars-URL.

1. Öppna Azure AD-katalogen i Azure Portal. Klicka på **företags program**, klicka på namnet på den **tidigare skapade företags applikationen** och klicka på **enkel inloggning**.

2. Redigera den **grundläggande SAML-konfigurationen**på sidan **Konfigurera enkel inloggning med Sam**.

    ![redigera grundläggande SAML-konfiguration](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. Lägg till URL: en för ytterligare webb program i **svars-URL: en (intygs mottagar tjänst-URL)** och klicka på **Spara**.

    ![redigera grundläggande SAML-konfiguration](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. Öppna **hanterings gränssnittet för SharePoint-201x** på SharePoint-servern och kör följande kommandon med namnet på den betrodda identitets-token som du använde tidigare.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. I Central Administration går du till webbprogrammet och aktiverar den befintliga betrodda identitetsprovidern.

Du kanske har ett annat scenario där du vill ge åtkomst till din lokala SharePoint för dina interna användare, i det här scenariot måste du distribuera Microsoft Azure Active Directory Connect som tillåter att dina lokala användare synkroniseras med Azure Active Directory, men installationen kommer att bli en del av en annan artikel. 

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vad är hybrid identitet med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
