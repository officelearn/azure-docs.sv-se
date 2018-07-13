---
title: 'Självstudier: Azure Active Directory-integrering med lokal SharePoint | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och lokal SharePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: jeedes
ms.openlocfilehash: a2f90944145bf98f9d3f39a12cd463245c97e67d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002270"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Självstudier: Azure Active Directory-integrering med lokal SharePoint

I den här självstudien får du lära dig hur du integrerar lokal SharePoint med Azure Active Directory (AD Azure).

Integrera lokal SharePoint med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till lokal SharePoint.
- Du kan aktivera användarna att automatiskt få loggat in på SharePoint lokalt (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med lokal SharePoint, behöver du följande objekt:

- En Azure AD-prenumeration
- En lokal SharePoint-enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till lokal SharePoint från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Att lägga till lokal SharePoint från galleriet
För att konfigurera integrering av lokal SharePoint i Azure AD, som du behöver lägga till lokal SharePoint från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till lokal SharePoint från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **lokal SharePoint-** väljer **lokal SharePoint-** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![SharePoint lokalt i listan med resultat](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med lokal SharePoint-baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarande i lokal SharePoint är till en användare i Azure AD. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SharePoint på plats måste upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med lokal SharePoint, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Bevilja åtkomst till SharePoint lokalt testanvändare](#grant-access-to-sharePoint-on-premises-test-user)**  – du har en motsvarighet för Britta Simon i lokal SharePoint som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt lokala SharePoint-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med lokal SharePoint:**

1. I Azure-portalen på den **lokal SharePoint-** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_samlbase.png)

3. På den **lokal SharePoint-domän och URL: er** avsnittet, utför följande steg:

    ![Lokal SharePoint-domän och URL: er enkel inloggning för information](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_url1.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. I den **identifierare** textrutan anger du URL: `urn:sharepoint:federation`

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media\sharepoint-on-premises-tutorial/tutorial_general_400.png)

6. På den **lokal SharePoint-konfigurationen** klickar du på **lokal Konfigurera SharePoint-** att öppna **konfigurera inloggning** fönster. Kopiera den **enkel inloggnings-URL för** från den **Snabbreferens avsnittet.**

    ![En lokal SharePoint-konfigurationen](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_configure.png)

    > [!NOTE]
    > Lokal SharePoint-programmet använder SAML 1.1 token, så att Azure AD förväntar sig WS Fed begäran från SharePoint-servern och efter autentisering, den utfärdar SAML 1.1. token.

7. I ett annat webbläsarfönster logga du in på SharePoint-webbplatsen lokala företag som administratör.

8. **Konfigurera en ny betrodd identitetsleverantör i SharePoint Server 2016**

    Logga in på SharePoint Server 2016-servern och öppna hanteringsgränssnittet för SharePoint 2016. Fyll i värdena för $realm och $wsfedurl $filepath från Azure-portalen och kör följande kommandon för att konfigurera en ny betrodd identitetsleverantör.

    > [!TIP]
    > Om du inte har arbetat med hjälp av PowerShell eller vill veta mer om hur PowerShell fungerar, se [SharePoint PowerShell](https://docs.microsoft.com/en-us/powershell/sharepoint/overview?view=sharepoint-ps). 

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have copied from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Därefter gör följande om du vill aktivera betrodd identitetsleverantör för ditt program:

    a. I Central Administration, gå till **hantera webbprogram** och välj den webbapp som du vill skydda med Azure AD.

    b. I menyfliksområdet klickar du på **autentiseringsproviders** och välj zonen som du vill använda.

    c. Välj **betrodd identitetsleverantör** och väljer den identifiera-provider som du just registrerade med namnet *AzureAD*.

    d. På inloggningssidan URL inställningen, Välj **anpassad inloggningssida** och ange värdet ”/_trust/”.

    e. Klicka på **OK**.

    ![Konfigurera din autentiseringsprovider](./media\sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media\sharepoint-on-premises-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media\sharepoint-on-premises-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media\sharepoint-on-premises-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media\sharepoint-on-premises-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="grant-access-to-sharepoint-on-premises-test-user"></a>Bevilja åtkomst till SharePoint lokalt testanvändare

De användare som ska logga in på Azure AD och få åtkomst till SharePoint måste beviljas åtkomst till programmet använder du följande steg för att ange behörighet för att få åtkomst till webbprogrammet.

1. I Central Administration klickar du på **programhantering**.

2. På den **programhantering** sidan den **webbprogram** klickar du på **hantera webbprogram**.

3. Klicka på lämplig webbprogrammet och klicka sedan på **användarprincip**.

4. I princip för webbprogram, klickar du på **Lägg till användare**.

    ![Söka efter en användare med deras namn anspråk](./media\sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. I den **Lägg till användare** dialogrutan klickar du på den aktuella zonen i **zoner**, och klicka sedan på **nästa**.

6. I den **princip för webbprogram** i dialogrutan den **Välj användare** klickar du på den **Bläddra** ikon.

7. I den **hitta** textrutan skriver den **användarens huvudnamn name(UPN)** värde som du har konfigurerat SharePoint lokalt program i Azure AD och klicka på **Search**. </br>Exempel: *brittasimon@contoso.com*.

8. Välj namnegenskapen under AzureAD-rubriken i listvyn och klicka på **Lägg till** klickar **OK** att stänga dialogrutan.

9. Vid behörigheter markerar **fullständig kontroll**.

    ![Ge fullständig behörighet till en användare av anspråk](./media\sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

10. Klicka på **Slutför**, och klicka sedan på **OK**.

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurera en betrodd identitetsleverantör för flera webbprogram

Konfigurationen fungerar för en enkel webbapp, men ytterligare konfiguration krävs om du planerar att använda samma betrodd identitetsleverantör för flera webbprogram. Anta exempelvis att ett webbprogram URL: en utökades hade `https://portal.contoso.local` och nu vill autentisera användare till `https://sales.contoso.local` samt. Detta gör måste vi du uppdatera identitetsprovider för att respektera parametern WReply och uppdatera program-registreringen i Azure AD för att lägga till en svars-URL.

1. Öppna Azure AD-katalog i Azure-portalen. Klicka på **appregistreringar**, klicka sedan på **visa alla program**. Klicka på det program som du skapade tidigare (SharePoint SAML-integrering).

2. Klicka på **inställningar**.

3. I inställningsbladet klickar du på **Svarswebbadresser**. 

4. Lägg till URL-Adressen för ytterligare webbprogrammet med `/_trust/default.aspx` läggas till URL: en (till exempel `https://sales.contoso.local/_trust/default.aspx`) och klicka på **spara**.

5. På SharePoint-servern öppnar du den **hanteringsgränssnittet för SharePoint 2016** och kör följande kommandon, med hjälp av namnet på den betrodda tokenutfärdare som du använde tidigare.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. Gå till webbprogrammet i Central Administration och aktivera befintliga betrodd identitetsleverantör. Kom ihåg att du också konfigurera inloggningssidan URL: en som en anpassad inloggningssida `/_trust/`.

7. I Central Administration, klicka på webbprogrammet och välj **användarprincip**. Lägg till en användare med behörighet som visas tidigare i den här artikeln.

### <a name="fixing-people-picker"></a>Åtgärda Personväljaren

Användare kan nu logga in på SharePoint 2016 med hjälp av identiteter från Azure AD, men det finns fortfarande möjligheter till förbättring av användarupplevelsen. Exempelvis anger söka efter en användare flera sökresultat i Personväljaren. Det finns ett sökresultat för var och en av de 3 anspråkstyper som har skapats i Anspråksmappning. Om du vill välja en användare som använder Personväljaren måste du ange sina användarnamn exakt och väljer den **namn** anspråk resultatet.

![Anspråk sökresultat](./media\sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Det finns ingen validering på de värden som du söker efter, vilket kan leda till felstavningar eller användare råkar välja fel Anspråkstypen tilldela som den **efternamn** anspråk. Detta kan förhindra att användare har åtkomst till resurser.

För att hjälpa till med det här scenariot, det finns en öppen källkod lösning kallad [AzureCP](https://yvand.github.io/AzureCP/) som ger en anpassad anspråksprovider för SharePoint 2016. Den använder Azure AD Graph för att lösa vad användarna ange och utföra verifiering. Läs mer på [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till lokal SharePoint.

![Tilldela rollen][200]

**Om du vill tilldela lokal SharePoint-Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **lokal SharePoint-**.

    ![SharePoint-länk i listan med program](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_app.png)

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen SharePoint lokalt i åtkomstpanelen bör du få automatiskt inloggade till ditt lokala SharePoint-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Använda Azure AD för SharePoint Server-autentisering](https://docs.microsoft.com/en-us/office365/enterprise/using-azure-ad-for-sharepoint-server-authentication)

<!--Image references-->

[1]: ./media\sharepoint-on-premises-tutorial/tutorial_general_01.png
[2]: ./media\sharepoint-on-premises-tutorial/tutorial_general_02.png
[3]: ./media\sharepoint-on-premises-tutorial/tutorial_general_03.png
[4]: ./media\sharepoint-on-premises-tutorial/tutorial_general_04.png

[100]: ./media\sharepoint-on-premises-tutorial/tutorial_general_100.png

[200]: ./media\sharepoint-on-premises-tutorial/tutorial_general_200.png
[201]: ./media\sharepoint-on-premises-tutorial/tutorial_general_201.png
[202]: ./media\sharepoint-on-premises-tutorial/tutorial_general_202.png
[203]: ./media\sharepoint-on-premises-tutorial/tutorial_general_203.png

