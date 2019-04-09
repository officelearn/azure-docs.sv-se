---
title: 'Självstudier: Azure Active Directory-integrering med Jitbit Helpdesk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jitbit Helpdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 851b28d10bdf0b2df67e1c0782a683e790b711bc
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266502"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Självstudier: Azure Active Directory-integrering med Jitbit Helpdesk

I den här självstudien får du lära dig hur du integrerar Jitbit Helpdesk med Azure Active Directory (AD Azure).
Integrera Jitbit Helpdesk med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Jitbit Helpdesk.
* Du kan aktivera användarna att vara automatiskt inloggad Jitbit Helpdesk (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Jitbit Helpdesk, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Jitbit Helpdesk enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Jitbit Helpdesk **SP** -initierad SSO

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Att lägga till Jitbit Helpdesk från galleriet

För att konfigurera integrering av Jitbit Helpdesk i Azure AD, som du behöver lägga till Jitbit Helpdesk från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Jitbit Helpdesk från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Jitbit Helpdesk**väljer **Jitbit Helpdesk** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Jitbit Helpdesk i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Jitbit Helpdesk baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Jitbit Helpdesk upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Jitbit Helpdesk, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Jitbit-supportavdelningen](#configure-jitbit-helpdesk-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Jitbit Helpdesk testanvändare](#create-jitbit-helpdesk-test-user)**  – du har en motsvarighet för Britta Simon i Jitbit Helpdesk som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Jitbit Helpdesk:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Jitbit Helpdesk** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Jitbit supportavdelningen domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL enligt följande mönster:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Jitbit supportavdelningen klienten supportteamet](https://www.jitbit.com/support/) att hämta det här värdet.

    b. I den **identifierare (entitets-ID)** text skriver en URL som följande: `https://www.jitbit.com/web-helpdesk/`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I den **konfigurera Jitbit Helpdesk** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Konfigurera Jitbit supportavdelningen enkel inloggning

1. I ett annat webbläsarfönster, loggar du in din Jitbit Helpdesk företagets webbplats som administratör.

1. I verktygsfältet högst upp, klickar du på **Administration**.

    ![Administration](./media/jitbit-helpdesk-tutorial/ic777681.png "Administration")

1. Klicka på **allmänna inställningar**.

    ![Användare, företag och behörigheter](./media/jitbit-helpdesk-tutorial/ic777680.png "användare, företag och behörigheter")

1. I den **autentiseringsinställningar** konfiguration och utför följande steg:

    ![Autentiseringsinställningar](./media/jitbit-helpdesk-tutorial/ic777683.png "autentiseringsinställningar")

    a. Välj **aktivera SAML 2.0 enkel inloggning**, för att logga in med enkel inloggning (SSO), med **OneLogin**.

    b. I den **slutpunkts-URL** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.

    c. Öppna din **Base64-** kodade certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** textrutan

    d. Klicka på **Spara ändringar**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Jitbit Helpdesk.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Jitbit Helpdesk**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Jitbit Helpdesk**.

    ![Jitbit Helpdesk-länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-jitbit-helpdesk-test-user"></a>Skapa Jitbit Helpdesk testanvändare

För att aktivera Azure AD-användare att logga in på Jitbit Helpdesk, måste de etableras i Jitbit Helpdesk. När det gäller Jitbit Helpdesk är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **Jitbit Helpdesk** klient.

1. Klicka på menyn längst upp **Administration**.

    ![Administration](./media/jitbit-helpdesk-tutorial/ic777681.png "Administration")

1. Klicka på **användare, företag och behörigheter**.

    ![Användare, företag och behörigheter](./media/jitbit-helpdesk-tutorial/ic777682.png "användare, företag och behörigheter")

1. Klicka på **Lägg till användare**.

    ![Lägg till användare](./media/jitbit-helpdesk-tutorial/ic777685.png "Lägg till användare")

1. I avsnittet Skapa skriver du data för Azure AD-konto som du vill etablera enligt följande:

    ![Skapa](./media/jitbit-helpdesk-tutorial/ic777686.png "skapa")

   a. I den **användarnamn** textrutan typ användarnamnet för användaren som **BrittaSimon**.

   b. I den **e-post** textrutan e-post för användaren som **BrittaSimon@contoso.com**.

   c. I textrutan **Förnamn** skriver du förnamnet på användaren: **Britta**.

   d. I textrutan **Efternamn** skriver du efternamnet på användaren: **Simon**.

   e. Klicka på **Skapa**.

> [!NOTE]
> Du kan använda andra verktyg för Jitbit Helpdesk användare konto att skapa eller tillhandahålls av Jitbit Helpdesk-API: er för att etablera användarkonton i Azure AD.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Jitbit Helpdesk i åtkomstpanelen, bör det vara loggas in automatiskt Jitbit-Helpdesk som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
