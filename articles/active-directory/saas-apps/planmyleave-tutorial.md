---
title: 'Självstudiekurs: Azure Active Directory-integrering med PlanMyLeave | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PlanMyLeave.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0d31cbe-7ae2-488b-9cf3-4927391fa744
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: d1192d3606dfbbf54a8177c2369a9cae8871d92b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094397"
---
# <a name="tutorial-azure-active-directory-integration-with-planmyleave"></a>Självstudiekurs: Azure Active Directory-integrering med PlanMyLeave

I den här självstudien får du lära dig hur du integrerar PlanMyLeave med Azure Active Directory (Azure AD).
Genom att integrera PlanMyLeave med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till PlanMyLeave.
* Du kan aktivera dina användare så att de automatiskt loggas in på PlanMyLeave (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med PlanMyLeave behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* PlanMyLeave enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* PlanMyLeave stöder **SP** initierade SSO

* PlanMyLeave stöder just in time-användaretablering **Just In Time**

## <a name="adding-planmyleave-from-the-gallery"></a>Lägga till PlanMyLeave från galleriet

Om du vill konfigurera integreringen av PlanMyLeave i Azure AD måste du lägga till PlanMyLeave från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till PlanMyLeave från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **PlanMyLeave**i sökrutan och välj **PlanMyLeave** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![PlanMyLeave i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med PlanMyLeave baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i PlanMyLeave upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med PlanMyLeave måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera PlanMyLeave Enkel inloggning](#configure-planmyleave-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa PlanMyLeave-testanvändare](#create-planmyleave-test-user)** – om du vill ha en motsvarighet till Britta Simon i PlanMyLeave som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med PlanMyLeave:

1. I [Azure-portalen](https://portal.azure.com/)väljer du **Enkel inloggning på**sidan Programintegrering av **PlanMyLeave** .

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![PlanMyLeave-domän och webbadresser med enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<company-name>.planmyleave.com/Login.aspx`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<company-name>.planmyleave.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta Supportteamet för [PlanMyLeave-klienten](mailto:support@planmyleave.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera PlanMyLeave.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-planmyleave-single-sign-on"></a>Konfigurera PlanMyLeave enkel inloggning

1. I ett annat webbläsarfönster loggar du in på din PlanMyLeave-klient som administratör.

2. Gå till **Systeminställningar .** Klicka sedan på **FöretagsSAML-inställningar** i avsnittet **Säkerhetshantering** .

    ![Konfigurera enkel inloggning på appsidan](./media/planmyleave-tutorial/tutorial_planmyleave_002.png) 

3. Klicka på redigeringsikonen i avsnittet **SAML-inställningar.**

    ![Konfigurera enkel inloggning på appsidan](./media/planmyleave-tutorial/tutorial_planmyleave_003.png)

4. Gör så här i avsnittet **Uppdatera SAML-inställningar:**

    ![Konfigurera enkel inloggning på appsidan](./media/planmyleave-tutorial/tutorial_planmyleave_004.png)

    a.  I textrutan för **inloggnings-URL** klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    b.  Öppna de nedladdade metadata, kopiera **X509Certificate-värdet** och klistra sedan in det i **textrutan Certifikat.**

    c. Set "**Is Enable**" to "**Yes**".

    d. Klicka på **Spara**. 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till PlanMyLeave.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **PlanMyLeave**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **PlanMyLeave**i programlistan .

    ![Länken PlanMyLeave i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-planmyleave-test-user"></a>Skapa Testanvändare för PlanMyLeave

I det här avsnittet skapas en användare som heter Britta Simon i PlanMyLeave. PlanMyLeave stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i PlanMyLeave skapas en ny efter autentisering.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta [PlanMyLeave supportteam](mailto:support@planmyleave.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen PlanMyLeave på åtkomstpanelen ska du automatiskt loggas in på den PlanMinaLeave som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

