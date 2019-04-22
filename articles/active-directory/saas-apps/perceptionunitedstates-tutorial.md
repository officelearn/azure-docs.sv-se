---
title: 'Självstudier: Azure Active Directory-integrering med Perception USA (icke-UltiPro) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Perception USA (icke-UltiPro).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 4b7a9409052d8255bbad00b38217bcff030e8620
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59277025"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Självstudier: Azure Active Directory-integrering med Perception USA (icke-UltiPro)

I den här självstudien får du lära dig hur du integrerar Perception USA (icke-UltiPro) med Azure Active Directory (AD Azure).
Integrera Perception USA (icke-UltiPro) med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Perception USA (icke-UltiPro).
* Du kan aktivera användarna att vara automatiskt inloggad till Perception USA (icke-UltiPro) (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Perception USA (icke-UltiPro), behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Enkel inloggning för perception-USA (icke-UltiPro) aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Perception USA (icke-UltiPro) stöder **IDP** -initierad SSO

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Att lägga till Perception USA (icke-UltiPro) från galleriet

För att konfigurera integrering av Perception USA (icke-UltiPro) till Azure AD, måste du lägga till Perception USA (icke-UltiPro) från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Perception USA (icke-UltiPro) från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Perception USA (icke-UltiPro)** väljer **Perception USA (icke-UltiPro)** resultatet panelen klickar **Lägg till** för att lägga till den programmet.

     ![Perception USA (icke-UltiPro) i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Perception USA (icke-UltiPro) baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Perception USA (icke-UltiPro) ska upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Perception USA (icke-UltiPro), måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Perception USA (icke-UltiPro) Single Sign-On](#configure-perception-united-states-non-ultipro-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Perception USA (icke-UltiPro)](#create-perception-united-states-non-ultipro-test-user)**  – du har en motsvarighet för Britta Simon i Perception USA (icke-UltiPro) som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Perception USA (icke-UltiPro):

1. I den [Azure-portalen](https://portal.azure.com/)på den **Perception USA (icke-UltiPro)** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Perception USA (icke-UltiPro)-domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du in en URL: `https://perception.kanjoya.com/sp`

    b. Skriv en URL med följande mönster i textrutan **Svars-URL**: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. Den **Perception USA (icke-UltiPro)** programmet kräver den **Azure AD-identifierare** värde som < entity_id >, där du får från den **konfigurera Perception USA ( Non-UltiPro)** så att uri-kodas. Om du vill få uri-kodas, använder du följande länk: **http://www.url-encode-decode.com/**.

    d. När du har fått URI: n kodad värde kombinera den med den **svars-URL** som nämns nedan –

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Klistra in ovanstående värde i den **svars-URL** textrutan.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **ange upp Perception USA (icke-UltiPro)** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Konfigurera enkel inloggning för Perception-USA (icke-UltiPro)

1. I ett nytt webbläsarfönster inloggning till webbplatsen för företagets Perception USA (icke-UltiPro) som administratör.

2. I verktygsfältet klickar du på **kontoinställningar**.

    ![Perception USA (icke-UltiPro)-användare](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. På den **kontoinställningar** utför följande steg:

    ![Perception USA (icke-UltiPro)-användare](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. I den **företagsnamn** textrutan skriver du namnet på den **företagets**.
    
    b. I den **kontonamn** textrutan skriver du namnet på den **konto**.

    c. I **standard svara till e-post** text skriver giltiga **e-post**.

    d. Välj **SSO identitetsprovider** som **SAML 2.0**.

4. På den **SSO-konfiguration** utför följande steg:

    ![Perception USA (icke-UltiPro) SSOConfig](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Välj **SAML NameID typ** som **e-post**.

    b. I den **SSO-Konfigurationsnamnet** textrutan skriver du namnet på din **Configuration**.
    
    c. I **namn på identitetsprovider** textrutan klistra in värdet för **Azure AD-identifierare**, som du har kopierat från Azure-portalen. 

    d. I **SAML domän textrutan**, anger du domänen som @contoso.com.

    e. Klicka på **ladda upp igen** att ladda upp den **XML-Metadata för** fil.

    f. Klicka på **Uppdatera**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till Perception USA (icke-UltiPro).

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Perception USA (icke-UltiPro)**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Perception USA (icke-UltiPro)**.

    ![Länken Perception USA (icke-UltiPro) i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Skapa testanvändare Perception USA (icke-UltiPro)

I det här avsnittet skapar du en användare som kallas Britta Simon Perception United States (icke-UltiPro). Arbeta med [Perception USA (icke-UltiPro) supportteamet](https://www.ultimatesoftware.com/Contact/ContactUs) att lägga till användare i USA för Perception (icke-UltiPro)-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Perception USA (icke-UltiPro) i åtkomstpanelen bör du vara loggas in automatiskt till Perception USA (icke-UltiPro) som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

