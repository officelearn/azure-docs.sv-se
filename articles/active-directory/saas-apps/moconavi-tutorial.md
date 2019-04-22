---
title: 'Självstudier: Azure Active Directory-integrering med moconavi | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och moconavi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e1916224-e1c2-426f-b233-0a2518fa41db
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 2a96364d486ed6c0db2762256798ac56afa69a78
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268465"
---
# <a name="tutorial-azure-active-directory-integration-with-moconavi"></a>Självstudier: Azure Active Directory-integrering med moconavi

I den här självstudien får du lära dig hur du integrerar moconavi med Azure Active Directory (AD Azure).
Integrera moconavi med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till moconavi.
* Du kan aktivera användarna att vara automatiskt inloggad till moconavi (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med moconavi, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* moconavi enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* har stöd för moconavi **SP** -initierad SSO

## <a name="adding-moconavi-from-the-gallery"></a>Att lägga till moconavi från galleriet

För att konfigurera integrering av moconavi i Azure AD, som du behöver lägga till moconavi från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till moconavi från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **moconavi**väljer **moconavi** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![moconavi i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med moconavi baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i moconavi upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med moconavi, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera moconavi enkel inloggning](#configure-moconavi-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare moconavi](#create-moconavi-test-user)**  – du har en motsvarighet för Britta Simon i moconavi som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med moconavi:

1. I den [Azure-portalen](https://portal.azure.com/)på den **moconavi** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![moconavi domän och URL: er enkel inloggning för information](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<yourserverurl>/moconavi-saml2/saml/login`

    b. I rutan **Identifierare** skriver du en URL med följande mönster: `https://<yourserverurl>/moconavi-saml2`

    c. I textrutan **svars-URL** skriver du en URL med följande mönster: `https://<yourserverurl>/moconavi-saml2/saml/SSO`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [moconavi klienten supportteamet](mailto:support@recomot.co.jp) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera moconavi** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-moconavi-single-sign-on"></a>Konfigurera moconavi enkel inloggning

Att konfigurera enkel inloggning på **moconavi** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** och lämpliga kopierade URL: er från Azure portal för att [moconavi supportteamet](mailto:support@recomot.co.jp). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till moconavi.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **moconavi**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **moconavi**.

    ![Länken moconavi i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-moconavi-test-user"></a>Skapa moconavi testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i moconavi. Arbeta med [moconavi supportteamet](mailto:support@recomot.co.jp) att lägga till användare i moconavi-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

1. Installera moconavi från Microsoft store.

2. Starta moconavi.

3. Klicka på **ansluta inställningen** knappen.

    ![Testa enkel inloggning](./media/moconavi-tutorial/testing1.png)

4. Ange `https://mcs-admin.moconavi.biz/gateway` till **Anslut till URL: en** textrutan och klicka sedan på **klar** knappen.

    ![Testa enkel inloggning](./media/moconavi-tutorial/testing2.png)

5. Utför följande steg på följande skärmbild:

    ![Testa enkel inloggning](./media/moconavi-tutorial/testing3.png)

    a. Ange **indata autentiseringsnyckeln**:`azureAD` till **indata autentiseringsnyckeln** textrutan.

    b. Ange **indata användar-ID**: `your ad account` till **indata användar-ID** textrutan.

    c. Klicka på **inloggning**.

6. Ange din Azure AD-lösenord till **lösenord** textrutan och klicka sedan på **inloggning** knappen.

    ![Testa enkel inloggning](./media/moconavi-tutorial/testing4.png)

7. Azure AD-autentisering har lyckats när menyn visas.

    ![Testa enkel inloggning](./media/moconavi-tutorial/testing5.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

