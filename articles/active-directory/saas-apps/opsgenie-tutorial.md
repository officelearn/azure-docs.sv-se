---
title: 'Självstudier: Azure Active Directory-integrering med OpsGenie | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: f395ae51d51e56668482ad6c8d446bdd31050fea
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280181"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Självstudier: Azure Active Directory-integrering med OpsGenie

I den här självstudien får du lära dig hur du integrerar OpsGenie med Azure Active Directory (AD Azure).
Integrera OpsGenie med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till OpsGenie.
* Du kan aktivera användarna att vara automatiskt inloggad till OpsGenie (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med OpsGenie, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* OpsGenie enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för OpsGenie **SP** -initierad SSO

## <a name="adding-opsgenie-from-the-gallery"></a>Att lägga till OpsGenie från galleriet

För att konfigurera integrering av OpsGenie i Azure AD, som du behöver lägga till OpsGenie från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till OpsGenie från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **OpsGenie**väljer **OpsGenie** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![OpsGenie i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med OpsGenie baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i OpsGenie upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med OpsGenie, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera OpsGenie Single Sign-On](#configure-opsgenie-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare OpsGenie](#create-opsgenie-test-user)**  – du har en motsvarighet för Britta Simon i OpsGenie som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med OpsGenie:

1. I den [Azure-portalen](https://portal.azure.com/)på den **OpsGenie** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![OpsGenie domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    I rutan **Inloggnings-URL** anger du en URL: `https://app.opsgenie.com/auth/login`

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

6. På den **konfigurera OpsGenie** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-opsgenie-single-sign-on"></a>Konfigurera OpsGenie Single Sign-On

1. Öppna en annan webbläsare instans och sedan logga in till OpsGenie som administratör.

2. Klicka på **inställningar**, och klicka sedan på den **Single Sign On** fliken.
   
    ![OpsGenie Single Sign-On](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

3. Välj för att aktivera SSO **aktiverad**.
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

4. I den **Provider** klickar du på den **Azure Active Directory** fliken.
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

5. Utför följande steg på sidan för Azure Active Directory-dialogrutan:
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. I den **slutpunkt för SAML 2.0** textrutan klistra in **inloggnings-URL**värde som du har kopierat från Azure-portalen.
    
    b. I den **Metadata-Url:** textrutan klistra in **Appfederationsmetadata** värde som du har kopierat från Azure-portalen.
    
    c. Klicka på **Spara ändringar**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till OpsGenie.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **OpsGenie**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **OpsGenie**.

    ![Länken OpsGenie i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-opsgenie-test-user"></a>Skapa OpsGenie testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i OpsGenie. 

1. Logga in på din OpsGenie-klient som en administratör i ett webbläsarfönster.

2. Gå till användarlistan genom att klicka på **användare** i vänster panel.
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. Klicka på **Lägg till användare**.

4. I dialogrutan **Lägg till användare** utför du följande steg:
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
    a. I den **e-post** textrutan e-postadressen sorts BrittaSimon åtgärdas i Azure Active Directory.
   
    b. I den **fullständigt namn** textrutan typ **Britta Simon**.
   
    c. Klicka på **Spara**. 

>[!NOTE]
>Britta får ett e-postmeddelande med instruktioner för att konfigurera sin profil.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen OpsGenie i åtkomstpanelen, bör det vara loggas in automatiskt till OpsGenie som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

