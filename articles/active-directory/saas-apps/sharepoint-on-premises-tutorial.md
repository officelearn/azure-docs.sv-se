---
title: 'Självstudier: Azure Active Directory-integrering med SharePoint lokala | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SharePoint lokala.
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
ms.date: 06/14/2018
ms.author: jeedes
ms.openlocfilehash: 02421ace226f42da58eb9864fe0ef2e1ca550391
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319290"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Självstudier: Azure Active Directory-integrering med SharePoint lokala

I kursen får lära du att integrera SharePoint lokala med Azure Active Directory (AD Azure).

Integrera SharePoint lokala med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SharePoint lokala.
- Du kan aktivera användarna att automatiskt hämta loggat in på SharePoint lokalt (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SharePoint lokala behöver du följande:

- En Azure AD-prenumeration
- Ett SharePoint lokalt enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SharePoint lokalt från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Att lägga till SharePoint lokalt från galleriet
Du måste lägga till SharePoint lokala från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SharePoint lokala i Azure AD.

**Utför följande steg för att lägga till SharePoint lokala från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]

3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SharePoint lokala**väljer **SharePoint lokala** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![SharePoint lokalt i resultatlistan](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med SharePoint lokalt baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i SharePoint lokala motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SharePoint lokalt måste upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med SharePoint på lokalt, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare för SharePoint lokalt](#create-a-sharePoint-on-premises-test-user)**  – du har en motsvarighet för Britta Simon i SharePoint lokala som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt lokala SharePoint-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SharePoint lokala:**

1. I Azure-portalen på den **SharePoint lokala** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning dialogrutan](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_samlbase.png)

3. På den **SharePoint lokalt domän och URL: er** avsnittet, utför följande steg:

    ![SharePoint-lokala domän URL: er och enkel inloggning information](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_url1.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `urn:sharepoint:<YourSharePointServerURL>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [SharePoint lokalt klienten supportteamet](https://support.office.com/) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn med filnamnstillägget .cer. Kopiera och klistra in den fullständiga sökvägen till metadatafilen hämtade i anteckningar.

    ![Länken hämta certifikatet](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media\sharepoint-on-premises-tutorial/tutorial_general_400.png)

6. På den **SharePoint lokalt Configuration** klickar du på **Konfigurera SharePoint lokala** att öppna **konfigurera inloggning** fönster. Kopiera den **inloggning tjänst-URL för enkel** från den **Snabbreferens avsnitt.**

    ![Konfiguration av SharePoint lokalt](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_configure.png)

    > [!NOTE]
    > SharePoint lokala program använder SAML 1.1 token, så Azure AD förväntar WS Fed begäran från SharePoint server och efter autentisering, den utfärdar SAML 1.1. token.

7. I en annan webbläsarfönster loggar du in på webbplatsen SharePoint lokalt företag som administratör.

8. **Konfigurera en ny betrodd identitetsleverantör i SharePoint Server 2016**

    Logga in på SharePoint Server 2016-server och öppna hanteringsgränssnittet för SharePoint 2016. Fyll i värdena för $realm, $wsfedurl och $filepath från Azure-portalen och kör följande kommandon för att konfigurera en ny betrodd identitetsleverantör.

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

    Därefter gör följande om du vill aktivera den betrodda identitetsleverantören för programmet:

    a. I Central Administration, gå till **hantera webbprogram** och välj webbprogram som du vill skydda med Azure AD.

    b. I menyfliksområdet klickar du på **autentiseringsproviders** och välj zonen som du vill använda.

    c. Välj **betrodda identitetsleverantör** och välj den identifiera-provider som du precis har registrerats med namnet *AzureAD*.

    d. Välj på inställningen inloggningssidan URL **anpassad inloggningssidan** och ange värdet ”/_trust/”.

    e. Klicka på **OK**.

    ![Konfigurera din autentiseringsprovider](./media\sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media\sharepoint-on-premises-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media\sharepoint-on-premises-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media\sharepoint-on-premises-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media\sharepoint-on-premises-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.

### <a name="create-a-sharepoint-on-premises-test-user"></a>Skapa en testanvändare för SharePoint lokalt

1. I Central Administration klickar du på **programhantering**.

2. På den **programhantering** sidan den **webbprogram** klickar du på **hantera webbprogram**.

3. Klicka på lämplig webbprogrammet och klickar sedan på **användarprincip**.

4. Klicka på i princip för webbprogram **Lägg till användare**.

    ![Söka efter en användare med deras namn anspråk](./media\sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. I den **Lägg till användare** dialogrutan klickar du på den aktuella zonen i **zoner**, och klicka sedan på **nästa**.

6. I den **princip för webbprogram** i dialogrutan den **Välj användare** klickar du på den **Bläddra** ikon.

7. I den **hitta** textruta in inloggningsnamn för en användare i din katalog och klickar på **Sök**. </br>Exempel: *demouser@blueskyabove.onmicrosoft.com*.

8. Välj namnegenskapen under rubriken AzureAD i listan och klicka på **Lägg till** Klicka **OK** att stänga dialogrutan.

9. I behörigheter, klickar du på **fullständig kontroll**.

    ![Ge fullständig behörighet till en användare med anspråk](./media\sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

10. Klicka på **Slutför**, och klicka sedan på **OK**.

### <a name="fixing-people-picker"></a>Åtgärda personer objektväljare

Användare kan nu logga in på SharePoint 2016 med hjälp av identiteter från Azure AD, men det finns fortfarande möjligheter till förbättring av användarupplevelsen. Till exempel anger söker efter en användare flera sökresultat i Personväljaren. Det finns ett sökresultat för varje 3 anspråkstyper som har skapats i mappningen för anspråk. Om du vill välja en användare med hjälp av Personväljaren för som du måste ange sina användarnamn exakt och välja den **namn** anspråk resultat.

![Anspråk sökresultat](./media\sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Ingen validering på de värden som du söker efter, vilket kan leda till stavfel eller användare oavsiktligt väljer fel Anspråkstyp tilldela som den **efternamn** anspråk. Detta kan förhindra användare från att ansluta till resurser.

Om du vill hjälpa till med det här scenariot, det finns en öppen källkod lösning kallad [AzureCP](https://yvand.github.io/AzureCP/) som ger en anpassad anspråksprovider för SharePoint 2016. Använder Azure AD-diagram för att lösa vad användarna ange och utföra valideringen. Mer information finns i [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SharePoint lokala.

![Tilldela rollen][200]

**Om du vill tilldela SharePoint lokala Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201]

2. Välj i listan med program **SharePoint lokala**.

    ![SharePoint-länken i listan med program](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_app.png)

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen SharePoint lokalt på åtkomstpanelen bör du få automatiskt inloggade i tillämpningsprogrammet SharePoint lokalt.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


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

