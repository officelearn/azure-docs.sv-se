---
title: 'Självstudier: Azure Active Directory-integrering med Workday | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 8451fd692409933803f5f8023f1e1161c3a97daf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60375041"
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Självstudier: Azure Active Directory-integrering med Workday

I den här självstudien får du lära dig hur du integrerar Workday med Azure Active Directory (AD Azure).
Integrera Workday med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Workday.
* Du kan aktivera användarna att vara automatiskt inloggad till Workday (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Workday, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Workday enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för workday **SP** och **IDP** -initierad SSO

## <a name="adding-workday-from-the-gallery"></a>Att lägga till Workday från galleriet

För att konfigurera integrering av Workday i Azure AD, som du behöver lägga till Workday från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Workday från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Workday**väljer **Workday** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Arbetsdag i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Workday baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Workday upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Workday, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Workday Single Sign-On](#configure-workday-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare i Workday](#create-workday-test-user)**  – du har en motsvarighet för Britta Simon i Workday som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Workday:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Workday** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Workday domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https:\//impl.workday.com/<tenant>/login-saml2.flex`

    b. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://www.workday.com`

5. Klicka på **ange ytterligare webbadresser** och utföra följande steg:

    ![Workday domän och URL: er med enkel inloggning för information](./media/workday-tutorial/reply.png)

    I textrutan **Svars-URL** skriver du en URL med följande mönster: `https:\//impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > De här värdena är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och svars-URL. Svars-URL måste ha en underdomän till exempel: www, wd2, wd3, wd3 impl, wd5, wd5 impl).
    > Med något som `http://www.myworkday.com` fungerar men `http://myworkday.com` inte. Kontakta [Workday klienten supportteamet](https://www.workday.com/en-us/partners-services/services/support.html) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Dina Workday-programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Workday program som förväntar **nameidentifier** mappas med **user.mail**, **UPN** osv, så du behöver redigera attribut mappar genom att klicka på **redigera**  ikon och ändra attributet mappningen.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Här har vi mappat namn-ID med UPN (user.userprincipalname) som standard. Du behöver mappa namn-ID med faktiska användar-ID i Workday-konto (din e-post, UPN etc.) för lyckad enkel inloggning att fungera.

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

8. På den **konfigurera Workday** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-workday-single-sign-on"></a>Konfigurera Workday enkel inloggning

1. I ett annat webbläsarfönster logga du in på webbplatsen för Workday-företag som administratör.

2. I den **sökrutan** sökning med namnet **redigera klientkonfiguration – Security** uppe till vänster på startsidan.

    ![Redigera klient Security](./media/workday-tutorial/IC782925.png "redigera klient-säkerhet")

3. I den **omdirigerings-URL: er** avsnittet, utför följande steg:

    ![Omdirigerings-URL: er](./media/workday-tutorial/IC7829581.png "omdirigerings-URL: er")

    a. Klicka på **Lägg till rad**.

    b. I den **inloggning omdirigerings-URL** textrutan och **Mobile omdirigerings-URL** textrutan skriver den **inloggnings-URL** du har angett på den **grundläggande SAML-konfiguration**  på Azure portal.

    c. I Azure-portalen på den **konfigurera Workday** avsnittet, kopiera den **URL för utloggning**, och klistra in den i den **omdirigerings-URL för utloggning** textrutan.

    d. I **används för miljöer** textrutan väljer du miljönamnet.  

   > [!NOTE]
   > Värdet för attributet miljö är kopplad till värdet för klient-URL:  
   > -Om domännamnet för Workday klient-URL börjar med impl till exempel: *https:\//impl.workday.com/\<klient\>/login-saml2.flex*), **miljö**attributet måste anges till implementering.  
   > -Om domännamnet startas med något annat, måste du kontakta [Workday klienten supportteamet](https://www.workday.com/en-us/partners-services/services/support.html) att hämta den matchande **miljö** värde.

4. I den **SAML installationsprogrammet** avsnittet, utför följande steg:

    ![SAML-konfiguration](./media/workday-tutorial/IC782926.png "SAML-konfiguration")

    a.  Välj **aktivera SAML-autentisering**.

    b.  Klicka på **Lägg till rad**.

5. I den **SAML identitetsleverantörer** avsnittet, utför följande steg:

    ![SAML-Identitetsproviders](./media/workday-tutorial/IC7829271.png "SAML Identitetsproviders")

    a. I den **namn på identitetsprovider** textrutan skriver du ett provider-namn (till exempel: *SPInitiatedSSO*).

    b. I Azure-portalen på den **konfigurera Workday** avsnittet, kopiera den **Azure AD-identifierare** värdet och klistra in den i den **utfärdare** textrutan.

    ![SAML-Identitetsproviders](./media/workday-tutorial/IC7829272.png "SAML Identitetsproviders")

    c. I Azure-portalen på den **konfigurera Workday** avsnittet, kopiera den **URL för utloggning** värdet och klistra in den i den **svars-URL för utloggning** textrutan.

    d. I Azure-portalen på den **konfigurera Workday** avsnittet, kopiera den **inloggnings-URL** värdet och klistra in den i den **tjänst-URL för IDP: N SSO** textrutan.

    e. I **används för miljöer** textrutan väljer du miljönamnet.

    f. Klicka på **providern offentliga nyckel identitetscertifikat**, och klicka sedan på **skapa**.

    ![Skapa](./media/workday-tutorial/IC782928.png "skapa")

    g. Klicka på **skapa x509 offentlig nyckel**.

    ![Skapa](./media/workday-tutorial/IC782929.png "skapa")

6. I den **visa x509 offentlig nyckel** avsnittet, utför följande steg:

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

7. Utför följande steg:

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

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Workday.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Workday**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Workday**.

    ![Workday-länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-workday-test-user"></a>Skapa testanvändare i Workday

I det här avsnittet skapar du en användare som kallas Britta Simon i Workday. Arbeta med [Workday klienten supportteamet](https://www.workday.com/en-us/partners-services/services/support.html) att lägga till användare i Workday-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Workday i åtkomstpanelen, bör det vara loggas in automatiskt till Workday som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

