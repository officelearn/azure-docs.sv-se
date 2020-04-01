---
title: 'Självstudiekurs: Azure Active Directory-integrering med Kantega SSO för sammanflödet | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kantega SSO för sammanflödet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d0d99c14-a6ca-45f2-bb84-633126095e7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 27fa0567eefbb50907c0ed6952333230e874c21d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099044"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Självstudiekurs: Azure Active Directory-integrering med Kantega SSO för sammanflödet

I den här självstudien får du lära dig hur du integrerar Kantega SSO för sammanflödet med Azure Active Directory (Azure AD).
Genom att integrera Kantega SSO for Confluence med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Kantega SSO för sammanflödet.
* Du kan aktivera dina användare så att de automatiskt loggas in på Kantega SSO for Confluence (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Kantega SSO för sammanflödet behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Kantega SSO för sammanflödet enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Kantega SSO for Confluence stöder **SP och IDP** initierade SSO

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Lägga kantega SSO för sammanflödet från galleriet

Om du vill konfigurera integreringen av Kantega SSO for Confluence i Azure AD måste du lägga till Kantega SSO för sammanflödet från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Kantega SSO for Confluence från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Kantega SSO för sammanflödet**i sökrutan och välj **Kantega SSO för sammanflödet** från resultatpanelen och klicka sedan på **Lägg** till knappen för att lägga till programmet.

    ![Kantega SSO för sammanflödet i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Kantega SSO for Confluence baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Kantega SSO for Confluence upprättas.

Om du vill konfigurera och testa en azure AD-inloggning med Kantega SSO for Confluence måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Kantega SSO för sammanflödet enkel inloggning](#configure-kantega-sso-for-confluence-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Kantega SSO för sammanflödet testanvändare](#create-kantega-sso-for-confluence-test-user)** - att ha en motsvarighet till Britta Simon i Kantega SSO för sammanflödet som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Kantega SSO för sammanflödet:

1. I [Azure-portalen](https://portal.azure.com/)väljer du **Enkel inloggning**på sidan **Kantega SSO för sammanflödet** av programintegration .

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![Kantega SSO för sammanflödet domän och webbadresser enkel inloggningsinformation](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Kantega SSO för sammanflödet domän och webbadresser enkel inloggningsinformation](common/metadata-upload-additional-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Dessa värden tas emot under konfigurationen av Confluence-pluginprogrammet, som beskrivs senare i självstudien.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Kantega SSO för sammanflödet.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-kantega-sso-for-confluence-single-sign-on"></a>Konfigurera Kantega SSO för sammanflödet enkel inloggning

1. I ett annat webbläsarfönster loggar du in på **administratörsportalen för Sammanflödet** som administratör.

1. Hovra över kugghjulet och klicka på **Tillägg**.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon1.png)

1. Klicka på **Hitta nya tillägg** under fliken **ATLASSIAN MARKETPLACE**.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon.png)

1. Sök **Kantega SSO för sammanflödet SAML Kerberos** och klicka på **Installera** knappen för att installera den nya SAML plugin.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon2.png)

1. Plugin-installationen startar.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon3.png)

1. När installationen är klar. Klicka på **Stäng**.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon33.png)

1. Klicka på **Hantera**.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon34.png)

1. Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon35.png)

1. Det nya plugin-programmet visas även under fliken **Användare och säkerhet**.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon36.png)

1. I **SAML** SAML-sektionen. Välj **Azure Active Directory (Azure AD)** i listrutan Lägg till **identitetsprovider.**

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon4.png)

1. Välj prenumerationsnivå som **Grundläggande**.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon5.png)

1. Gör följande i avsnittet **Appegenskaper:**

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. Kopiera **app-ID-URI-värdet** och använd det som **identifierare, svars-URL och inloggnings-URL** på avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    b. Klicka på **Nästa**.

1. Gör följande i avsnittet **Importera metadata:** 

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Välj **Metadata-fil på min dator**och ladda upp metadatafilen som du har hämtat från Azure-portalen.

    b. Klicka på **Nästa**.

1. Gör följande i avsnittet **Namn och SSO-plats:**

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon8.png)

    a. Lägg till namnet på identitetsprovidern i **identitetsproviderns namntextruta** (t.ex.

    b. Klicka på **Nästa**.

1. Verifiera signeringscertifikatet och klicka på **Nästa**.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon9.png)

1. Gör följande i avsnittet **Sammanflödet av användarkonton:**

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. Välj **Skapa användare i Confluences interna katalog om det behövs** och ange lämpligt namn på gruppen för användare (kan vara flera nej. grupper åtskilda av kommatecken).

    b. Klicka på **Nästa**.

1. Klicka på **Slutför**.

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon11.png)

1. På avsnittet **Kända domäner för Azure AD** utför du följande steg: 

    ![Konfigurera enkel inloggning](./media/kantegassoforconfluence-tutorial/addon12.png)

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Kantega SSO för sammanflödet.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Kantega SSO för sammanflödet**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Kantega SSO för sammanflödet i**programlistan .

    ![Länken Kantega SSO for Confluence i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-kantega-sso-for-confluence-test-user"></a>Skapa Kantega SSO för sammanflödet testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Sammanflödet måste de etableras i Sammanflödet. När det gäller Kantega SSO för sammanflödet är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på webbplatsen Kantega SSO for Confluence som administratör.

1. Hovra över kugghjulet och klicka på **Användarhantering**.

    ![Lägga till medarbetare](./media/kantegassoforconfluence-tutorial/user1.png)

1. Klicka på Fliken Lägg till användare under avsnittet **Användare.** Gör följande på dialogrutan **Lägg till en användare:**

    ![Lägga till medarbetare](./media/kantegassoforconfluence-tutorial/user2.png)

    a. Skriv e-postadressen för användaren i textrutan **Användarnamn** som Brittasimon@contoso.com.

    b. I textrutan **Fullständigt namn** skriver du det fullständiga namnet för användaren: Britta Simon.

    c. I textrutan **E-post** skriver du e-postadressen för användaren: Brittasimon@contoso.com.

    d. Skriv lösenordet för användaren i textrutan **Lösenord.**

    e. Klicka på **Bekräfta lösenord** för att ange lösenordet igen.

    f. Klicka på knappen **Lägg till**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Kantega SSO for Confluence i åtkomstpanelen ska du automatiskt loggas in på Den Kantega SSO för sammanflödet som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

