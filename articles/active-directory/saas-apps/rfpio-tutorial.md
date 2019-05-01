---
title: 'Självstudier: Azure Active Directory-integrering med RFPIO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 176e0c80b4b1c70c3f49a35ae04d6233bc080f43
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687820"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Självstudier: Azure Active Directory-integrering med RFPIO

I den här självstudien får du lära dig hur du integrerar RFPIO med Azure Active Directory (AD Azure).
Integrera RFPIO med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till RFPIO.
* Du kan aktivera användarna att vara automatiskt inloggad till RFPIO (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med RFPIO, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* RFPIO enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för RFPIO **SP och IDP** -initierad SSO

## <a name="adding-rfpio-from-the-gallery"></a>Att lägga till RFPIO från galleriet

För att konfigurera integrering av RFPIO i Azure AD, som du behöver lägga till RFPIO från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till RFPIO från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **RFPIO**väljer **RFPIO** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![RFPIO i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med RFPIO baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i RFPIO upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med RFPIO, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera RFPIO Single Sign-On](#configure-rfpio-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare RFPIO](#create-rfpio-test-user)**  – du har en motsvarighet för Britta Simon i RFPIO som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med RFPIO:

1. I den [Azure-portalen](https://portal.azure.com/)på den **RFPIO** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På den **SAML grundkonfiguration** om du vill konfigurera programmet i **IDP** initierade läge, utföra följande steg:

    ![RFPIO domän och URL: er med enkel inloggning för information](common/idp-identifier.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://www.rfpio.com`

    b. Klicka på **Ange ytterligare URL:er**.

    c. I den **Vidarebefordransstatus** textrutan anger du ett strängvärde. Kontakta [RFPIO supportteam](https://www.rfpio.com/contact/) att hämta det här värdet.

    ![RFPIO domän och URL: er med enkel inloggning för information](common/idp-preintegrated-relay.png)

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![image](common/both-preintegrated-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://www.app.rfpio.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska identifierare och inloggnings-URL: en. Kontakta [RFPIO klienten supportteamet](https://www.rfpio.com/contact/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. På den **konfigurera RFPIO** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-rfpio-single-sign-on"></a>Konfigurera RFPIO enkel inloggning

1. I ett annat webbläsarfönster, loggar du in den **RFPIO** webbplats som administratör.

1. Klicka på listrutan längst ned till vänster.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app1.png)

1. Klicka på den **organisationsinställningar**. 

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app2.png)

1. Klicka på den **funktioner och integrering**.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app4.png)

1. I den **SAML SSO-konfiguration** klickar du på **redigera**.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app3.png)

1. I det här avsnittet utför du följande åtgärder:

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app5.png)
    
    a. Kopiera innehållet i den **XML-Metadata för hämtade** och klistra in den i den **identitet configuration** fält.

    > [!NOTE]
    >Om du vill kopiera innehållet i nedladdade **XML-Metadata för Federation** Använd **anteckningar ++** eller till rätt **XML-redigerare**.

    b. Klicka på **Validera**.

    c. När du klickar på **Validera**, vänd **SAML(Enabled)** till på.

    d. Klicka på **Skicka**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till RFPIO.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **RFPIO**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **RFPIO**.

    ![Länken RFPIO i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-rfpio-test-user"></a>Skapa RFPIO testanvändare

1. Logga in på webbplatsen RFPIO företag som administratör.

1. Klicka på listrutan längst ned till vänster.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app1.png)

1. Klicka på den **organisationsinställningar**. 

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app2.png)

1. Klicka på **TEAMMEDLEMMAR**.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app6.png)

1. Klicka på **Lägg till MEDLEMMAR**.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app7.png)

1. I den **lägga till nya medlemmar** avsnittet. Utför följande åtgärder:

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app8.png)

    a. Ange **e-postadress** i den **ange en e-postadress per rad** fält.

    b. Välj **rollen** enligt dina krav.

    c. Klicka på **Lägg till MEDLEMMAR**.

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen RFPIO i åtkomstpanelen, bör det vara loggas in automatiskt till RFPIO som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

