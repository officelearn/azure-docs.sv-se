---
title: 'Självstudiekurs: Azure Active Directory-integrering med Signagelive | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Signagelive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d923f0e7-ad31-4d59-a6fd-f0e895e1a32d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dab2fd0ee2f25e835b4bd07a3534475d3d93b5e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160938"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Självstudiekurs: Azure Active Directory-integrering med Signagelive

I den här självstudien lär du dig att integrera Signagelive med Azure Active Directory (AD Azure).
Genom att integrera Signagelive med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Signagelive.
* Du kan aktivera dina användare så att de automatiskt loggas in på Signagelive (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Signagelive behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [utvärderingsversion på en månad](https://azure.microsoft.com/pricing/free-trial/).
* En Signagelive-prenumeration med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Signagelive stöder SP-initierad SSO.

## <a name="add-signagelive-from-the-gallery"></a>Lägg till Signagelive från galleriet

Om du vill konfigurera integreringen av Signagelive i Azure AD lägger du först till Signagelive från galleriet i listan över hanterade SaaS-appar.

Så här lägger du till Signagelive från galleriet:

1. Välj Azure Active Directory-ikonen i **Azure Active Directory** [Azure-portalen](https://portal.azure.com)i den vänstra rutan.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise Applications**och välj sedan alternativet **Alla program.**

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program markerar du knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Signagelive**. 

     ![Signagelive i resultatlistan](common/search-new-app.png)

5. Välj **Signagelive** i resultatfönstret och välj sedan knappen **Lägg** till för att lägga till programmet.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning Azure AD med Signagelive baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste du upprätta en länk mellan en Azure AD-användare och den relaterade användaren i Signagelive.

Om du vill konfigurera och testa en azure AD-inloggning med Signagelive slutför du först följande byggblock:

1. [Konfigurera enkel inloggning i Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda den här funktionen.
2. [Konfigurera Signagelive enkel inloggning](#configure-signagelive-single-sign-on) för att konfigurera de enskilda inloggningsinställningarna på programsidan.
3. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning för Azure AD med Britta Simon.
4. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
5. [Skapa en Signagelive-testanvändare](#create-a-signagelive-test-user) för att ha en motsvarighet till Britta Simon i Signagelive som är länkad till Azure AD-representationen av användaren.
6. [Testa enkel inloggning](#test-single-sign-on) för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du enkel Azure AD-inloggning med Signagelive:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Signagelive**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en enda inloggningsmetod** väljer du **SAML** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan Konfigurera enkel inloggning med SAML väljer du **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration.** **Set up single sign-on with SAML**

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![Signagelive-domän och information om URL:er för enkel inloggning](common/sp-signonurl.png)

    Ange en URL som använder följande mönster i rutan **Inloggnings-URL:**`https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta Supportteamet för [Signagelive Client](mailto:support@signagelive.com) för att få värdet. Du kan också referera till de mönster som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan Konfigurera enkel inloggning med SAML väljer du **Hämta** i avsnittet **SAML-signeringscertifikat** för att hämta **certifikatet (Raw)** från de angivna alternativen enligt dina behov. **Set up Single Sign-On with SAML** Spara den sedan på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. Kopiera webbadresserna som du behöver i avsnittet **Konfigurera Signagelive.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-signagelive-single-sign-on"></a>Konfigurera Signagelive Enkel inloggning

Om du vill konfigurera enkel inloggning på Signagelive-sidan skickar du det hämtade **certifikatet (Raw)** och kopierade url:er från Azure-portalen till [Signagelive-supportteamet](mailto:support@signagelive.com). De säkerställer att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Gör följande i dialogrutan **Användare.**

    ![Dialogrutan Användare](common/user-properties.png)

    a. Ange **BrittaSimon**i fältet **Namn** .
  
    b. Ange **User name** "brittasimon@yourcompanydomain.extension". I det här fallet kan duBrittaSimon@contoso.comtill exempel skriva " ".

    c. Markera kryssrutan **Visa lösenord** och notera sedan värdet som visas i rutan Lösenord.

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Signagelive.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Signagelive**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Signagelive**.

    ![Signagelive-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Välj knappen **Lägg till användare.** Välj sedan **Användare och grupper**i dialogrutan Lägg till **tilldelning** .

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. Välj **Britta Simon**i listan **Användare** **och grupper** . Klicka sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Klicka sedan på **knappen Välj** längst ned på skärmen.

7. Välj knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-a-signagelive-test-user"></a>Skapa en Signagelive-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Signagelive. Samarbeta med [Signagelives supportteam](mailto:support@signagelive.com) för att lägga till användarna i Signagelive-plattformen. Du måste skapa och aktivera användare innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du din Azure AD-konfiguration med hjälp av MyApps-portalen.

När du väljer **panelen Signagelive** i MyApps-portalen bör du loggas in automatiskt. Mer information om MyApps-portalen finns i [Vad är MyApps-portalen?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

