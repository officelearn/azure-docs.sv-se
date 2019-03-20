---
title: 'Självstudier: Azure Active Directory-integrering med MyWorkDrive | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: c0557ccc995e591187aae24dfd7078755fec3e9a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57885529"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>Självstudier: Azure Active Directory-integrering med MyWorkDrive

I den här självstudien får du lära dig hur du integrerar MyWorkDrive med Azure Active Directory (AD Azure).
Integrera MyWorkDrive med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till MyWorkDrive.
* Du kan aktivera användarna att vara automatiskt inloggad till MyWorkDrive (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med MyWorkDrive, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* MyWorkDrive enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för MyWorkDrive **SP** och **IDP** -initierad SSO

## <a name="adding-myworkdrive-from-the-gallery"></a>Att lägga till MyWorkDrive från galleriet

För att konfigurera integrering av MyWorkDrive i Azure AD, som du behöver lägga till MyWorkDrive från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till MyWorkDrive från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **MyWorkDrive**väljer **MyWorkDrive** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![MyWorkDrive i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med MyWorkDrive baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i MyWorkDrive upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med MyWorkDrive, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera MyWorkDrive Single Sign-On](#configure-myworkdrive-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare MyWorkDrive](#create-myworkdrive-test-user)**  – du har en motsvarighet för Britta Simon i MyWorkDrive som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med MyWorkDrive:

1. I den [Azure-portalen](https://portal.azure.com/)på den **MyWorkDrive** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera appen i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![MyWorkDrive domän och URL: er med enkel inloggning för information](common/both-replyurl.png)

    I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![MyWorkDrive domän och URL: er med enkel inloggning för information](common/both-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Ange ditt företag MyWorkDrive Server värd name:e.g.
    > 
    > Svars-URL: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Inloggnings-URL:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Kontakta [MyWorkDrive supportteamet](mailto:support@myworkdrive.com) om du är osäker på hur du ställer in dina egna värdnamn och SSL-certifikat för dessa värden.

6. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

### <a name="configure-myworkdrive-single-sign-on"></a>Konfigurera MyWorkDrive Single Sign-On

1. I ett annat webbläsarfönster, logga in på MyWorkDrive som en administratör.

2. På MyWorkDrive servern admin-panelen, klickar du på **ENTERPRISE** och utför följande steg:

    ![Administratören](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Aktivera **SAML/ADFS SSO**.

    b. Välj **SAML - Azure AD**

    c. I den **Azure Appfederationsmetadata** textrutan klistra in värdet för **Appfederationsmetadata** som du har kopierat från Azure-portalen.

    d. Klicka på **Spara**

    >[!NOTE]
    >För ytterligare information finns i [MyWorkDrive Azure AD support-artikeln](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till MyWorkDrive.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **MyWorkDrive**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **MyWorkDrive**.

    ![Länken MyWorkDrive i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-myworkdrive-test-user"></a>Skapa MyWorkDrive testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i MyWorkDrive. Arbeta med [MyWorkDrive supportteamet](mailto:support@myworkdrive.com) att lägga till användare i MyWorkDrive-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen MyWorkDrive i åtkomstpanelen, bör det vara loggas in automatiskt till MyWorkDrive som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

