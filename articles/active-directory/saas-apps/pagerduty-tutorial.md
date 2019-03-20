---
title: 'Självstudier: Azure Active Directory-integrering med PagerDuty | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 5470c13f75d010634f97e87dc1a870a100187973
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57835075"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Självstudier: Azure Active Directory-integrering med PagerDuty

I den här självstudien får du lära dig hur du integrerar PagerDuty med Azure Active Directory (AD Azure).
Integrera PagerDuty med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till PagerDuty.
* Du kan aktivera användarna att vara automatiskt inloggad till PagerDuty (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med PagerDuty, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* PagerDuty enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för PagerDuty **SP** -initierad SSO

## <a name="adding-pagerduty-from-the-gallery"></a>Att lägga till PagerDuty från galleriet

För att konfigurera integrering av PagerDuty i Azure AD, som du behöver lägga till PagerDuty från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till PagerDuty från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **PagerDuty**väljer **PagerDuty** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![PagerDuty i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med PagerDuty baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i PagerDuty upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med PagerDuty, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera PagerDuty Single Sign-On](#configure-pagerduty-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare PagerDuty](#create-pagerduty-test-user)**  – du har en motsvarighet för Britta Simon i PagerDuty som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med PagerDuty:

1. I den [Azure-portalen](https://portal.azure.com/)på den **PagerDuty** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![PagerDuty domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenant-name>.pagerduty.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [PagerDuty klienten supportteamet](https://www.pagerduty.com/support/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera PagerDuty** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-pagerduty-single-sign-on"></a>Konfigurera PagerDuty Single Sign-On

1. Logga in på webbplatsen Pagerduty företag som en administratör i ett annat webbläsarfönster.

2. Klicka på menyn längst upp **kontoinställningar**.

    ![Kontoinställningar](./media/pagerduty-tutorial/ic778535.png "kontoinställningar")

3. Klicka på **Enkel inloggning**.

    ![Single sign-on (Enkel inloggning)](./media/pagerduty-tutorial/ic778536.png "Single sign-on (Enkel inloggning)")

4. På den **aktivera enkel inloggning (SSO)** utför följande steg:

    ![Aktivera enkel inloggning](./media/pagerduty-tutorial/ic778537.png "aktivera enkel inloggning")

    a. Öppna din Base64-kodat certifikat som hämtats från Azure-portalen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** textrutan
  
    b. I textrutan för **inloggnings-URL** klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.
  
    c. I textrutan **Logout URL** (Utloggnings-URL) klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    d. Välj **inloggningen för Tillåt användarnamn/lösenord**.

    e. Välj **kräver exakt autentisering kontext jämförelse** kryssrutan.

    f. Klicka på **Spara ändringar**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till PagerDuty.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **PagerDuty**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **PagerDuty**.

    ![Länken PagerDuty i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-pagerduty-test-user"></a>Skapa PagerDuty testanvändare

Om du vill aktivera Azure AD-användare att logga in på PagerDuty, måste de etableras i PagerDuty.  
När det gäller PagerDuty är etablering en manuell aktivitet.

>[!NOTE]
>Du kan använda alla andra Pagerduty användare konto verktyg för att skapa eller API: er som tillhandahålls av Pagerduty för att etablera Azure Active Directory användarkonton.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **Pagerduty** klient.

2. Klicka på menyn längst upp **användare**.

3. Klicka på **lägga till användare**.
   
    ![Lägga till användare](./media/pagerduty-tutorial/ic778539.png "lägga till användare")

4.  På den **bjuda in ditt team** dialogrutan utför följande steg:
   
    ![Bjud in ditt team](./media/pagerduty-tutorial/ic778540.png "bjuda in ditt team")

    a. Skriv den **första och sista namnet** för användare som **Britta Simon**. 
   
    b. Ange **e-post** -adressen för användaren som **brittasimon\@contoso.com**.
   
    c. Klicka på **Lägg till**, och klicka sedan på **skicka inbjudningar**.
   
    >[!NOTE]
    >Alla tillagda användare får en inbjudan till att skapa en PagerDuty-konto.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen PagerDuty i åtkomstpanelen, bör det vara loggas in automatiskt till PagerDuty som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

