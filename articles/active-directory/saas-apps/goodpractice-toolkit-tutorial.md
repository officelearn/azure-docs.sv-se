---
title: 'Självstudier: Azure Active Directory-integrering med GoodPractice Toolkit | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GoodPractice Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 65b2979d-9e2f-4530-bc08-546975269ebc
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 538392c1812cfe6d703249fbe1802fd38b552bc2
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174171"
---
# <a name="tutorial-azure-active-directory-integration-with-goodpractice-toolkit"></a>Självstudier: Azure Active Directory-integrering med GoodPractice Toolkit

I den här självstudien får du lära dig hur du integrerar GoodPractice Toolkit med Azure Active Directory (Azure AD).
Integrera GoodPractice Toolkit med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till GoodPractice Toolkit.
* Du kan göra så att dina användare automatiskt loggas in på GoodPractice Toolkit (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med GoodPractice Toolkit behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* GoodPractice Toolkit-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* GoodPractice Toolkit stöder **SP**-initierad enkel inloggning
* GoodPractice Toolkit stöder **just-in-time**-användaretablering

## <a name="adding-goodpractice-toolkit-from-the-gallery"></a>Lägga till GoodPractice Toolkit från galleriet

Om du vill konfigurera integrering av GoodPractice Toolkit i Azure AD måste du lägga till GoodPractice Toolkit från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till GoodPractice Toolkit från galleriet måste du göra följande:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **GoodPractice Toolkit** i sökrutan, välj **GoodPractice Toolkit** på resultatpanelen och lägg sedan till programmet genom att klicka på knappen **Lägg till**.

     ![GoodPractice Toolkit i resultatlistan](common/search-new-app.png)
## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med GoodPractice Toolkit baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i GoodPractice Toolkit upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med GoodPractice Toolkit måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för GoodPractice Toolkit](#configure-goodpractice-toolkit-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Skapa GoodPractice Toolkit testanvändare](#create-goodpractice-toolkit-test-user)**  – för att få en motsvarighet till Britta Simon i GoodPractice Toolkit som är länkad till användarens Azure AD-representation.
5. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera enkel inloggning i Azure AD med GoodPractice Toolkit gör du följande:

1. Välj **Enkel inloggning** på sidan för **GoodPractice Toolkit**-programintegrering [ i Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om GoodPractice Toolkit-domänen och URL:er med enkel inloggning](common/sp-intiated.png)

    I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId> `.

    > [!Note]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Hämta värdet genom att kontakta [supportteamet för GoodPractice Toolkit-klienten](mailto:support@goodpractice.com).

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga URL:er enligt dina behov i avsnittet **Konfigurera GoodPractice Toolkit**.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-goodpractice-toolkit-single-sign-on"></a>Konfigurera enkel inloggning för GoodPractice Toolkit

Om du vill konfigurera enkel inloggning på **GoodPractice Toolkit**-sidan måste du skicka den hämtade **Federation Metadata-XML:en** och lämpliga kopierade URL:er från Azure Portal till [GoodPractice Toolkit-supportteamet](mailto:support@goodpractice.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning i Azure genom att ge åtkomst till GoodPractice Toolkit.

1. Välj **Företagsprogram**, sedan **Alla program** och därefter **GoodPractice Toolkit** i Azure Portal.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **GoodPractice Toolkit** i programlistan.

    ![GoodPractice Toolkit-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-goodpractice-toolkit-test-user"></a>Skapa testanvändare för GoodPractice Toolkit

I det här avsnittet skapas en användare som heter Britta Simon i GoodPractice Toolkit. GoodPractice Toolkit stöder **just-in-time-etablering**, vilken är aktiverad som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i GoodPractice Toolkit skapas en ny när du försöker få åtkomst till GoodPractice Toolkit.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på GoodPractice Toolkit i åtkomstpanelen bör du bli automatiskt inloggad på det GoodPractice Toolkit för vilket du konfigurerade enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

