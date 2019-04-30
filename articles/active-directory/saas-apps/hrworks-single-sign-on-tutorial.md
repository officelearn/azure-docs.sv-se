---
title: 'Självstudier: Azure Active Directory-integrering med HRworks enkel inloggning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HRworks enkel inloggning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: f69ef2c7c260d8df918fb2495bded6910d6b17b1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123779"
---
# <a name="tutorial-azure-active-directory-integration-with-hrworks-single-sign-on"></a>Självstudier: Azure Active Directory-integrering med HRworks enkel inloggning

I den här självstudien får du lära dig hur du integrerar HRworks enkel inloggning med Azure Active Directory (AD Azure).
Integrera HRworks enkel inloggning med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till HRworks enkel inloggning.
* Du kan aktivera användarna att vara automatiskt inloggad till HRworks enkel inloggning (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med HRworks enkel inloggning, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* HRworks enkel inloggning för enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för HRworks Single Sign-On **SP** -initierad SSO

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Att lägga till HRworks enkel inloggning från galleriet

För att konfigurera integrering av HRworks enkel inloggning i Azure AD, som du behöver lägga till HRworks enkel inloggning från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till HRworks enkel inloggning från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **HRworks Single Sign-On**väljer **HRworks Single Sign-On** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![HRworks enkel inloggning i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med HRworks enkel inloggning baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i HRworks enkel inloggning ska upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med HRworks enkel inloggning, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera HRworks enkel inloggning för enkel inloggning](#configure-hrworks-single-sign-on-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa HRworks Single Sign-On testanvändare](#create-hrworks-single-sign-on-test-user)**  – du har en motsvarighet för Britta Simon i HRworks enkel inloggning som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med HRworks enkel inloggning:

1. I den [Azure-portalen](https://portal.azure.com/)på den **HRworks Single Sign-On** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![HRworks enkel inloggning på domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [HRworks enkel inloggning för klienten supportteamet](mailto:support@hrworks.de) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **ange upp HRworks enkel inloggning** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-hrworks-single-sign-on-single-sign-on"></a>Konfigurera HRworks enkel inloggning för enkel inloggning

1. I ett annat webbläsarfönster inloggningen till HRworks enkel inloggning som administratör.

2. Klicka på **administratör** > **grunderna** > **Security** > **enkel inloggning** från till vänster på menyn menyraden och utför följande steg:

       ![Konfigurera enkel inloggning](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Kontrollera den **Använd enkel inloggning** box.

    b. Välj **XML-Metadata** som **metadata indatametod**.

    c. Välj **enskilda NameID identifierare** som **värde för NameID**.

    d. I anteckningar, öppna den XML-Metadata som du laddade ned från Azure-portalen, kopiera innehållet och klistra in den i den **Metadata** textrutan.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** skriver användarnamnet som BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till HRworks enkel inloggning.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **HRworks Single Sign-On**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **HRworks Single Sign-On**.

    ![Länken HRworks enkel inloggning i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Skapa HRworks Single Sign-On testanvändare

Om du vill aktivera Azure AD-användare registrera i att HRworks enkel inloggning, de måste etableras i HRworks enkel inloggning. I HRworks enkel inloggning, är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på HRworks Single Sign-On som administratör.

2. Klicka på **administratör** > **personer** > **personer** > **ny person** från den vänster sida av menyraden.

     ![Konfigurera enkel inloggning](./media/hrworks-single-sign-on-tutorial/configure02.png)

3. På popup-fönstret klickar du på **nästa**.

    ![Konfigurera enkel inloggning](./media/hrworks-single-sign-on-tutorial/configure03.png)

4. På den **Skapa ny person med land för juridiska villkor** popup-fönstret fyller du i respektive information som **Förnamn**, **efternamn** och klicka på **skapa**.
    
    ![Konfigurera enkel inloggning](./media/hrworks-single-sign-on-tutorial/configure04.png)

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen HRworks enkel inloggning i åtkomstpanelen bör du vara loggas in automatiskt till den HRworks enkel inloggning som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

