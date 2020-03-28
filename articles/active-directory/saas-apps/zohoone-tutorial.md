---
title: 'Självstudiekurs: Azure Active Directory-integrering med Zoho One | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zoho One.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 0a37789e7c7efeb71770ff0e8061d57e6603b6c4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086236"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Självstudiekurs: Azure Active Directory-integrering med Zoho One

I den här självstudien får du lära dig hur du integrerar Zoho One med Azure Active Directory (Azure AD).
Genom att integrera Zoho One med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Zoho One.
* Du kan aktivera dina användare så att de automatiskt loggas in på Zoho One (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Zoho One behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Zoho One enda inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zoho One stöder **SP** och **IDP** initierade SSO

## <a name="adding-zoho-one-from-the-gallery"></a>Lägga till Zoho One från galleriet

Om du vill konfigurera integreringen av Zoho One i Azure AD måste du lägga till Zoho One från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Zoho One från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Zoho One**i sökrutan och välj **Zoho One** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Zoho One i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Zoho One baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Zoho One upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Zoho One måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Zoho One Single Sign-On](#configure-zoho-one-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Zoho One-testanvändare](#create-zoho-one-test-user)** - att ha en motsvarighet till Britta Simon i Zoho One som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Zoho One:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Zoho** **One-programintegration**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![Zoho One Domän och webbadresser enkel inloggningsinformation](common/idp-relay.png)

    a. Skriv en URL i textrutan **Identifierare:**`one.zoho.com`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Det föregående **svars-URL-värdet** är inte verkligt. Du kommer `<saml-identifier>` att få värdet från #step4 **av Konfigurera Zoho One Single Sign-On** avsnitt , som förklaras senare i handledningen.

    c. Klicka på **Ange ytterligare URL:er**.

    d. Skriv en URL i textrutan **Vidarebefordransstatus**: `https://one.zoho.com`

5. Om du vill konfigurera programmet i **återupptastartat SP-läge** utför du följande steg:


    ![Zoho One Domän och webbadresser enkel inloggningsinformation](common/both-signonurl.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Det föregående **url-värdet för inloggning** är inte verkligt. Du kommer att uppdatera värdet med den faktiska inloggnings-URL:en från avsnittet **Konfigurera Zoho One Single Sign-On,** som förklaras senare i självstudien. 

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Zoho One.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-zoho-one-single-sign-on"></a>Konfigurera Zoho One Single Sign-On

1. I ett annat webbläsarfönster loggar du in på din Zoho One-företagswebbplats som administratör.

2. Klicka på **Installationsprogrammet** under **SAML-autentisering**på fliken **Organisation** .

    ![Zoho En org](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. På popup-sidan utför du följande steg:

    ![Zoho En sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Klistra in värdet för inloggnings-URL **Login URL**i textrutan **För inloggnings-URL** , som du har kopierat från Azure-portalen.

    b. Klistra in värdet för url för **utloggning**i textrutan **För signerings-URL** , som du har kopierat från Azure-portalen.

    c. Klicka på **Bläddra** om du vill ladda upp **certifikatet (Base64)** som du har hämtat från Azure-portalen.

    d. Klicka på **Spara**.

4. När du har sparat konfigurationen för SAML-autentisering kopierar du **SAML-identifier-värdet** och lägger till det med **svars-URL:en** `<saml-identifier>`i stället för , `https://accounts.zoho.com/samlresponse/one.zoho.com` gilla och klistra in det genererade värdet i textrutan **Svars-URL** under Avsnittet Grundläggande **SAML-konfiguration.**

    ![Zoho En saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Gå till fliken **Domäner** och klicka sedan på **Lägg till domän**.

    ![Zoho One-domän](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Gör följande på sidan **Lägg till domän:**

    ![Zoho One lägga till domän](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Skriv domän som contoso.com i textrutan **Domännamn.**

    b. Klicka på **Lägg till**.

    >[!Note]
    >När du har lagt till domänen följer du [dessa](https://www.zoho.com/one/help/admin-guide/domain-verification.html) steg för att verifiera din domän. När domänen har verifierats använder du ditt domännamn i **inloggnings-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure single sign-on genom att bevilja åtkomst till Zoho One.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Zoho One**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Zoho One**i programlistan .

    ![Den Zoho One länk i ansökan listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-zoho-one-test-user"></a>Skapa Zoho One-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Zoho One måste de etableras i Zoho One. I Zoho One är etablering en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Zoho One som säkerhetsadministratör.

2. Klicka på **användarens logotyp**på fliken **Användare** .

    ![Zoho En användare](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Gör följande på sidan **Lägg till användare:**

    ![Zoho One lägga till användare](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. I textrutan **Namn** anger du namnet på användaren som **Britta simon**.
    
    b. I textrutan **E-postadress** anger du användarens e-postadress, som brittasimon@contoso.com.

    >[!Note]
    >Välj din verifierade domän i domänlistan.

    c. Klicka på **Lägg till**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Panelen Zoho One i åtkomstpanelen ska du automatiskt loggas in på den Zoho One som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

