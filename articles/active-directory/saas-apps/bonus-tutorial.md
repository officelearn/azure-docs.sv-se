---
title: 'Självstudiekurs: Azure Active Directory-integrering med Bonusly | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea9c88f8eb8ac7b72f11ff286d2294df8cb70860
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232063"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Självstudiekurs: Azure Active Directory-integrering med Bonusly

I den här självstudien får du lära dig hur du integrerar Bonusly med Azure Active Directory (Azure AD).
Genom att integrera Bonusly med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Bonusly.
* Du kan aktivera dina användare automatiskt inloggad på Bonusly (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Bonusly behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Bonusly enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Bonusstöden **IDP** initierade SSO

## <a name="adding-bonusly-from-the-gallery"></a>Lägga till Bonusly från galleriet

Om du vill konfigurera integreringen av Bonusly i Azure AD måste du lägga till Bonusly från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Bonusly från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Bonus**i sökrutan och välj **Bonus från** resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![Bonusly i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Bonusly baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Bonusly upprättas.

Om du vill konfigurera och testa azure AD-enkel inloggning med Bonusly måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Bonusly Single Sign-On](#configure-bonusly-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Bonusly testanvändare](#create-bonusly-test-user)** - att ha en motsvarighet till Britta Simon i Bonusly som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Bonusly:

1. Välj [Azure portal](https://portal.azure.com/)Enkel inloggning på sidan Bonusly-programintegration på Sidan **Bonusly-programintegration**. **Bonusly**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Bonusdomän och webbadresser enkel inloggningsinformation](common/idp-reply.png)

    Skriv en URL med följande mönster i textrutan **Svara URL:**`https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [Bonusly Client support team](https://bonus.ly/contact) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. Kopiera **Tumavtryck** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Bonusly** enligt dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-bonusly-single-sign-on"></a>Konfigurera bonusly enkel inloggning

1. Logga in på din **Bonusly-klient** i ett annat webbläsarfönster.

1. Klicka på **Inställningar** i verktygsfältet högst upp och välj sedan **Integrationer och appar**.

    ![Bonusly social sektion](./media/bonus-tutorial/ic773686.png "Bonusly")
1. Under **Enkel inloggning**väljer du **SAML**.

1. Gör följande på dialogsidan för **SAML:**

    ![Dialogsida för Bonusly Saml](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. Klistra in värdet **för inloggnings-URL**i **textrutan för IdP SSO-mål-URL** , som du har kopierat från Azure-portalen.

    b. Klistra in värdet **för inloggnings-URL**i textrutan **för IdP-inloggningsadress** , som du har kopierat från Azure-portalen.

    c. Klistra in värdet för **Azure AD-identifierare**i **textrutan för IdP-utfärdare** , som du har kopierat från Azure-portalen.
    
    d. Klistra in **tumavtrycksvärdet** som kopierats från Azure-portalen i textrutan **Cert Fingerprint.**
    
    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Bonusly.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan Bonus **.**

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Bonus**i programlistan .

    ![Länken Bonusly i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-bonusly-test-user"></a>Skapa bonustestanvändare

För att Azure AD-användare ska kunna logga in på Bonusly måste de etableras i Bonusly. När det gäller Bonusly är etablering en manuell uppgift.

> [!NOTE]
> Du kan använda andra verktyg för att skapa bonuskonton eller API:er som tillhandahålls av Bonusly för att etablera Azure AD-användarkonton. 

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din Bonusly-klient i ett webbläsarfönster.

1. Klicka på **Inställningar**.

    ![Inställningar](./media/bonus-tutorial/ic781041.png "Inställningar")

1. Klicka på fliken **Användare och bonusar.**

    ![Användare och bonusar](./media/bonus-tutorial/ic781042.png "Användare och bonusar")

1. Klicka på **Hantera användare**.

    ![Hantera användare](./media/bonus-tutorial/ic781043.png "Hantera användare")

1. Klicka på **Lägg till användare**.

    ![Lägg till användare](./media/bonus-tutorial/ic781044.png "Lägg till användare")

1. I dialogrutan **Lägg till användare** utför du följande steg:

    ![Lägg till användare](./media/bonus-tutorial/ic781045.png "Lägg till användare")  

    a. I textrutan **Förnamn** anger du förnamnet på en användare som **Britta**.

    b. I textrutan **Efternamn** skriver du efternamnet på användaren: **Simon**.

    c. I textrutan **E-post** anger du e-postadressen för användaren, t.ex. `brittasimon\@contoso.com`.

    d. Klicka på **Spara**.

    > [!NOTE]
    > Azure AD-kontoinnehavaren får ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det blir aktivt.  

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Bonusly på åtkomstpanelen ska du automatiskt loggas in på den Bonusly som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
