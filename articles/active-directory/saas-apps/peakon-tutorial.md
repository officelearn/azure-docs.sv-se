---
title: 'Självstudier: Azure Active Directory-integrering med Peakon | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Peakon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: a98ec0d460e050f8158ffee1262dd85f0cd07d9a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256422"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Självstudier: Azure Active Directory-integrering med Peakon

I den här självstudien får du lära dig hur du integrerar Peakon med Azure Active Directory (AD Azure).
Integrera Peakon med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Peakon.
* Du kan aktivera användarna att vara automatiskt inloggad till Peakon (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Peakon, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Peakon enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Peakon **SP** och **IDP** -initierad SSO

## <a name="adding-peakon-from-the-gallery"></a>Att lägga till Peakon från galleriet

För att konfigurera integrering av Peakon i Azure AD, som du behöver lägga till Peakon från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Peakon från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Peakon**väljer **Peakon** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Peakon i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Peakon baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Peakon upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Peakon, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Peakon Single Sign-On](#configure-peakon-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Peakon](#create-peakon-test-user)**  – du har en motsvarighet för Britta Simon i Peakon som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Peakon:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Peakon** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Peakon domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://app.peakon.com/saml/<companyid>/metadata`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://app.peakon.com/saml/<companyid>/assert`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Peakon domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I rutan **Inloggnings-URL** anger du en URL: `https://app.peakon.com/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare och svars-URL som beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

7. På den **konfigurera Peakon** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-peakon-single-sign-on"></a>Konfigurera Peakon Single Sign-On

1. I ett annat webbläsarfönster, loggar du in Peakon som administratör.

2. I menyraden till vänster på sidan klickar du på **Configuration**, gå sedan till **integreringar**.

    ![Konfig](./media/peakon-tutorial/tutorial_peakon_config.png)

3. På **integreringar** klickar du på **enkel inloggning**.

    ![Enda](./media/peakon-tutorial/tutorial_peakon_single.png)

4. Under **enkel inloggning** klickar du på **aktivera**.

    ![Aktivera](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. På den **enkel inloggning för anställda som använder SAML** avsnittet, utför följande steg:

    ![Saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. I den **inloggnings-URL för enkel inloggning** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    b. I den **utloggnings-URL för enkel inloggning** textrutan klistra in värdet för **URL för utloggning**, som du har kopierat från Azure-portalen.

    c. Klicka på **Välj fil** att ladda upp det certifikat som du har hämtat från Azure-portalen i rutan certifikat.

    d. Klickar du på den **ikonen** att kopiera den **entitets-ID** och klistra in i **identifierare** -textrutan i **SAML grundkonfiguration** avsnittet på Azure-portalen.

    e. Klicka på den **ikonen** att kopiera den **svars-URL (ACS)** och klistra in i **svars-URL** textrutan i **grundläggande SAML-konfiguration** avsnittet på Azure-portalen.

    f. Klicka på **Spara**

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

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Peakon.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Peakon**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Peakon**.

    ![Länken Peakon i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-peakon-test-user"></a>Skapa Peakon testanvändare

För att aktivera Azure AD-användare att logga in på Peakon, måste de etableras i Peakon.  
När det gäller Peakon är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på webbplatsen Peakon företag som administratör.

2. I menyraden till vänster på sidan klickar du på **Configuration**, gå sedan till **anställda**.

    ![Medarbetaren](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. På upp till höger på sidan, klickar du på **Lägg till medarbetare**.

      ![Lägg till medarbetare](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. På den **nyanställd** dialogrutan utför följande steg:

     ![Den nya medarbetaren](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. I den **namn** textrutan typ förnamn som **Britta** - och efternamn som **simon**.

    b. I den **e-post** textrutan, skriver du in den e-postadressen som **Brittasimon\@contoso.com**.

    c. Klicka på **skapa medarbetare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Peakon i åtkomstpanelen, bör det vara loggas in automatiskt till Peakon som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

