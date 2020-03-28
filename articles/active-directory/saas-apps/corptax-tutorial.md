---
title: 'Självstudiekurs: Azure Active Directory-integrering med Corptax | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Corptax.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: fdad2893-41f4-4056-b6d2-ec3d050eb350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cd09a35d5796cdc4267c9d01a901b4e3b812d7c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67104386"
---
# <a name="tutorial-azure-active-directory-integration-with-corptax"></a>Självstudiekurs: Azure Active Directory-integrering med Corptax

I den här självstudien får du lära dig hur du integrerar Corptax med Azure Active Directory (Azure AD).
Genom att integrera Corptax med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Corptax.
* Du kan aktivera dina användare automatiskt inloggad på Corptax (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Corptax behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Corptax enda inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Corptax stöder **SP** initierade SSO

## <a name="adding-corptax-from-the-gallery"></a>Lägga Corptax från galleriet

Om du vill konfigurera integreringen av Corptax i Azure AD måste du lägga till Corptax från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Corptax från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select_azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise_applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add_new_app.png)

4. Skriv **Corptax**i sökrutan och välj **Corptax** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![Corptax i resultatlistan](common/search_new_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Corptax baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Corptax upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Corptax måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Corptax Single Sign-On](#configure-corptax-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Corptax testanvändare](#create-corptax-test-user)** - att ha en motsvarighet till Britta Simon i Corptax som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Corptax:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Corptax-programintegration**. **Corptax**

    ![Konfigurera länk för enkel inloggning](common/select_sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select_saml_option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit_urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Corptax Domän och webbadresser enkel inloggningsinformation](common/sp_intiated.png)

    Skriv en URL i textrutan **Sign-on-URL:**`https://asp.corptax.com`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Ladda ned** för att ladda ned **XML för federationsmetadata** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-corptax-single-sign-on"></a>Konfigurera corptax enkel inloggning

Om du vill konfigurera enkel inloggning på **Corptax-sidan** måste du skicka den nedladdade **FEDERATION Metadata XML** till [Corptax supportteam](https://connect.corptax.com/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new_user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user_properties.png)

    a. Ange **BrittaSimon**i fältet **Namn** .

    b. Skriv i fältet **Användarnamn**`brittasimon\@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Corptax.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Corptax**.

    ![Bladet Företagsprogram](common/enterprise_applications.png)

2. Skriv och välj **Corptax**i programlistan .

    ![Corptax-länken i listan Över ansökningar](common/all_applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users_groups_blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add_assign_user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-corptax-test-user"></a>Skapa Corptax-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Corptax. Arbeta med [Corptax supportteam](https://connect.corptax.com/) för att lägga till användarna i Corptax-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.
När du klickar på Corptax-panelen i åtkomstpanelen bör du omdirigeras till nedanstående Corptax-sida- 

![image](media/corptax-tutorial/corptaxlogin.png)

I textrutan **Miljö** skriver du lämplig miljö, du bör automatiskt loggas in på den Corptax som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
