---
title: 'Självstudiekurs: Azure Active Directory-integrering med RFPIO | Microsoft-dokument'
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
ms.openlocfilehash: 63d7b6af8ff76c890b98c29ded0e8bdc637b45dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092852"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Självstudiekurs: Azure Active Directory-integrering med RFPIO

I den här självstudien får du lära dig hur du integrerar RFPIO med Azure Active Directory (Azure AD).
Genom att integrera RFPIO med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till RFPIO.
* Du kan aktivera dina användare så att de automatiskt loggas in på RFPIO (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med RFPIO behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* RFPIO enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* RFPIO stöder **SP och IDP** initierad SSO

## <a name="adding-rfpio-from-the-gallery"></a>Lägga till RFPIO från galleriet

Om du vill konfigurera integreringen av RFPIO i Azure AD måste du lägga till RFPIO från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till RFPIO från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **RFPIO**i sökrutan och välj **RFPIO** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![RFPIO i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med RFPIO baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i RFPIO upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med RFPIO måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera RFPIO Single Sign-On](#configure-rfpio-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa RFPIO-testanvändare](#create-rfpio-test-user)** – om du vill ha en motsvarighet till Britta Simon i RFPIO som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du enkel Azure AD-inloggning med RFPIO:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **RFPIO-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande steg:

    ![RFPIO-domän och url:er enkel inloggningsinformation](common/idp-identifier.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://www.rfpio.com`

    b. Klicka på **Ange ytterligare URL:er**.

    c. I textrutan **Relay State** anger du ett strängvärde. Kontakta [RFPIO:s supportteam](https://www.rfpio.com/contact/) för att få det här värdet.

    ![RFPIO-domän och url:er enkel inloggningsinformation](common/idp-preintegrated-relay.png)

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![image](common/both-preintegrated-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://www.app.rfpio.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och inloggnings-URL:en. Kontakta [RFPIO Client support team](https://www.rfpio.com/contact/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera RFPIO.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-rfpio-single-sign-on"></a>Konfigurera enstaka RFPIO-inloggning

1. Logga in på **RFPIO-webbplatsen** som administratör i ett annat webbläsarfönster.

1. Klicka på det nedre vänstra hörnet rullgardinsmenyn.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app1.png)

1. Klicka på **organisationsinställningarna**. 

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app2.png)

1. Klicka på **funktioner & integration**.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app4.png)

1. Klicka på **Redigera**i **SAML SSO-konfigurationen** .

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app3.png)

1. I det här avsnittet utför du följande åtgärder:

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app5.png)
    
    a. Kopiera innehållet i **XML-koden för hämtade metadata** och klistra in det i **konfigurationsfältet** för identitet.

    > [!NOTE]
    >Om du vill kopiera innehållet i nedladdad **XML för federationsmetadata** använder **du Anteckningar++** eller korrekt **XML-redigerare**.

    b. Klicka på **Validate** (Validera).

    c. När du har klickat på **Validera**, Vänd **SAML (Aktiverad)** till på.

    d. Klicka på **Skicka**.

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

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till RFPIO.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **RFPIO**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **RFPIO**i programlistan .

    ![RFPIO-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-rfpio-test-user"></a>Skapa rfpio-testanvändare

1. Logga in på rfpio-företagets webbplats som administratör.

1. Klicka på det nedre vänstra hörnet rullgardinsmenyn.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app1.png)

1. Klicka på **organisationsinställningarna**. 

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app2.png)

1. Klicka på **GRUPPMEDLEMMAR**.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app6.png)

1. Klicka på **ADD MEMBERS**.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app7.png)

1. I avsnittet **Lägg till nya medlemmar.** Utför följande åtgärder:

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app8.png)

    a. Ange **E-postadress** i fältet **Ange ett e-postmeddelande per rad.**

    b. Välj **Roll** enligt dina krav.

    c. Klicka på **LÄGG TILL MEDLEMMAR**.

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på RFPIO-panelen på åtkomstpanelen ska du automatiskt loggas in på den RFPIO som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

