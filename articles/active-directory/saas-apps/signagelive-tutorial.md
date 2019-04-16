---
title: 'Självstudier: Azure Active Directory-integrering med Signagelive | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Signagelive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d923f0e7-ad31-4d59-a6fd-f0e895e1a32d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36d4bb38d7a12edddac9d64ecc1ed3ee5a34456c
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59577820"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Självstudier: Azure Active Directory-integrering med Signagelive

I den här självstudien lär du dig att integrera Signagelive med Azure Active Directory (AD Azure).
Genom att integrera Signagelive med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Signagelive.
* Du kan aktivera användarna att logga in automatiskt till Signagelive (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Signagelive behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [utvärderingsmånad](https://azure.microsoft.com/pricing/free-trial/).
* En Signagelive single-sign-på-aktiverade prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Signagelive stöder SP-initierad SSO.

## <a name="add-signagelive-from-the-gallery"></a>Lägg till Signagelive från galleriet

Att konfigurera integrering av Signagelive i Azure AD, Lägg först till Signagelive från galleriet i din lista över hanterade SaaS-appar.

Om du vill lägga till Signagelive från galleriet, gör du följande:

1. I den [Azure-portalen](https://portal.azure.com), i rutan till vänster väljer du den **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan den **alla program** alternativet.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Signagelive**. 

     ![Signagelive i resultatlistan](common/search-new-app.png)

5. Välj **Signagelive** i resultatfönstret och väljer sedan den **Lägg till** för att lägga till programmet.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning Azure AD med Signagelive baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning ska fungera, måste du upprätta en länk mellan en Azure AD-användare och relaterade användaren i Signagelive.

Om du vill konfigurera och testa Azure AD enkel inloggning med Signagelive, slutföra följande byggblock:

1. [Konfigurera enkel inloggning i Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda den här funktionen.
2. [Konfigurera Signagelive enkel inloggning](#configure-signagelive-single-sign-on) att konfigurera inställningar för enkel inloggning på programsidan.
3. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning för Azure AD med Britta Simon.
4. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
5. [Skapa en testanvändare Signagelive](#create-a-signagelive-test-user) har en motsvarighet för Britta Simon i Signagelive som är länkad till en Azure AD-representation av användaren.
6. [Testa enkel inloggning](#test-single-sign-on) för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera Azure AD enkel inloggning med Signagelive, gör du följande:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Signagelive**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I den **väljer du en metod för enkel inloggning** dialogrutan **SAML** att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På den **Konfigurera enkel inloggning med SAML** väljer **redigera** att öppna den **SAML grundkonfiguration** dialogrutan.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I den **SAML grundkonfiguration** avsnittet, gör följande:

    ![Signagelive-domän och information om URL:er för enkel inloggning](common/sp-signonurl.png)

    I den **inloggnings-URL** anger en URL som använder följande mönster:  `https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med faktiska inloggnings-URL: en. För att få värdet kan kontakta den [Signagelive klienten supportteamet](mailto:support@signagelive.com) . Du kan också referera till de mönster som visas i den **SAML grundkonfiguration** avsnitt i Azure-portalen.

5. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** väljer **hämta** att ladda ned den **certifikat (Raw)** från de angivna alternativen per dina behov. Spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. I den **konfigurera Signagelive** avsnittet, Kopiera URL: er som du behöver.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-signagelive-single-sign-on"></a>Konfigurera Signagelive enkel inloggning

Om du vill konfigurera enkel inloggning på Signagelive sida, skicka de hämtade **certifikat (Raw)** och kopieras URL: er från Azure portal för att den [Signagelive supportteamet](mailto:support@signagelive.com). De säkerställer att SAML SSO-anslutningen är korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. I den **användaren** dialogrutan rutan, vidta följande steg.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fältet, anger du ”brittasimon@yourcompanydomain.extension”. Exempel: i det här fallet, du kan ange ”BrittaSimon@contoso.com”.

    c. Välj den **Show lösenord** och anteckna värdet som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Signagelive.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**, och välj sedan **Signagelive**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Signagelive**.

    ![Signagelive-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Välj den **Lägg till användare** knappen. I den **Lägg till tilldelning** dialogrutan **användare och grupper**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I den **användare och grupper** i dialogrutan den **användare** väljer **Britta Simon**. Klicka sedan på den **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran sedan, i den **Välj roll** dialogrutan väljer du rätt roll för användaren i listan. Klicka sedan på den **Välj** längst ned på skärmen.

7. I den **Lägg till tilldelning** dialogrutan den **tilldela** knappen.

### <a name="create-a-signagelive-test-user"></a>Skapa en Signagelive testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Signagelive. Arbeta med den [Signagelive supportteamet](mailto:support@signagelive.com) att lägga till användare i Signagelive-plattformen. Du måste skapa och aktivera användare innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av MyApps-portalen.

När du väljer den **Signagelive** panelen i MyApps-portalen som du ska logga in automatiskt. Mer information om MyApps-portalen finns i [vad är MyApps-portalen?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

