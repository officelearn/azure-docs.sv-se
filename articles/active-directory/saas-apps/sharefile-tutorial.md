---
title: 'Självstudie: Azure Active Directory integrering med Citrix ShareFile | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Citrix ShareFile.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/18/2020
ms.author: jeedes
ms.openlocfilehash: cfbb704799a1884c689bd0de547526a33f1ba7ce
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651929"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Självstudie: Azure Active Directory integrering med Citrix ShareFile

I den här självstudien lär du dig hur du integrerar Citrix ShareFile med Azure Active Directory (Azure AD).
Du får följande fördelar när du integrerar Citrix ShareFile med Azure AD:

* Du kan styra i Azure AD vilka som har åtkomst till Citrix ShareFile.
* Du kan göra så att dina användare automatiskt loggas in på Citrix ShareFile (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna konfigurera Azure AD-integrering med Citrix ShareFile behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Citrix ShareFile-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Citrix ShareFile har stöd för **SP**-initierad enkel inloggning
* När du har konfigurerat Citrix ShareFile kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Lägga till Citrix ShareFile från galleriet

För att kunna konfigurera integreringen av Citrix ShareFile i Azure AD måste du lägga till Citrix ShareFile från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. Skriv **Citrix ShareFile** i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **Citrix ShareFile** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurera och testa Azure AD SSO

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Citrix ShareFile baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Citrix ShareFile upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med Citrix ShareFile måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera Citrix SHAREFILE SSO](#configure-citrix-sharefile-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa Citrix ShareFile-testanvändare](#create-citrix-sharefile-test-user)** – så att det finns en motsvarighet till Britta Simon i Citrix ShareFile som är länkad till Azure AD-representationen av användaren.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Citrix ShareFile** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<tenant-name>.sharefile.com/saml/login`

    b. I textrutan **Identifierare (entitets-ID)** skriver du en URL med följande mönster:

    - `https://<tenant-name>.sharefile.com`
    - `https://<tenant-name>.sharefile.com/saml/info`
    - `https://<tenant-name>.sharefile1.com/saml/info`
    - `https://<tenant-name>.sharefile1.eu/saml/info`
    - `https://<tenant-name>.sharefile.eu/saml/info`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: 
    
    - `https://<tenant-name>.sharefile.com/saml/acs`
    - `https://<tenant-name>.sharefile.eu/saml/<URL path>`
    - `https://<tenant-name>.sharefile.com/saml/<URL path>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Citrix ShareFile-kundsupporten](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **om att konfigurera Citrix ShareFile** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Citrix ShareFile.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Citrix ShareFile**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-citrix-sharefile-sso"></a>Konfigurera Citrix ShareFile SSO

1. Öppna ett annat webbläsarfönster och logga in på din **Citrix ShareFile**-företagswebbplats som administratör.

1. På den vänstra menyn klickar du på **Inställningar ikon**  ->  **säkerhets**  ->  **inloggning & säkerhets princip**.
   
    ![Konto administration](./media/sharefile-tutorial/settings-security.png "Konto administration")

1. Gör följande i dialogrutan **Single Sign-On/SAML 2.0 Configuration** (Konfiguration av enkel inloggning/SAML 2.0) under **Basic Settings** (Grundinställningar):
   
    ![Enkel inloggning](./media/sharefile-tutorial/saml-configuration.png "Enkel inloggning")
   
    a. Välj **Ja** i listan **Aktivera SAML**.

    b. Kopiera **ShareFile-utfärdaren/entitets-ID-** värdet och klistra in det i rutan **identifiera URL** i dialog rutan för den **grundläggande SAML-konfigurationen** i Azure Portal.
    
    c. I textrutan **Your IDP Issuer/ Entity ID** (Utfärdare av identitetsprovider/entitets-ID) klistrar du in värdet för **Azure Ad-identifierare**, som du har kopierat från Azure-portalen.

    d. Klicka på **Ändra** bredvid fältet **X.509-certifikat** och ladda sedan upp certifikatet du har laddat ned från Azure-portalen.
    
    e. I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen.
    
    f. I text rutan **Logga ut URL** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure Portal.

5. Klicka på **Spara** på Citrix ShareFile-hanteringsportalen.

## <a name="create-citrix-sharefile-test-user"></a>Skapa Citrix ShareFile-testanvändare

1. Logga in på din **Citrix ShareFile**-klient.

2. Klicka på **personer**  ->  **Hantera användare start**  ->  **skapa nya användare**  ->  **skapa anställd**.
   
    ![Skapa anställd](./media/sharefile-tutorial/create-user.png "Skapa anställd")

3. Gör följande i avsnittet **Grundläggande information**:
   
    ![Grundläggande information](./media/sharefile-tutorial/user-form.png "Grundläggande information")
   
    a. I textrutan **Förnamn** anger du **förnamnet** på användaren: **Britta**.
   
    b.  I textrutan **Efternamn** anger du **efternamnet** på användaren: **Simon**.
   
    c. I text rutan **e-postadress** skriver du e-postadressen för Britta Simon som **brittasimon \@ contoso.com**.

4. Klicka på **Lägg till användare**.
  
    >[!NOTE]
    >Azure AD-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta sitt konto innan det blir aktivt. Du kan använda alla anda verktyg eller API:er för att skapa Citrix ShareFile-användarkonton som tillhandahålls av Citrix ShareFile för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Citrix ShareFile-panelen i åtkomstpanelen bör du automatiskt loggas in på Citrix ShareFile som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

