---
title: 'Självstudier: Azure Active Directory-integrering med SciQuest spendera Director | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SciQuest spendera Director.
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
ms.openlocfilehash: 371ea6609181b84ff8554b9e2f1a5047d2d48c48
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59258461"
---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>Självstudier: Azure Active Directory-integrering med SciQuest spendera Director

Lär dig hur du integrerar SciQuest spendera Director med Azure Active Directory (AD Azure) i den här självstudien.
Integrera SciQuest spendera Director med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SciQuest spendera Director.
* Du kan aktivera användarna att vara automatiskt inloggad till SciQuest spendera Director (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med SciQuest spendera Director, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SciQuest spendera Director enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för SciQuest spendera Director **SP** -initierad SSO
* Har stöd för SciQuest spendera Director **Just In Time** etableringen av användare

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>Att lägga till SciQuest spendera Director från galleriet

För att konfigurera integrering av SciQuest spendera Director i Azure AD, som du behöver lägga till SciQuest spendera Director från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SciQuest spendera Director från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **SciQuest spendera Director**väljer **SciQuest spendera Director** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![SciQuest spendera Director i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SciQuest spendera Director baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SciQuest spendera Director upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med SciQuest spendera Director, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SciQuest spendera Director enkel inloggning](#configure-sciquest-spend-director-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SciQuest spendera Director testanvändare](#create-sciquest-spend-director-test-user)**  – du har en motsvarighet för Britta Simon i SciQuest spendera Director som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera Azure AD enkel inloggning med SciQuest spendera Director, utför du följande steg:

1. I den [Azure-portalen](https://portal.azure.com/)på den **SciQuest spendera Director** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![SciQuest spendera Director domän och URL: er med enkel inloggning för information](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<companyname>.sciquest.com/apps/Router/SAMLAuth/<instancename>`

    b. I rutan **Identifierare** skriver du en URL med följande mönster: `https://<companyname>.sciquest.com`

    c. I textrutan **svars-URL** skriver du en URL med följande mönster: `https://<companyname>.sciquest.com/apps/Router/ExternalAuth/Login/<instancename>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [SciQuest spendera Director klienten supportteamet](https://www.jaggaer.com/contact-us/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera SciQuest spendera Director** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sciquest-spend-director-single-sign-on"></a>Konfigurera SciQuest spendera Director Single Sign-On

Att konfigurera enkel inloggning på **SciQuest spendera Director** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** och lämpliga kopierade URL: er från Azure portal för att [SciQuest utgifter Director supportteamet](https://www.jaggaer.com/contact-us/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SciQuest spendera Director.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **SciQuest spendera Director**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **SciQuest spendera Director**.

    ![Länken SciQuest spendera Director i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sciquest-spend-director-test-user"></a>Skapa SciQuest spendera Director testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i SciQuest spendera Director.

Du måste kontakta din [SciQuest spendera Director supportteamet](https://www.jaggaer.com/contact-us/) och ge dem med information om ditt testkonto för att hämta den skapade.

Alternativt kan du även använda just-in-time etablering, en enkel inloggnings-funktion som stöds av SciQuest spendera Director.  
Om just-in-time-etablering är aktiverat skapas användare automatiskt av SciQuest spendera Director under ett försök för enkel inloggning om de inte finns. Den här funktionen eliminerar behovet av att manuellt skapa motsvarighet för enkel inloggning för användare.

För att få just-in-time-etablering aktiverad kan du behöva kontakta din [SciQuest spendera Director supportteamet](https://www.jaggaer.com/contact-us/).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen SciQuest spendera Director på åtkomstpanelen, bör det vara loggas in automatiskt till den SciQuest spendera Director som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)