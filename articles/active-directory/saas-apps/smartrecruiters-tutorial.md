---
title: 'Självstudier: Azure Active Directory-integrering med SmartRecruiters | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SmartRecruiters.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e96aeecd-e113-454e-89c3-58c9f44cfd4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 6cf24dd5bd43670efbc41b89ca62ec061dc0dbc3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58177610"
---
# <a name="tutorial-azure-active-directory-integration-with-smartrecruiters"></a>Självstudier: Azure Active Directory-integrering med SmartRecruiters

I den här självstudien får du lära dig hur du integrerar SmartRecruiters med Azure Active Directory (AD Azure).
Integrera SmartRecruiters med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SmartRecruiters.
* Du kan aktivera användarna att vara automatiskt inloggad till SmartRecruiters (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SmartRecruiters, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SmartRecruiters enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för SmartRecruiters **SP och IDP** -initierad SSO

## <a name="adding-smartrecruiters-from-the-gallery"></a>Att lägga till SmartRecruiters från galleriet

För att konfigurera integrering av SmartRecruiters i Azure AD, som du behöver lägga till SmartRecruiters från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SmartRecruiters från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **SmartRecruiters**väljer **SmartRecruiters** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![SmartRecruiters i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SmartRecruiters baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SmartRecruiters upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med SmartRecruiters, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SmartRecruiters Single Sign-On](#configure-smartrecruiters-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare SmartRecruiters](#create-smartrecruiters-test-user)**  – du har en motsvarighet för Britta Simon i SmartRecruiters som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med SmartRecruiters:

1. I den [Azure-portalen](https://portal.azure.com/)på den **SmartRecruiters** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![SmartRecruiters domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://www.smartrecruiters.com/web-sso/saml/<companyname>`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://www.smartrecruiters.com/web-sso/saml/<companyname>/callback`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![SmartRecruiters domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://www.smartrecruiters.com/web-sso/saml/<companyname>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [SmartRecruiters klienten supportteamet](https://www.smartrecruiters.com/about-us/contact-us/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. På den **konfigurera SmartRecruiters** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-smartrecruiters-single-sign-on"></a>Konfigurera SmartRecruiters Single Sign-On

1. I ett annat webbläsarfönster logga du in på webbplatsen SmartRecruiters företag som administratör.

1. Gå till **inställningar / Admin**.

    ![SmartRecruiters konfiguration](./media/smartrecruiters-tutorial/configure.png)

1. I den **Configuration** klickar du på **Enkelinloggning**.

    ![SmartRecruiters konfiguration](./media/smartrecruiters-tutorial/configure1.png)

1. Visa/dölj **aktivera enkel inloggning på webben**.

    ![SmartRecruiters konfiguration](./media/smartrecruiters-tutorial/configure2.png)

1. I **identitet providerkonfigurationen**, utför följande steg:

    ![SmartRecruiters konfiguration](./media/smartrecruiters-tutorial/configure4.png)

    a. I **-URL för identitetsprovider** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.

    b. Öppna **certificate(Base64)** som du har hämtat från Azure-portalen i anteckningar, kopiera innehållet i den och klistra in i **identitetsprovider certifikat** textrutan.

1. Klicka på **spara Web SSO-konfiguration**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SmartRecruiters.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **SmartRecruiters**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **SmartRecruiters**.

    ![Länken SmartRecruiters i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-smartrecruiters-test-user"></a>Skapa SmartRecruiters testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i SmartRecruiters. Arbeta med [SmartRecruiters supportteam](https://www.smartrecruiters.com/about-us/contact-us/) att lägga till användare i SmartRecruiters-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen SmartRecruiters i åtkomstpanelen, bör det vara loggas in automatiskt till SmartRecruiters som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

