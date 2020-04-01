---
title: 'Självstudiekurs: Azure Active Directory-integrering med Icertis Contract Management Platform | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Icertis Contract Management Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6627e6dd-f559-4cd4-a509-f6d9a4961b49
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 4d105fd9d0611ad56cd5ec2ecaa36bdcf9deed18
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100731"
---
# <a name="tutorial-azure-active-directory-integration-with-icertis-contract-management-platform"></a>Självstudiekurs: Azure Active Directory-integrering med Icertis Contract Management Platform

I den här självstudien får du lära dig hur du integrerar Icertis Contract Management Platform med Azure Active Directory (Azure AD).
Genom att integrera Icertis Contract Management Platform med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Icertis Contract Management Platform.
* Du kan aktivera dina användare automatiskt inloggad på Icertis Contract Management Platform (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Icertis Contract Management Platform behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Icertis Contract Management Platform enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Icertis Contract Management Platform stöder **SP** initierade SSO

## <a name="adding-icertis-contract-management-platform-from-the-gallery"></a>Lägga till Icertis Contract Management Platform från galleriet

Om du vill konfigurera integreringen av Icertis Contract Management Platform i Azure AD måste du lägga till Icertis Contract Management Platform från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Icertis Contract Management Platform från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Icertis Contract Management Platform**i sökrutan och välj **Icertis Contract Management Platform** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![Icertis Contract Management Platform i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Icertis Contract Management Platform baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Icertis Contract Management Platform upprättas.

Om du vill konfigurera och testa azure AD-enkel inloggning med Icertis Contract Management Platform måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Icertis Contract Management Platform Single Sign-On](#configure-icertis-contract-management-platform-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Icertis Contract Management Platform testanvändare](#create-icertis-contract-management-platform-test-user)** - att ha en motsvarighet till Britta Simon i Icertis Contract Management Platform som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Icertis Contract Management Platform:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på programintegreringssidan **för Icertis Contract Management Platform** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Icertis Contract Management Platform Domän och WEBBADRESSER enkel inloggningsinformation](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<company name>.icertis.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<company name>.icertis.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Icertis Contract Management Platform Client support team](https://www.icertis.com/company/contact/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Icertis Contract Management Platform.** För **inloggnings-URL**använder du värdet med följande mönster:`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ är klient-ID för Azure AD-prenumeration.

    ![Kopiera konfigurations-URL:er](media/icertisicm-tutorial/configurls.png)

    a. Azure AD-identifierare

    b. Utloggnings-URL

### <a name="configure-icertis-contract-management-platform-single-sign-on"></a>Konfigurera Icertis Kontraktshanteringsplattform enkel inloggning

Om du vill konfigurera enkel inloggning på **Icertis Kontrakt Management Platform-sidan** måste du skicka den nedladdade **XML-koden för federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [Icertis Contract Management Platform supportteam](https://www.icertis.com/company/contact/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** `brittasimon@yourcompanydomain.extension`. Till exempel BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Icertis Contract Management Platform.

1. I Azure-portalen väljer du **Företagsprogram**, väljer **Alla program**och väljer sedan **Icertis Contract Management Platform**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Icertis Contract Management Platform**i programlistan .

    ![Länken Icertis Contract Management Platform i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-icertis-contract-management-platform-test-user"></a>Skapa testanvändare för Icertis Contract Management Platform

I det här avsnittet skapar du en användare som heter Britta Simon i Icertis Contract Management Platform. Arbeta med [Icertis Contract Management Platform supportteam](https://www.icertis.com/company/contact/) för att lägga till användarna i Icertis Contract Management Platform-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Icertis Contract Management Platform på åtkomstpanelen ska du automatiskt loggas in på den Icertis Contract Management Platform som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)