---
title: 'Självstudier: Azure Active Directory-integrering med Workday | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aba2d3bab7d709b4bb9ac18e4a9c6ed052a5fb83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086970"
---
# <a name="tutorial-integrate-workday-with-azure-active-directory"></a>Självstudier: Integrera Workday med Azure Active Directory

I de här självstudierna lär du dig att integrera Workday med Azure Active Directory (AD Azure). När du integrerar Workday med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Workday.
* Ge dina användare att automatiskt inloggad till Workday med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Aktiverat prenumeration workday enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för workday **SP** -initierad SSO.

## <a name="adding-workday-from-the-gallery"></a>Att lägga till Workday från galleriet

För att konfigurera integrering av Workday i Azure AD, som du behöver lägga till Workday från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Workday** i sökrutan.
1. Välj **Workday** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Workday med en testanvändare kallas **Britta Simon**. För enkel inloggning ska fungera, måste du upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Workday.

Om du vill konfigurera och testa Azure AD enkel inloggning med Workday, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera Workday](#configure-workday)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare i Workday](#create-workday-test-user)**  har en motsvarighet för Britta Simon i Workday som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Workday** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** ange värdena för följande fält:

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://www.workday.com`

    c. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > De här värdena är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och svars-URL. Svars-URL måste ha en underdomän till exempel: www, wd2, wd3, wd3 impl, wd5, wd5 impl).
    > Med något som `http://www.myworkday.com` fungerar men `http://myworkday.com` inte. Kontakta [Workday klienten supportteamet](https://www.workday.com/en-us/partners-services/services/support.html) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Dina Workday-programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Workday program som förväntar **nameidentifier** mappas med **user.mail**, **UPN**, o.s.v., så du behöver redigera attribut mappar genom att klicka på  **Redigera** ikon och ändra attributet mappningen.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Här har vi mappat namn-ID med UPN (user.userprincipalname) som standard. Du behöver mappa namn-ID med faktiska användar-ID i Workday-konto (e-post, UPN, etc.) för lyckad enkel inloggning att fungera.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Base64)** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. Att ändra den **signering** alternativ enligt dina krav, klickar du på **redigera** knappen för att öppna **SAML-signeringscertifikat** dialogrutan.

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Välj **inloggning SAML-svar och försäkran** för **signering alternativet**.

    b. Klicka på **Spara**

1. På den **konfigurera Workday** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-workday"></a>Konfigurera Workday

1. I ett annat webbläsarfönster, loggar du in din Workday företagets webbplats som administratör.

2. I den **sökrutan** sökning med namnet **redigera klientkonfiguration – Security** uppe till vänster på startsidan.

    ![Redigera klient Security](./media/workday-tutorial/IC782925.png "redigera klient-säkerhet")

3. I den **omdirigerings-URL: er** avsnittet, utför följande steg:

    ![Omdirigerings-URL: er](./media/workday-tutorial/IC7829581.png "omdirigerings-URL: er")

    a. Klicka på **Lägg till rad**.

    b. I den **inloggning omdirigerings-URL**, **omdirigerings-URL för Sessionstidsgräns** och **Mobile omdirigerings-URL** textrutan klistra in den **inloggnings-URL** som du har kopierat från den **konfigurera Workday** på Azure portal.

    c. I den **omdirigerings-URL för utloggning** textrutan klistra in den **URL för utloggning** som du har kopierat från den **konfigurera Workday** på Azure portal.

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

    a.  I den **Service Provider-ID** textrutan typ **https://www.workday.com** .

    b. Välj **inte Deflate SP-initierat autentiseringsbegäran**.

    c. Som **begära signatur autentiseringsmetod**väljer **SHA256**.

    ![Autentiseringsmetod begäran signatur](./media/workday-tutorial/WorkdaySSOConfiguration.png "autentiseringsmetod begäran signatur") 

    d. Klicka på **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Kontrollera att du konfigurerar enkel inloggning på rätt sätt. Om du aktiverar enkel inloggning med felaktig inställning kanske du inte att ange programmet med dina autentiseringsuppgifter och blir utelåsta. I det här fallet Workday ger en backup log-url där användare kan logga in med sitt vanliga användarnamn och lösenord i följande format: [Your Workday URL]/login.flex?redirect=n

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas Britta Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Workday.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **Workday**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **Britta Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-workday-test-user"></a>Skapa testanvändare i Workday

I det här avsnittet skapar du en användare som kallas Britta Simon i Workday. Arbeta med [Workday klienten supportteamet](https://www.workday.com/en-us/partners-services/services/support.html) att lägga till användare i Workday-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Workday i åtkomstpanelen, bör det vara loggas in automatiskt till Workday som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
