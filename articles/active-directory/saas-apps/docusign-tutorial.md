---
title: 'Självstudier: Azure Active Directory integrering med DocuSign | Microsoft Docs'
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
ms.openlocfilehash: 5c7d6116ed2925e57f094a67f27a11f9e2d61831
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499250"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Självstudier: Azure Active Directory integrering med DocuSign

I den här självstudien får du lära dig hur du integrerar DocuSign med Azure Active Directory (Azure AD).
Genom att integrera DocuSign med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till DocuSign.
* Du kan göra det möjligt för användarna att logga in automatiskt till DocuSign (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med DocuSign behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* DocuSign-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* DocuSign stöder **SP** -INITIERAd SSO

* DocuSign stöder [Automatisk användar etablering](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)

## <a name="adding-docusign-from-the-gallery"></a>Lägga till DocuSign från galleriet

Om du vill konfigurera integreringen av DocuSign i Azure AD måste du lägga till DocuSign från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till DocuSign från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **DocuSign**, väljer **DocuSign** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![DocuSign i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med DocuSign baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i DocuSign upprättas.

Om du vill konfigurera och testa enkel inloggning med DocuSign i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera DocuSign-enkel inloggning](#configure-docusign-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa DocuSign test User](#create-docusign-test-user)** – om du vill ha en motsvarighet till Britta Simon i DocuSign som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med DocuSign i Azure AD:

1. Välj **enkel inloggning**på sidan **DocuSign** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för DocuSign-domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska inloggnings-URL: en och identifieraren som förklaras senare **Visa SAML 2,0-slut punkter** i självstudien.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. I avsnittet **Konfigurera DocuSign** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-docusign-single-sign-on"></a>Konfigurera DocuSign enkel inloggning

1. Logga in på din **DocuSign-administratörs Portal** som administratör i ett annat webbläsarfönster.

2. Längst upp till höger på sidan klickar du på profil **logo typ** och sedan på **gå till administratör**.
  
    ![Konfigurerar enkel inloggning][51]

3. På sidan domän lösningar klickar du på **domäner**

    ![Konfigurerar enkel inloggning][50]

4. Under avsnittet **domäner** klickar du på **anspråks domän**.

    ![Konfigurerar enkel inloggning][52]

5. Skriv din företags domän i text rutan **domän namn** i dialog rutan **anspråk en domän** och klicka sedan på **anspråk**. Kontrol lera att du verifierar domänen och att statusen är aktiv.

    ![Konfigurerar enkel inloggning][53]

6. Klicka på **identitets leverantörer**på sidan domän lösningar.
  
    ![Konfigurerar enkel inloggning][54]

7. Under avsnittet **identitets leverantörer** klickar du på **Lägg till identitets leverantör**. 

    ![Konfigurerar enkel inloggning][55]

8. Utför följande steg på sidan **Inställningar för identitetsprovider** :

    ![Konfigurerar enkel inloggning][56]

    a. I text rutan **namn** anger du ett unikt namn för din konfiguration. Använd inte blank steg.

    b. I **text rutan för identitets leverantörs utfärdare**klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    c. I text rutan inloggnings- **URL för identitetsprovider** klistrar du in värdet för inloggnings- **URL: en**som du har kopierat från Azure Portal.

    d. I text rutan **URL för identitets leverantörs utloggning** klistrar du in värdet för **URL för utloggning**som du har kopierat från Azure Portal.

    e. Välj **signera authn-begäran**.

    f. Som **Skicka authn-begäran av**väljer du **post**.

    g. Som **Skicka utloggnings förfrågan efter**väljer du **Hämta**.

    h. I avsnittet **Mappning av anpassade attribut** klickar du på **Lägg till ny mappning**.

    ![Konfigurerar enkel inloggning][62]

    i. Välj det fält som du vill mappa med Azure AD-anspråk. I det här exemplet mappas **EmailAddress** -anspråket till värdet **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** . Det är standard anspråks namnet från Azure AD för e-postanspråk och klicka sedan på **Spara**.

    ![Konfigurerar enkel inloggning][57]

    > [!NOTE]
    > Använd rätt **användar identifierare** för att mappa användaren från Azure AD till DocuSign användar mappning. Välj fältet korrekt och ange lämpligt värde baserat på organisations inställningarna.

    j. I avsnittet **certifikat för identitetsprovider** klickar du på **Lägg till certifikat**och laddar sedan upp det certifikat som du har laddat ned från Azure AD Portal och klickar på **Spara**.

    ![Konfigurerar enkel inloggning][58]

    k. I avsnittet **identitets leverantörer** klickar du på **åtgärder**och sedan på **slut punkter**.

    ![Konfigurerar enkel inloggning][59]

    l. I avsnittet **Visa SAML 2,0** -slutpunkter på **DocuSign Admin Portal**, utför följande steg:

    ![Konfigurerar enkel inloggning][60]

    * Kopiera **URL: en för service providerns utfärdare**och klistra in den i text rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    * Kopiera **inloggnings-URL: en för tjänst leverantören**och klistra in den i text rutan för inloggnings- **URL** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

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
  
    b. I brittasimon@yourcompanydomain.extensionfältet **användar namn** . Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till DocuSign.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **DocuSign**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **DocuSign**.

    ![DocuSign-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-docusign-test-user"></a>Skapa DocuSign test användare

I det här avsnittet skapas en användare som kallas Britta Simon i DocuSign. DocuSign stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i DocuSign skapas en ny efter autentiseringen.

>[!Note]
>Kontakta [DocuSign support team](https://support.docusign.com/)om du behöver skapa en användare manuellt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen DocuSign på åtkomst panelen, bör du loggas in automatiskt på den DocuSign som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

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
