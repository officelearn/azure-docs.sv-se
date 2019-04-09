---
title: 'Självstudier: Azure Active Directory-integrering med Help Scout | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Help Scout.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35f9a8949f5b51f88b9297890fc5562e7b8dd591
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273387"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Självstudier: Azure Active Directory-integrering med Help Scout

I den här självstudien lär du dig att integrera Help Scout med Azure Active Directory (AD Azure).
Integreringen av Help Scout med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Help Scout.
* Du kan göra så att dina användare loggas in automatiskt på Help Scout (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Help Scout behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Help Scout-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Help Scout har stöd för **SP- och IDP**-initierad enkel inloggning
* Help Scout har stöd för **just-in-time**-användaretablering

## <a name="adding-help-scout-from-the-gallery"></a>Lägga till att Help Scout från galleriet

För att konfigurera integreringen av Help Scout i Azure AD måste du lägga till Help Scout från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till att Scout från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Help Scout**, väljer **Help Scout** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Help Scout i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Help Scout baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Help Scout upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Help Scout behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Help Scout](#configure-help-scout-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Help Scout-testanvändare](#create-help-scout-test-user)** – för att ha en motsvarighet för Britta Simon i Help Scout som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Help Scout:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Help Scout** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![Help Scout-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. **Identifieraren** är den **Målgrupps-URI (Service Provider entitets-ID)** från att Scout börjar med `urn:`

    b. **Svars-URL** är den **efter tillbaka URL (försäkran URL för Konsumenttjänst)** från att Scout börjar med `https://` 

    > [!NOTE]
    > Värdena i dessa URL:er är bara exempel. Du måste uppdatera dessa värden från faktiska svars-URL och identifierare. Du får dessa värden från fliken **Enkel inloggning** under avsnittet Autentisering, som beskrivs senare i självstudien.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Help Scout-domän och information om URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    I den **inloggnings-URL** textrutan anger du ett URL: en som: `https://secure.helpscout.net/members/login/`

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera Help Scout** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-help-scout-single-sign-on"></a>Konfigurera enkel inloggning för Help Scout

1. I ett annat webbläsarfönster loggar du in på din Help Scout-företagswebbplats som administratör.

2. Klicka på **Hantera** från den översta menyn och välj sedan **Företag** från den nedrullningsbara menyn.

    ![Konfigurera enkel inloggning](./media/helpscout-tutorial/settings1.png)

3. Välj **Autentisering** från det vänstra navigeringsfönstret.

    ![Konfigurera enkel inloggning](./media/helpscout-tutorial/settings2.png)

4. Då öppnas avsnittet SAML-inställningar. Utför följande steg:

    ![Konfigurera enkel inloggning](./media/helpscout-tutorial/settings3.png)

    a. Kopiera värdet för **Post-back URL (Assertion Consumer Service URL)** (Återskickande-URL (URL för Assertion Consumer Service)) och klistra in det i textrutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    b. Kopiera värdet för **Audience URI (Service Provider Entity ID)** (URI för målgrupp (Entitets-ID för serviceprovider)) och klistra in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Växla på **Aktivera SAML** och utför följande steg:

    ![Konfigurera enkel inloggning](./media/helpscout-tutorial/settings4.png)

    a. I **URL för enkel inloggning** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    b. Klicka på **Ladda upp certifikat** för att ladda upp det **certifikat (Base64)** som laddats ned från Azure-portalen.

    c. Ange din organisations e-postdomän, t.ex. `contoso.com`, i textrutan **Email Domains** (E-postdomäner). Du kan avgränsa flera domäner med ett kommatecken. Användare eller administratörer för Help Scout som går in i den specifika domänen på [inloggningssidan för Help Scout](https://secure.helpscout.net/members/login/) dirigeras till Identitetsprovider för att autentisera med sina autentiseringsuppgifter.

    d. Slutligen kan du växla på **Force SAML Sign-on** (Framtvinga SAML-inloggning) om du vill att användare endast ska logga in på Help Scout med den här metoden. Om du ändå vill låta alternativet att logga in med deras Help Scout-autentiseringsuppgifter vara kvar kan du lämna detta avstängt. Även om det här aktiveras kan en Kontoägare alltid logga in på Help Scout med sitt kontolösenord.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Help Scout.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Help Scout**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Help Scout**.

    ![Länken för Help Scout i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-help-scout-test-user"></a>Skapa Help Scout-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Help Scout. Help Scout har stöd för just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Help Scout skapas en ny efter autentisering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Help Scout-panelen i åtkomstpanelen bör du automatiskt loggas in på Help Scout som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)