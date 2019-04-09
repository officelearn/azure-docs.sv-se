---
title: 'Självstudier: Azure Active Directory-integrering med små förbättringar | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och små förbättringar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 19d9624c5bb60f47ef4bfa1b0629327780c2a9c7
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266311"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Självstudier: Azure Active Directory-integrering med små förbättringar

I den här självstudien får du lära dig hur du integrerar små förbättringar med Azure Active Directory (AD Azure).
Integrera små förbättringar med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till små förbättringar.
* Du kan aktivera användarna att vara automatiskt inloggad till små förbättringar (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med små förbättringar, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Små förbättringar av enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Små förbättringar stöder **SP** -initierad SSO

## <a name="adding-small-improvements-from-the-gallery"></a>Att lägga till små förbättringar från galleriet

För att konfigurera integrering av små förbättringar i Azure AD, som du behöver lägga till små förbättringar från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till små förbättringar från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **små förbättringar**väljer **små förbättringar** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Små förbättringar i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med små förbättringar som baseras på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och den relaterade användaren små förbättringar i upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med små förbättringar, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera små förbättringar av enkel inloggning](#configure-small-improvements-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa små förbättringar testanvändare](#create-small-improvements-test-user)**  – du har en motsvarighet för Britta Simon i små förbättringar som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med små förbättringar:

1. I den [Azure-portalen](https://portal.azure.com/)på den **små förbättringar** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Små förbättringar domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL enligt följande mönster: `https://<subdomain>.small-improvements.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [små förbättringar klienten supportteamet](mailto:support@small-improvements.com) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera små förbättringar** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-small-improvements-single-sign-on"></a>Konfigurera små förbättringar av enkel inloggning

1. I ett nytt webbläsarfönster inloggning till webbplatsen förbättringar av små företag som administratör.

1. Huvudinstrumentpanel-sidan klickar du på **Administration** knappen till vänster.

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Klicka på den **SAML SSO** knappen **integreringar** avsnittet.

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. På konfigurationssidan för enkel inloggning utför du följande steg:

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. I den **HTTP-slutpunkt** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    b. Öppna din nedladdade certifikatet i anteckningar, kopiera innehållet och klistra in den i den **x509 certifikat** textrutan. 

    c. Om du vill ha enkel inloggning och logga in formuläret autentiseringsalternativet tillgänglig för användare, kontrollerar du den **Aktivera åtkomst via inloggning för** alternativet.  

    d. Ange rätt värde för att nämna knappen SSO-inloggning i den **SAML fråga** textrutan.  

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
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till små förbättringar.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **små förbättringar**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **små förbättringar**.

    ![Länken små förbättringar i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-small-improvements-test-user"></a>Skapa små förbättringar testanvändare

Om du vill aktivera Azure AD-användare att logga in på små förbättringar, måste de etableras i små förbättringar. När det gäller små förbättringar är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Inloggning till webbplatsen förbättringar av små företag som administratör.

1. Från startsidan går du till menyn på vänster och sedan på **Administration**.

1. Klicka på den **användarkatalog** knappen Användarhantering avsnitt.

    ![Skapa en Azure AD-användare för testning](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Klicka på **lägga till användare**.

    ![Skapa en Azure AD-användare för testning](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. På den **Lägg till användare** dialogrutan utför följande steg: 

    ![Skapa en Azure AD-användare för testning](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Ange den **Förnamn** för användare som **Britta**.

    b. Ange den **efternamn** för användare som **Simon**.

    c. Ange den **e-post** för användare som **brittasimon@contoso.com**.

    d. Du kan också välja att ange personligt meddelande i den **skicka e-postmeddelande** box. Om du inte vill skicka meddelandet, avmarkerar du den här kryssrutan.

    e. Klicka på **skapa användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen små förbättringar i åtkomstpanelen, bör det vara loggas in automatiskt till små förbättringar som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)