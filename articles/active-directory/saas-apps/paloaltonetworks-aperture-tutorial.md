---
title: 'Självstudier: Azure Active Directory-integrering med Palo Alto Networks - öppning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Palo Alto Networks - öppning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: beb84671db01020f0118502e35128387d36845d6
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361429"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Självstudier: Azure Active Directory-integrering med Palo Alto Networks - öppning

I den här självstudien får du lära dig hur du integrerar Palo Alto Networks - öppning med Azure Active Directory (AD Azure).
Integrera Palo Alto Networks - öppning med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Palo Alto Networks - öppning.
* Du kan aktivera användarna att vara automatiskt inloggad till Palo Alto Networks - öppning (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Palo Alto Networks - öppning, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Palo Alto Networks - öppning enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Palo Alto Networks - öppning **SP** och **IDP** -initierad SSO

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Att lägga till Palo Alto Networks - öppning från galleriet

Om du vill konfigurera integreringen av Palo Alto Networks - öppning i Azure AD som du behöver lägga till Palo Alto Networks - öppning från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Palo Alto Networks - öppning från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Palo Alto Networks - öppning**väljer **Palo Alto Networks - öppning** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Palo Alto Networks - öppning i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Palo Alto Networks - öppning baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning till arbete, en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Palo Alto Networks - måste öppning upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Palo Alto Networks - öppning, måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Palo Alto Networks - öppning Single Sign-On](#configure-palo-alto-networks---aperture-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Palo Alto Networks - öppning testanvändare](#create-palo-alto-networks---aperture-test-user)**  – du har en motsvarighet för Britta Simon i Palo Alto Networks - öppning som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera Azure AD utför enkel inloggning med Palo Alto Networks - öppning, du följande steg:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Palo Alto Networks - öppning** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Palo Alto Networks - öppning domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Palo Alto Networks - öppning domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Palo Alto Networks - supportteamet för öppning klienten](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. På den **konfigurera Palo Alto Networks - öppning** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-palo-alto-networks---aperture-single-sign-on"></a>Konfigurera Palo Alto Networks - öppning enkel inloggning

1. I ett annat webbläsarfönster, logga in på Palo Alto Networks - öppning som administratör.

2. På den övre menyraden klickar du på **inställningar**.

    ![Inställningsfliken](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. Gå till **programmet** klickar du på avsnittet **autentisering** formuläret till vänster på menyn.

    ![Fliken Auth](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. På den **autentisering** sidan utför följande steg:
    
    ![Fliken autentisering](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Kontrollera den **aktivera enkel inloggning-On(Supported SSP Providers are Okta, One login)** från **enkel inloggning** fält.

    b. I den **identitet Provider-ID** textrutan klistra in värdet för **Azure AD-identifierare**, som du har kopierat från Azure-portalen.

    c. Klicka på **Välj fil** att ladda upp det nedladdade certifikatet från Azure AD i den **providern identitetscertifikat** fält.

    d. I den **SSO-URL för identitetsprovider** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    e. Granska IdP-informationen från **öppning Info** avsnittet och hämta certifikatet från **öppning nyckeln** fält.

    f. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Palo Alto Networks - öppning.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Palo Alto Networks - öppning**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Palo Alto Networks - öppning**.

    ![Palo Alto nätverk - öppning länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-palo-alto-networks---aperture-test-user"></a>Skapa Palo Alto Networks - öppning testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Palo Alto Networks - öppning. Arbeta med [Palo Alto Networks - supportteamet för öppning klienten](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) att lägga till användare i Palo Alto Networks - öppning plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Palo Alto Networks - Öppning panel i åtkomstpanelen, bör du vara automatiskt inloggad på Palo Alto Networks - öppning som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

