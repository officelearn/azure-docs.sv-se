---
title: 'Självstudiekurs: Azure Active Directory-integrering med Mitt prispoäng Toppunder-/topplag | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och My Award Points Top Sub/Top Team.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a7a08eed-7a6b-4a83-8f8e-0add6d2fb8cf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: b1de9e394dd1e6b4c47b8de3175dd1e8fba1d87e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161335"
---
# <a name="tutorial-azure-active-directory-integration-with-my-award-points-top-subtop-team"></a>Självstudiekurs: Azure Active Directory-integrering med Mitt award points top sub/top team

I den här självstudien får du lära dig hur du integrerar Mitt Award Points Top Sub/Top Team med Azure Active Directory (Azure AD).
Genom att integrera mitt prispoäng toppunder-/topplag med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Mitt bonuspoäng toppunder-/topplag.
* Du kan aktivera dina användare automatiskt inloggade på Min Award Points Top Sub/Top Team (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med My Award Points Top Sub/Top Team behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Min Award Points Top Sub / Top Team enda inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Min Award Points Top Sub / Top Team stöder **SP** initierade SSO

## <a name="adding-my-award-points-top-subtop-team-from-the-gallery"></a>Lägga till mina bonuspoäng ToppUnder / Top Team från galleriet

Om du vill konfigurera integreringen av Mitt topplag för högsta tilldelar i Azure AD måste du lägga till Mitt topplag för bonuspoäng från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Top Sub/Top Team för Mina Bonuspoäng från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Min Award Points Top Sub/Top Team**, väljer Min Award Points Top **Sub/Top Team** från resultatpanelen och klickar sedan på **Lägg** till knappen för att lägga till programmet.

     ![Mina award points toppunder-/topplag i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med My Award Points Top Sub/Top Team baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Min Award Points Top Sub/Top Team upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med My Award Points Top Sub/Top Team måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **Konfigurera mina bonuspoäng Top Sub/Top Team Single Sign-On** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **Skapa mina bonuspoäng Toppunder-/toppteam-testanvändare** – om du vill ha en motsvarighet till Britta Simon i Mitt prispoäng Toppunder-/toppteam som är länkat till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enda Azure AD-inloggning med Mitt bonuspoäng toppunder-/toppteam:

1. I [Azure-portalen](https://portal.azure.com/)väljer du **Enkel inloggning**på sidan **Mina bonuspoäng top under-/topplag-programintegration** .

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Min Award Points Top Sub / Top Team Domän och webbadresser enkel inloggningsinformation](common/sp-signonurl.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://microsoftrr.performnet.com/biwv1auth/Shibboleth.sso/Login?providerId=<Azure AD Identifier>`

    > [!NOTE]
    > Värdet är inte verkligt. Du får `<Azure AD Identifier>` värdet i de senare stegen i den här självstudien.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet Konfigurera mina bonuspoäng överst **i under-/topplag.** 

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

    >[!NOTE]
    >Lägg till det kopierade Azure AD-identifierare värdet med `<Azure AD Identifier>` inloggnings-URL:en i stället för i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

### <a name="configure-my-award-points-top-subtop-team-single-sign-on"></a>Konfigurera mina bonuspoäng Toppunder/toppteam enkel inloggning

Om du vill konfigurera enkel inloggning på **min tilldelningspoäng toppunder-/topplag-sida** måste du skicka den nedladdade **XML-koden för federationsmetadata** och lämpliga kopierade url:er från Azure-portalen till Supportteamet för [Mitt bonuspoäng top under-/toppteam](mailto:myawardpoints@biworldwide.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** skriver **du\@brittasimon yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att ge åtkomst till Min Award Points Top Sub/Top Team.

1. I Azure-portalen väljer du **Företagsprogram**, väljer **Alla program**och väljer sedan Min Award Points **Top Sub/Top Team**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Mitt prispoäng Toppunder-/topplag**.

    ![Länken Mina bonuspoäng Toppunder-/topplag i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-my-award-points-top-subtop-team-test-user"></a>Skapa mina bonuspoäng Toppunder/Toppteam testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Mitt prispoäng Top Sub/Top Team. Arbeta med [supportteamet för Mitt Bonuspoäng Top Sub/Top Team](mailto:myawardpoints@biworldwide.com) för att lägga till användarna i plattformen My Award Points Top Sub/Top Team. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Mina bonuspoängs toppunder-/topplag på åtkomstpanelen ska du automatiskt loggas in på det topp-/topplag för mina bonuspoäng som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
