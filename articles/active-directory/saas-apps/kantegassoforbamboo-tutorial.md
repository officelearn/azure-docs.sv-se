---
title: 'Självstudiekurs: Azure Active Directory-integrering med Kantega SSO för bambu | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kantega SSO för Bambu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e238b574-9e9b-43b7-ab98-d2a87ff89d48
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 8c951d7f5f1629447b1b5c1fc6e8a1c202246d74
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099112"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bamboo"></a>Självstudiekurs: Azure Active Directory-integrering med Kantega SSO för bambu

I den här självstudien får du lära dig hur du integrerar Kantega SSO för bambu med Azure Active Directory (Azure AD).
Genom att integrera Kantega SSO for Bamboo med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Kantega SSO för Bambu.
* Du kan aktivera dina användare så att de automatiskt loggas in på Kantega SSO for Bamboo (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Kantega SSO för Bambu behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Kantega SSO för enstaka sign-on-aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Kantega SSO for Bamboo stöder **SP och IDP** initierade SSO

## <a name="adding-kantega-sso-for-bamboo-from-the-gallery"></a>Lägga Kantega SSO för Bambu från galleriet

Om du vill konfigurera integreringen av Kantega SSO for Bamboo i Azure AD måste du lägga till Kantega SSO för Bambu från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Kantega SSO for Bamboo från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Kantega SSO för bambu**i sökrutan och välj **Kantega SSO för Bambu** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

    ![Kantega SSO för Bambu i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med Kantega SSO för bambu baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Kantega SSO for Bamboo upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Kantega SSO för Bambu måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Kantega SSO för enkel inloggning för bambu](#configure-kantega-sso-for-bamboo-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Kantega SSO för bamboo testanvändare](#create-kantega-sso-for-bamboo-test-user)** - att ha en motsvarighet till Britta Simon i Kantega SSO för bambu som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Kantega SSO för Bambu:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Kantega SSO for** **Bamboo-programintegration**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![Kantega SSO för bamboo domain och webbadresser enkel inloggningsinformation](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Kantega SSO för bamboo domain och webbadresser enkel inloggningsinformation](common/metadata-upload-additional-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Dessa värden tas emot under konfigurationen av Bamboo plugin som förklaras senare i handledningen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Kantega SSO för bambu.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-kantega-sso-for-bamboo-single-sign-on"></a>Konfigurera Kantega SSO för enkel inloggning för bambu

1. I ett annat webbläsarfönster loggar du in på din lokala bambooserver som administratör.

1. Hovra över kugghjulet och klicka på **Tillägg**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbamboo-tutorial/addon1.png)

1. I avsnittet Tillägg klickar du på **Hitta nya tillägg**. Sök **Kantega SSO for Bamboo (SAML & Kerberos)** och klicka på **Installera** knappen för att installera den nya SAML plugin.

    ![Konfigurera enkel inloggning](./media/kantegassoforbamboo-tutorial/addon2.png)

1. Installationen av plugin-programmet startar.

    ![Konfigurera enkel inloggning](./media/kantegassoforbamboo-tutorial/addon21.png)

1. När installationen är klar. Klicka på **Stäng**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbamboo-tutorial/addon33.png)

1. Klicka på **Hantera**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbamboo-tutorial/addon34.png)

1. Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/kantegassoforbamboo-tutorial/addon3.png)

1. I **SAML** SAML-sektionen. Välj **Azure Active Directory (Azure AD)** i listrutan Lägg till **identitetsprovider.**

    ![Konfigurera enkel inloggning](./media/kantegassoforbamboo-tutorial/addon4.png)

1. Välj prenumerationsnivå som **Grundläggande**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbamboo-tutorial/addon5.png)

1. Gör följande i avsnittet **Appegenskaper:**

    ![Konfigurera enkel inloggning](./media/kantegassoforbamboo-tutorial/addon6.png)

    a. Kopiera **app-ID-URI-värdet** och använd det som **identifierare, svars-URL och inloggnings-URL** på avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    b. Klicka på **Nästa**.

1. Gör följande i avsnittet **Importera metadata:**

    ![Konfigurera enkel inloggning](./media/kantegassoforbamboo-tutorial/addon7.png)

    a. Välj **Metadata-fil på min dator**och ladda upp metadatafilen som du har hämtat från Azure-portalen.

    b. Klicka på **Nästa**.

1. Gör följande i avsnittet **Namn och SSO-plats:**

    ![Konfigurera enkel inloggning](./media/kantegassoforbamboo-tutorial/addon8.png)

    a. Lägg till namnet på identitetsprovidern i **identitetsproviderns namntextruta** (t.ex.

    b. Klicka på **Nästa**.

1. Verifiera signeringscertifikatet och klicka på **Nästa**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbamboo-tutorial/addon9.png)

1. Gör följande i avsnittet **Bamboo-användarkonton:**

    ![Konfigurera enkel inloggning](./media/kantegassoforbamboo-tutorial/addon10.png)

    a. Välj **Skapa användare i Bamboos interna katalog om det behövs** och ange lämpligt namn på gruppen för användare (kan vara flera nej. grupper åtskilda av kommatecken).

    b. Klicka på **Nästa**.

1. Klicka på **Slutför**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbamboo-tutorial/addon11.png)

1. På avsnittet **Kända domäner för Azure AD** utför du följande steg:

    ![Konfigurera enkel inloggning](./media/kantegassoforbamboo-tutorial/addon12.png)

    a. Välj **Kända domäner** på sidans vänstra panel.

    b. Ange domännamn i textrutan **Kända domäner.**

    c. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Kantega SSO för Bambu.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Kantega SSO för Bambu**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Kantega SSO för bambu**i programlistan .

    ![Den Kantega SSO för Bambu länk i ansökan listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-kantega-sso-for-bamboo-test-user"></a>Skapa Kantega SSO för bamboo testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Bambu måste de etableras i Bambu. När det gäller Kantega SSO för Bambu är etablering en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din lokala bamboo-server som administratör.

1. Hovra över kugghjulet och klicka på **Användarhantering**.

    ![Lägga till medarbetare](./media/kantegassoforbamboo-tutorial/user1.png)

1. Klicka på **Användare**. Utför följande steg under avsnittet **Lägg till användare:**

    ![Lägga till medarbetare](./media/kantegassoforbamboo-tutorial/user2.png)

    a. Skriv e-postadressen för användaren i textrutan **Användarnamn** som Brittasimon@contoso.com.

    b. I textrutan **Password** (Lösenord) skriver du lösenordet för användaren.

    c. Ange lösenordet för användaren i textrutan **Bekräfta lösenord.**

    d. Skriv det fullständiga namnet för användaren, t.ex. Britta Simon, i textrutan **Fullständigt namn**.

    e. I textrutan **E-post** skriver du e-postadressen för användaren: Brittasimon@contoso.com.

    f. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Kantega SSO for Bamboo i åtkomstpanelen ska du automatiskt loggas in på Kantega SSO för bambu som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
