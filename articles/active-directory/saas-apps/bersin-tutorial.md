---
title: 'Självstudiekurs: Azure Active Directory-integrering med Bersin | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bersin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc9d229-69fb-4ab5-b6c3-5f3d790ea13e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c6794baab00dbb59df89187c8ab91f1a42ab03e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158885"
---
# <a name="tutorial-azure-active-directory-integration-with-bersin"></a>Självstudiekurs: Azure Active Directory-integrering med Bersin

I den här självstudien får du lära dig hur du integrerar Bersin med Azure Active Directory (Azure AD).
Genom att integrera Bersin med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Bersin.
* Du kan aktivera dina användare så att de automatiskt loggas in på Bersin (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Bersin behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Bersin enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Bersin stöder **SP och IDP** initierade SSO

## <a name="adding-bersin-from-the-gallery"></a>Lägga till Bersin från galleriet

Om du vill konfigurera integreringen av Bersin i Azure AD måste du lägga till Bersin från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Bersin från galleriet**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Bersin**i sökrutan och välj **Bersin** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Bersin i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD-enkel inloggning med Bersin baserat på en testanvändare som heter **Britta Simon** För enkel inloggning för att fungera, måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Bersin upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Bersin måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Bersin Single Sign-On](#configure-bersin-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Bersin-testanvändare](#create-bersin-test-user)** - om du vill ha en motsvarighet till Britta Simon i Bersin som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Bersin:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Bersin-programintegration**. **Bersin**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Gör följande steg om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration:**

    ![Bersin-domän och webbadresser med enkel inloggning](common/idp-identifier-relay.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://www.bersin.com/shibboleth`

    b. Klicka på **Ange ytterligare URL:er**.

    c. Skriv en URL med följande mönster i textrutan **Vidarebefordransstatus**: `https://www.bersin.com/secure/`

5. Klicka på **Ange ytterligare webbadresser** och gör följande om du vill konfigurera programmet i **återupptastartat SP-läge:**

    ![Bersin-domän och webbadresser med enkel inloggning](common/metadata-upload-additional-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://www.bersin.com/Login.aspx`

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Bersin.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-bersin-single-sign-on"></a>Konfigurera Bersin Enkel inloggning

Om du vill konfigurera enkel inloggning på **Bersin-sidan** skickar du den nedladdade **XML-koden för federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [Bersin-supportteamet](mailto:ramansabde@gmail.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Gör följande i egenskaperna Användare.

    ![Dialogrutan Användare](common/user-properties.png)

    a. Ange **BrittaSimon**i fältet **Namn** .
  
    b. Skriv **brittasimon\@ditt företaganydomain.extension** i fältet **Användarnamn**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Bersin.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Bersin**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Bersin**i programlistan .

    ![Bersin-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Klicka på knappen **Välj** längst ned på skärmen.

7. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-bersin-test-user"></a>Skapa Bersin-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Bersin. Arbeta med [Bersin-supportteamet](mailto:USBersinServiceClient@deloitte.com) för att lägga till användarna i Bersin-plattformen eller domänen som måste läggas till i en tillåt-lista för Bersin-plattformen. Om domänen läggs till av teamet etableras användarna automatiskt till Bersin-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på bersinpanelen på åtkomstpanelen ska du automatiskt loggas in på den Bersin som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
