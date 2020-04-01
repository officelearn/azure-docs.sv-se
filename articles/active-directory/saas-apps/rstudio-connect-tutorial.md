---
title: 'Självstudiekurs: Azure Active Directory-integrering med RStudio Connect | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RStudio Connect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9bc78022-6d38-4476-8f03-e3ca2551e72e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9a9b49f75ad377a9377a2311ed16c17ca3d749e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092577"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect"></a>Självstudiekurs: Azure Active Directory-integrering med RStudio Connect

I den här självstudien får du lära dig hur du integrerar RStudio Connect med Azure Active Directory (Azure AD).
Genom att integrera RStudio Connect med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till RStudio Connect.
* Du kan aktivera dina användare så att de automatiskt loggas in på RStudio Connect (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med RStudio Connect behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* RStudio Connect. Det finns en [45 dagars gratis utvärdering.](https://www.rstudio.com/products/connect/)

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* RStudio Connect stöder **SP och IDP** initierad SSO

* RStudio Connect stöder just in time-användaretablering **Just In Time**

## <a name="adding-rstudio-connect-from-the-gallery"></a>Lägga till RStudio Connect från galleriet

Om du vill konfigurera integreringen av RStudio Connect i Azure AD måste du lägga till RStudio Connect från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till RStudio Connect från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **RStudio Connect**i sökrutan och välj **RStudio Connect** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![RStudio Connect i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med RStudio Connect baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i RStudio Connect upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med RStudio Connect måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera RStudio Connect Enkel inloggning](#configure-rstudio-connect-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa RStudio Connect-testanvändare](#create-rstudio-connect-test-user)** – om du vill ha en motsvarighet till Britta Simon i RStudio Connect som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du enkel Azure AD-inloggning med RStudio Connect:

1. Välj Enkel inloggning på sidan **RStudio** Connect-programintegrering på **Azure-portalen**. [Azure portal](https://portal.azure.com/)

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet Grundläggande `<example.com>` **SAML-konfiguration** utför du följande steg och ersätter med din RStudio Connect Server-adress och port:

    ![RStudio Connect-domän och webbadresser med enkel inloggning](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<example.com>/__login__/saml`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<example.com>/__login__/saml/acs`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![RStudio Connect-domän och webbadresser med enkel inloggning](common/metadata-upload-additional-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<example.com>/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. De bestäms från RStudio Connect`https://example.com` Server-adressen (i exemplen ovan). Kontakta [RStudio Connect-supportteamet](mailto:support@rstudio.com) om du har problem. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Ditt RStudio Connect-program förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. RStudio Connect-programmet förväntar sig att **namnidentifieraren** mappas med **user.mail**, så du måste redigera attributmappningen genom att klicka på **Redigera-ikonen** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

7. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-rstudio-connect-single-sign-on"></a>Konfigurera enkel inloggning för RStudio Connect

Om du vill konfigurera enkel inloggning för **RStudio Connect**måste du använda **url:en och** **serveradressen för** App Federation som används ovan. Detta görs i konfigurationsfilen för `/etc/rstudio-connect.rstudio-connect.gcfg`RStudio Connect på .

Det här är en exempelkonfigurationsfil:

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

Lagra **serveradressen** `Server.Address` i värdet och **url:en för appfederationens metadata** i `SAML.IdPMetaData` värdet.

Om du har problem med konfigurationen kan du läsa [RStudio Connect-administratörsguiden](https://docs.rstudio.com/connect/admin/authentication.html#authentication-saml) eller skicka e-post till [RStudio-supportteamet](mailto:support@rstudio.com) för hjälp.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till RStudio Connect.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **RStudio Connect**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **RStudio Connect**i programlistan .

    ![Länken RStudio Connect i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-rstudio-connect-test-user"></a>Skapa RStudio Connect-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i RStudio Connect. RStudio Connect stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i RStudio Connect skapas en ny när du försöker komma åt RStudio Connect.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen RStudio Connect på åtkomstpanelen bör du automatiskt loggas in på den RStudio Connect som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

