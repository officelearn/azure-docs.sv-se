---
title: 'Självstudiekurs: Azure Active Directory-integrering med Vidyard | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: a55ec7afc94440ea8b6a48ed1507476d362df6c0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67087428"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Självstudiekurs: Azure Active Directory-integrering med Vidyard

I den här självstudien får du lära dig hur du integrerar Vidyard med Azure Active Directory (Azure AD).
Genomyard med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Vidyard.
* Du kan aktivera dina användare så att de automatiskt loggas in på Vidyard (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Vidyard behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Vidyard enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Vidyard stöder **SP** och **IDP** initierade SSO

* Vidyard stöder just in time-användaresetablering **Just In Time**

## <a name="adding-vidyard-from-the-gallery"></a>Lägga till Vidyard från galleriet

Om du vill konfigurera integreringen av Vidyard i Azure AD måste du lägga till Vidyard från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Vidyard från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Vidyard**i sökrutan och välj **Vidyard** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![Vidyard i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Vidyard baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Vidyard upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Vidyard måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Vidyard Single Sign-On](#configure-vidyard-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Vidyard testanvändare](#create-vidyard-test-user)** - att ha en motsvarighet till Britta Simon i Vidyard som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Vidyard:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Vidyard-programintegration**. **Vidyard**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![Vidyard Domain och webbadresser enkel inloggningsinformation](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Vidyard Domain och webbadresser enkel inloggningsinformation](common/metadata-upload-additional-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du kommer att uppdatera dessa värden med den faktiska identifieraren, svars-URL:en och inloggnings-URL: en, vilket förklaras senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Vidyard.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-vidyard-single-sign-on"></a>Konfigurera Vidyard Enkel inloggning

1. I ett annat webbläsarfönster loggar du in på din Webbplats för Vidyard Software som administratör.

2. På Vidyard-instrumentpanelen väljer du > **Gruppsäkerhet** **Group**

    ![Vidyard Konfiguration](./media/vidyard-tutorial/configure1.png)

3. Klicka på fliken **Ny profil.**

    ![Vidyard Konfiguration](./media/vidyard-tutorial/configure2.png)

4. Gör följande i avsnittet **SAML-konfiguration:**

    ![Vidyard Konfiguration](./media/vidyard-tutorial/configure3.png)

    a. Ange allmänt profilnamn i textrutan **Profilnamn.**

    b. Kopiera **värdet på SSO-användarlogin och** klistra in det i textrutan Logga in på **URL** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    c. Kopiera **ACS-URL-värdet** och klistra in det i textrutan **Svara url** i avsnittet Grundläggande **SAML-konfiguration** på Azure-portalen.

    d. **Url-värdet kopiera utfärdare/metadata** och klistra in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    e. Öppna den nedladdade certifikatfilen från Azure-portalen i Anteckningar och klistra sedan in den i textrutan **X.509 Certificate.**

    f. Klistra in värdet för **inloggnings-URL** som kopierats från Azure-portalen i SMS-textrutan FÖR **SIML-slutpunkt.**

    g. Klicka på **Bekräfta**.

5. Välj **Tilldela bredvid** en befintlig profil på fliken Enkel inloggning

    ![Vidyard Konfiguration](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > När du har skapat en SSO-profil tilldelar du den till alla grupper som användarna behöver åtkomst via Azure. Om användaren inte finns i den grupp som de tilldelades, skapar Vidyard automatiskt ett användarkonto och tilldelar sin roll i realtid.

6. Välj din organisationsgrupp, som visas i **grupperna Som är tillgängliga att tilldela**.

    ![Vidyard Konfiguration](./media/vidyard-tutorial/configure5.png)

7. Du kan se de tilldelade grupperna under grupperna **Som för närvarande har tilldelats**. Välj en roll för gruppen enligt din organisation och klicka på **Bekräfta**.

    ![Vidyard Konfiguration](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Mer information finns [i det här dokumentet](https://knowledge.vidyard.com/hc/articles/360009990033-SAML-based-Single-Sign-On-SSO-in-Vidyard).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure single sign-on genom att bevilja åtkomst till Vidyard.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Vidyard**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Vidyard**.

    ![Länken Vidyard i listan Applikationer](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-vidyard-test-user"></a>Skapa Avvaryard testanvändare

I det här avsnittet skapas en användare som heter Britta Simon på Vidyard. Vidyard stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Vidyard skapas en ny efter autentisering.

>[!Note]
>Om du behöver skapa en användare manuellt kontaktar du [Vidyard supportteam](mailto:support@vidyard.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Vidyard-panelen i åtkomstpanelen ska du automatiskt loggas in på den Vidyard som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

