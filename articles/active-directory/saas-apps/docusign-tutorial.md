---
title: 'Självstudier: Azure Active Directory-integrering med DocuSign | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 35fc4e855ef53bd7e667fc8de7146916b4d05acb
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65407068"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Självstudier: Azure Active Directory-integrering med DocuSign

I den här självstudien får du lära dig hur du integrerar DocuSign med Azure Active Directory (AD Azure).
Integrera DocuSign med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till DocuSign.
* Du kan aktivera användarna att vara automatiskt inloggad till DocuSign (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med DocuSign, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* DocuSign enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för DocuSign **SP** -initierad SSO

* Har stöd för DocuSign **Just In Time** etableringen av användare

## <a name="adding-docusign-from-the-gallery"></a>Att lägga till DocuSign från galleriet

För att konfigurera integrering av DocuSign i Azure AD, som du behöver lägga till DocuSign från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till DocuSign från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **DocuSign**väljer **DocuSign** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![DocuSign i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med DocuSign baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i DocuSign upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med DocuSign, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera DocuSign Single Sign-On](#configure-docusign-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare DocuSign](#create-docusign-test-user)**  – du har en motsvarighet för Britta Simon i DocuSign som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med DocuSign:

1. I den [Azure-portalen](https://portal.azure.com/)på den **DocuSign** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![DocuSign domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare som beskrivs senare **visa SAML 2.0 slutpunkter** avsnitt i självstudien.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. På den **konfigurera DocuSign** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-docusign-single-sign-on"></a>Konfigurera DocuSign Single Sign-On

1. I ett annat webbläsarfönster, loggar till din **DocuSign administrationsportalen** som administratör.

2. Längst upp till höger på sidan klickar du på profilen **logotyp** och klicka sedan på **gå till administratör**.
  
    ![Konfigurerar enkel inloggning][51]

3. På sidan med lösningar domän klickar du på **domäner**

    ![Konfigurerar enkel inloggning][50]

4. Under den **domäner** klickar du på **anspråk domän**.

    ![Konfigurerar enkel inloggning][52]

5. På den **gör anspråk på en domän** dialogrutan i den **domännamn** textruta, ange din företagsdomän och klicka sedan på **anspråk**. Se till att du verifiera domänen och att statusen är aktiv.

    ![Konfigurerar enkel inloggning][53]

6. På sidan med lösningar domän klickar du på **Identitetsprovidrar**.
  
    ![Konfigurerar enkel inloggning][54]

7. Under **Identitetsprovidrar** klickar du på **Lägg till IDENTITETSPROVIDER**. 

    ![Konfigurerar enkel inloggning][55]

8. På den **identitet providerinställningar** utför följande steg:

    ![Konfigurerar enkel inloggning][56]

    a. I den **namn** textrutan skriver du ett unikt namn för din konfiguration. Använd inte blanksteg.

    b. I den **Providerutgivare textrutan**, klistra in värdet för **Azure AD-identifierare**, som du har kopierat från Azure-portalen.

    c. I den **inloggnings-URL för identitetsprovider** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    d. I den **utloggnings-URL för identitetsprovider** textrutan klistra in värdet för **URL för utloggning**, som du har kopierat från Azure-portalen.

    e. Välj **logga AuthN begäran**.

    f. Som **skicka AuthN-begäran från**väljer **POST**.

    g. Som **skicka utloggningsbegäran från**väljer **hämta**.

    h. I den **anpassad attributmappning** klickar du på **Lägg till ny MAPPNING**.

    ![Konfigurerar enkel inloggning][62]

    i. Välj fältet som du vill mappa med Azure AD-anspråk. I det här exemplet på **e-postadress** anspråk har mappats med värdet för **https://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Det är standardnamnet för anspråk från Azure AD för e-anspråk och klicka sedan på **spara**.

    ![Konfigurerar enkel inloggning][57]

    > [!NOTE]
    > Använd lämplig **användaridentifierare** att mappa användaren från Azure AD att DocuSign användarmappning. Välj rätt fältet och ange rätt värde baserat på Organisationsinställningarna för din.

    j. I den **identitet providern certifikat** klickar du på **Lägg till certifikat**, och sedan ladda upp det certifikat som du har hämtat från Azure AD-portalen och klicka på **spara**.

    ![Konfigurerar enkel inloggning][58]

    k. I den **Identitetsprovidrar** klickar du på **åtgärder**, och klicka sedan på **slutpunkter**.

    ![Konfigurerar enkel inloggning][59]

    l. I den **visa SAML 2.0 slutpunkter** avsnittet på **DocuSign administrationsportalen**, utför följande steg:

    ![Konfigurerar enkel inloggning][60]

    * Kopiera den **utfärdar-URL för Service Provider**, och klistra in den i den **identifierare** textrutan i **SAML grundkonfiguration** avsnittet på Azure portal.

    * Kopiera den **inloggnings-URL för tjänsten**, och klistra in den i den **inloggning på URL: en** -textrutan i **grundläggande SAML-konfiguration** avsnittet på Azure portal.

    * Klicka på **Stäng**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till DocuSign.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **DocuSign**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **DocuSign**.

    ![DocuSign-länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-docusign-test-user"></a>Skapa DocuSign testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i DocuSign. DocuSign stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i DocuSign, skapas en ny efter autentisering.

>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [DocuSign-supportteamet](https://support.docusign.com/).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen DocuSign i åtkomstpanelen, bör det vara loggas in automatiskt till DocuSign som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
