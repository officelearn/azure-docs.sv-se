---
title: 'Självstudier: Azure Active Directory-integrering med Workday | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workday.
services: active-directory
documentationCenter: na
author: cmmdesai
manager: daveba
ms.reviewer: jeedes
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2018
ms.author: chmutali
ms.openlocfilehash: d44d44b47420e2e05c351e5c607cc71b1c7e1247
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54824448"
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Självstudier: Azure Active Directory-integrering med Workday

I den här självstudien får du lära dig hur du integrerar Workday med Azure Active Directory (AD Azure).

Integrera Workday med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Workday.
- Du kan aktivera användarna att automatiskt få loggat in på Workday (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Workday, behöver du följande objekt:

- En Azure AD-prenumeration
- En Workday enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Workday från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-workday-from-the-gallery"></a>Att lägga till Workday från galleriet

För att konfigurera integrering av Workday i Azure AD, som du behöver lägga till Workday från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Workday från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Workday**väljer **Workday** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Arbetsdag i resultatlistan](./media/workday-tutorial/tutorial_workday_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Workday baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Workday är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Workday upprättas.

I Workday, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Workday, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare i Workday](#create-a-workday-test-user)**  – du har en motsvarighet för Britta Simon i Workday som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Workday-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Workday:**

1. I Azure-portalen på den **Workday** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/workday-tutorial/tutorial_workday_samlbase.png)

3. På den **Workday domän och URL: er** avsnittet, utför följande steg:

    ![Workday domän och URL: er med enkel inloggning för information](./media/workday-tutorial/tutorial_workday_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://impl.workday.com/<tenant>/login-saml2.htmld`

    b. I den **identifierare** textrutan anger du ett URL: `https://www.workday.com`

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg:

    ![Workday domän och URL: er med enkel inloggning för information](./media/workday-tutorial/tutorial_workday_url1.png)

    I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > De här värdena är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och svars-URL. Svars-URL måste ha en underdomän till exempel: www, wd2, wd3, wd3 impl, wd5, wd5 impl).
    > Med något som ”*http://www.myworkday.com*” fungerar men ”*http://myworkday.com*” inte. Kontakta [Workday klienten supportteamet](https://www.workday.com/en-us/partners-services/services/support.html) att hämta dessa värden.

5. Workday program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. Följande skärmbild visar ett exempel för den här konfigurationen.

    ![Konfigurera enkel inloggning](./media/Workday-tutorial/tutorial_workday_attributes.png)

    > [!NOTE]
    > Här har vi mappat namn-ID med UPN (user.userprincipalname) som standard. Du behöver mappa namn-ID med faktiska användar-ID i Workday-konto (din e-post, UPN etc.) för lyckad enkel inloggning att fungera.

6. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/workday-tutorial/tutorial_workday_certificate.png)

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/workday-tutorial/tutorial_general_400.png)

8. På den **Workday Configuration** klickar du på **konfigurera Workday** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Workday-konfiguration](./media/workday-tutorial/tutorial_workday_configure.png)

9. I ett annat webbläsarfönster logga du in på webbplatsen för Workday-företag som administratör.

10. I den **sökrutan** sökning med namnet **redigera klientkonfiguration – Security** uppe till vänster på startsidan.

    ![Redigera klient Security](./media/workday-tutorial/IC782925.png "redigera klient-säkerhet")

11. I den **omdirigerings-URL: er** avsnittet, utför följande steg:

    ![Omdirigerings-URL: er](./media/workday-tutorial/IC7829581.png "omdirigerings-URL: er")

    a. Klicka på **Lägg till rad**.

    b. I den **inloggning omdirigerings-URL** textrutan och **Mobile omdirigerings-URL** textrutan skriver den **inloggnings-URL** du har angett på den **Workday domän och URL: er** på Azure portal.

    c. I Azure-portalen på den **konfigurera inloggning** fönster, kopiera den **URL: en för utloggning**, och klistra in den i den **omdirigerings-URL för utloggning** textrutan.

    d. I **används för miljöer** textrutan väljer du miljönamnet.  

    >[!NOTE]
    > Värdet för attributet miljö är kopplad till värdet för klient-URL:  
    >-Om domännamnet för Workday klient-URL börjar med impl till exempel: *https://impl.workday.com/\<tenant\>/login-saml2.htmld*), **miljö** attributet måste anges till implementering.  
    >-Om domännamnet startas med något annat, måste du kontakta [Workday klienten supportteamet](https://www.workday.com/en-us/partners-services/services/support.html) att hämta den matchande **miljö** värde.

12. I den **SAML installationsprogrammet** avsnittet, utför följande steg:

    ![SAML-installationsprogrammet](./media/workday-tutorial/IC782926.png "SAML-konfiguration")

    a.  Välj **aktivera SAML-autentisering**.

    b.  Klicka på **Lägg till rad**.

13. I den **SAML identitetsleverantörer** avsnittet, utför följande steg:

    ![SAML-Identitetsproviders](./media/workday-tutorial/IC7829271.png "SAML Identitetsproviders")

    a. I den **namn på identitetsprovider** textrutan skriver du ett provider-namn (till exempel: *SPInitiatedSSO*).

    b. I Azure-portalen på den **konfigurera inloggning** fönster, kopiera den **SAML entitets-ID** värdet och klistra in den i den **utfärdare** textrutan.

    ![SAML-Identitetsproviders](./media/workday-tutorial/IC7829272.png "SAML Identitetsproviders")

    c. I Azure-portalen på den **konfigurera inloggning** fönster, kopiera den **URL: en för utloggning** värdet och klistra in den i den **svars-URL för utloggning** textrutan.

    d. I Azure-portalen på den **konfigurera inloggning** fönster, kopiera den **SAML enkel inloggning för tjänst-URL** värdet och klistra in den i den **tjänst-URL för IDP: N SSO** textrutan.

    e. I **används för miljöer** textrutan väljer du miljönamnet.

    f. Klicka på **providern offentliga nyckel identitetscertifikat**, och klicka sedan på **skapa**.

    ![Skapa](./media/workday-tutorial/IC782928.png "skapa")

    g. Klicka på **skapa x509 offentlig nyckel**.

    ![Skapa](./media/workday-tutorial/IC782929.png "skapa")

14. I den **visa x509 offentlig nyckel** avsnittet, utför följande steg:

    ![Visa x509 offentlig nyckel](./media/workday-tutorial/IC782930.png "visa x509 offentlig nyckel")

    a. I den **namn** textrutan anger du ett namn för ditt certifikat (till exempel: *PPE\_SP*).

    b. I den **giltig från** textrutan skriver giltigt från attributvärdet för certifikatet.

    c.  I den **giltig till** textrutan skriver giltigt till attribut-värde på certifikatet.

    > [!NOTE]
    > Du kan hämta giltiga från datum och giltiga datum från det nedladdade certifikatet genom att dubbelklicka på den.  Datumen visas under den **information** fliken.
    >
    >

    d.  Öppna din Base64-kodat certifikat i anteckningar och kopiera innehållet i den.

    e.  I den **certifikat** textrutan klistra in innehållet i Urklipp.

    f.  Klicka på **OK**.

15. Utför följande steg:

    ![Konfiguration av SSO](./media/workday-tutorial/WorkdaySSOConfiguratio.png "SSO-konfiguration")

    a.  I den **Service Provider-ID** textrutan typ **https://www.workday.com**.

    b. Välj **inte Deflate SP-initierat autentiseringsbegäran**.

    c. Som **begära signatur autentiseringsmetod**väljer **SHA256**.

    ![Autentiseringsmetod begäran signatur](./media/workday-tutorial/WorkdaySSOConfiguration.png "autentiseringsmetod begäran signatur") 

    d. Klicka på **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Kontrollera att du konfigurerar enkel inloggning på rätt sätt. Om du aktiverar enkel inloggning med felaktig inställning kanske du inte att ange programmet med dina autentiseringsuppgifter och blir utelåsta. I det här fallet Workday ger en backup log-url där användare kan logga in med sitt vanliga användarnamn och lösenord i följande format: [Your Workday URL]/login.flex?redirect=n

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/workday-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/workday-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/workday-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/workday-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-workday-test-user"></a>Skapa en testanvändare i Workday

I det här avsnittet skapar du en användare som kallas Britta Simon i Workday. Arbeta med [Workday klienten supportteamet](https://www.workday.com/en-us/partners-services/services/support.html) att lägga till användare i Workday-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Workday.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Workday, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Workday**.

    ![Workday-länk i listan med program](./media/workday-tutorial/tutorial_workday_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Workday i åtkomstpanelen du bör få automatiskt loggat in på ditt Workday-program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/workday-tutorial/tutorial_general_01.png
[2]: ./media/workday-tutorial/tutorial_general_02.png
[3]: ./media/workday-tutorial/tutorial_general_03.png
[4]: ./media/workday-tutorial/tutorial_general_04.png

[100]: ./media/workday-tutorial/tutorial_general_100.png

[200]: ./media/workday-tutorial/tutorial_general_200.png
[201]: ./media/workday-tutorial/tutorial_general_201.png
[202]: ./media/workday-tutorial/tutorial_general_202.png
[203]: ./media/workday-tutorial/tutorial_general_203.png
