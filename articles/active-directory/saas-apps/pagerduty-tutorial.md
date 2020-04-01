---
title: 'Självstudiekurs: Azure Active Directory-integrering med PagerDuty | Microsoft-dokument'
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
ms.openlocfilehash: b7a522aaf35303bbd87e7aafe65b1302f1b98bc0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095338"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Självstudiekurs: Azure Active Directory-integrering med PagerDuty

I den här självstudien får du lära dig hur du integrerar PagerDuty med Azure Active Directory (Azure AD).
Genom att integrera PagerDuty med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till PagerDuty.
* Du kan aktivera dina användare så att de automatiskt loggas in på PagerDuty (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med PagerDuty behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* PagerDuty enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* PagerDuty stöder **SP** initierade SSO

## <a name="adding-pagerduty-from-the-gallery"></a>Lägga till PagerDuty från galleriet

Om du vill konfigurera integreringen av PagerDuty i Azure AD måste du lägga till PagerDuty från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till PagerDuty från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **PagerDuty**i sökrutan och välj **PagerDuty** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![PagerDuty i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med PagerDuty baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i PagerDuty upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med PagerDuty måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera PagerDuty Single Sign-On](#configure-pagerduty-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa PagerDuty-testanvändare](#create-pagerduty-test-user)** – om du vill ha en motsvarighet till Britta Simon i PagerDuty som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du enkel Azure AD-inloggning med PagerDuty:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **PagerDuty-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![PagerDuty Domän och webbadresser enkel inloggningsinformation](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenant-name>.pagerduty.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [PagerDuty Client support team](https://www.pagerduty.com/support/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera PagerDuty.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-pagerduty-single-sign-on"></a>Konfigurera PagerDuty Enkel inloggning

1. I ett annat webbläsarfönster loggar du in på din Pagerduty-företagswebbplats som administratör.

2. Klicka på **Kontoinställningar**högst upp.

    ![Kontoinställningar](./media/pagerduty-tutorial/ic778535.png "Kontoinställningar")

3. Klicka **på Enkel inloggning**.

    ![Enkel inloggning](./media/pagerduty-tutorial/ic778536.png "Enkel inloggning")

4. Gör följande på sidan **Aktivera enkel inloggning (SSO):**

    ![Aktivera enkel inloggning](./media/pagerduty-tutorial/ic778537.png "Aktivera enkel inloggning")

    a. Öppna ditt bas-64-kodade certifikat som hämtats från Azure-portalen i anteckningar, kopiera innehållet i det i Urklipp och klistra sedan in det i textrutan **X.509 Certifikat**
  
    b. I textrutan för **inloggnings-URL** klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.
  
    c. I textrutan **Logout URL** (Utloggnings-URL) klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    d. Välj **Tillåt inloggning av användarnamn/lösenord**.

    e. Välj **Kryssrutan Kräv exakt autentiseringskontextjämn.**

    f. Klicka på **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till PagerDuty.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **PagerDuty**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **PagerDuty**i programlistan .

    ![PagerDuty-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-pagerduty-test-user"></a>Skapa PagerDuty-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på PagerDuty måste de etableras i PagerDuty.  
När det gäller PagerDuty är etablering en manuell aktivitet.

>[!NOTE]
>Du kan använda andra verktyg eller API:er för att skapa sidortjed för att etablera Azure Active Directory-användarkonton.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **Pagerduty-klient.**

2. Klicka på **Användare**i menyn högst upp .

3. Klicka på **Lägg till användare**.
   
    ![Lägg till användare](./media/pagerduty-tutorial/ic778539.png "Lägg till användare")

4.  Gör följande i dialogrutan **Bjud in ditt team:**
   
    ![Bjud in ditt team](./media/pagerduty-tutorial/ic778540.png "Bjud in ditt team")

    a. Skriv **för- och efternamn** för användare som **Britta Simon**. 
   
    b. Ange **e-postadress** för användare som **\@brittasimon contoso.com**.
   
    c. Klicka på **Lägg till**och sedan på **Skicka inbjudningar**.
   
    >[!NOTE]
    >Alla tillagda användare får en inbjudan om att skapa ett PagerDuty-konto.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen PagerDuty på åtkomstpanelen ska du automatiskt loggas in på den PagerDuty som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

