---
title: 'Självstudiekurs: Azure Active Directory-integrering med SciQuest Spend Director | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SciQuest Spend Director.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9fab641b-292e-4bef-91d1-8ccc4f3a0c1f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 199f531485a6a6c9ea234b35a7dbb88b66b3067c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091646"
---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>Självstudiekurs: Azure Active Directory-integrering med SciQuest Spend Director

I den här självstudien får du lära dig hur du integrerar SciQuest Spend Director med Azure Active Directory (Azure AD).
Genom att integrera SciQuest Spend Director med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SciQuest Spend Director.
* Du kan aktivera dina användare automatiskt inloggad på SciQuest Spend Director (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med SciQuest Spend Director behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SciQuest Spend Director enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SciQuest Spend Director stöder **SP** initierade SSO
* SciQuest Spend Director stöder **Just In Time** användaretablering

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>Lägga SciQuest Spend Director från galleriet

Om du vill konfigurera integreringen av SciQuest Spend Director i Azure AD måste du lägga till SciQuest Spend Director från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till SciQuest Spend Director från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **SciQuest Spend Director**, välj **SciQuest Spend Director** från resultatpanelen och klicka sedan på **Lägg** till knappen för att lägga till programmet.

     ![SciQuest Spend Director i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med SciQuest Spend Director baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SciQuest Spend Director upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med SciQuest Spend Director måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SciQuest Spend Director Single Sign-On](#configure-sciquest-spend-director-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SciQuest Spend Director testanvändare](#create-sciquest-spend-director-test-user)** - att ha en motsvarighet till Britta Simon i SciQuest Spend Director som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med SciQuest Spend Director:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **SciQuest Spend** **Director-programintegration**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![SciQuest Spend Director Domän och webbadresser enkel inloggningsinformation](common/sp-identifier-reply.png)

    a. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<companyname>.sciquest.com/apps/Router/SAMLAuth/<instancename>`

    b. Skriv en URL med följande mönster i rutan **Identifierare:**`https://<companyname>.sciquest.com`

    c. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<companyname>.sciquest.com/apps/Router/ExternalAuth/Login/<instancename>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [SciQuest Spend Director Client support team](https://www.jaggaer.com/contact-us/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera SciQuest Spend Director.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sciquest-spend-director-single-sign-on"></a>Konfigurera SciQuest Spend Director Enkel inloggning

Om du vill konfigurera enkel inloggning på **SciQuest Spend Director-sidan** måste du skicka den nedladdade **XML-koden för federationsmetadata** och lämpliga kopierade url:er från Azure-portalen till [SciQuest Spend Director supportteam](https://www.jaggaer.com/contact-us/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure single sign-on genom att bevilja åtkomst till SciQuest Spend Director.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **SciQuest Spend Director**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **SciQuest Spend Director**.

    ![Länken SciQuest Spend Director i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sciquest-spend-director-test-user"></a>Skapa Testanvändare för SciQuest Spend Director

Syftet med detta avsnitt är att skapa en användare som heter Britta Simon i SciQuest Spend Director.

Du måste kontakta din [SciQuest Spend Director supportteam](https://www.jaggaer.com/contact-us/) och förse dem med information om ditt testkonto för att få det skapat.

Alternativt kan du också utnyttja just-in-time etablering, en enda inloggningsfunktion som stöds av SciQuest Spend Director.  
Om just-in-time-etablering är aktiverad skapas användare automatiskt av SciQuest Spend Director under ett enda inloggningsförsök om de inte finns. Den här funktionen eliminerar behovet av att manuellt skapa enskilda inloggningsmotsvarighetsanvändare.

För att få just-in-time etablering aktiverat, måste du kontakta din [SciQuest Spend Director supportteam.](https://www.jaggaer.com/contact-us/)

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SciQuest Spend Director i åtkomstpanelen ska du automatiskt loggas in på den SciQuest Spend Director som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)