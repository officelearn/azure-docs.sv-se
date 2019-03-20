---
title: 'Självstudier: Azure Active Directory-integrering med Appraisd | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd7d1e3511bf74650ddc9dca1cf77c2b7c297c96
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861510"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>Självstudier: Azure Active Directory-katalogintegrering med Appraisd

I den här självstudien lär du dig att integrera Appraisd med Azure Active Directory (AD Azure).
När du integrerar Appraisd med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Appraisd.
* Du kan göra så att dina användare automatiskt loggas in på Appraisd (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Appraisd behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Appraisd-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Appraisd har stöd för **SP och IDP**-initierad enkel inloggning

## <a name="adding-appraisd-from-the-gallery"></a>Lägga till Appraisd från galleriet

För att konfigurera integrering av Appraisd i Azure AD måste du lägga till Appraisd från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Appraisd från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Appraisd**, väljer **Appraisd** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Appraisd i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Appraisd baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Appraisd upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Appraisd måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Appraisd](#configure-appraisd-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Appraisd-testanvändare](#create-appraisd-test-user)** – för att ha en motsvarighet för Britta Simon i Appraisd som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Appraisd:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Appraisd** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![Appraisd-domän och information om URL:er för enkel inloggning](common/both-preintegrated-advanced-urls.png)

    a. Klicka på **Ange ytterligare URL:er**.

    b. Skriv en URL i textrutan **Vidarebefordransstatus**: `<TENANTCODE>`

    c. Om du vill konfigurera programmet i **SP**-initierat läge skriver du en URL med hjälp av följande mönster i textrutan **Inloggnings-URL**: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Du får det faktiska värdet för inloggnings-URL och värdet för vidarebefordransstatus på konfigurationssidan för Appraisd SSO som beskrivs senare i självstudien.

5. Appraisd-program förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:

    | Namn |  Källattribut|
    | ---------------| --------------- |
    | nameidentifier | user.mail |
    | | |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

8. I avsnittet **Konfigurera Appraisd** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-appraisd-single-sign-on"></a>Konfigurera enkel inloggning för Appraisd

1. I ett annat webbläsarfönster loggar du in på Appraisd som administratör.

2. Längst upp till höger på sidan klickar du på ikonen **Inställningar** och går sedan till **Konfiguration**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

3. På vänster sida av menyn klickar du på **Enkel inloggning med SAML**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

4. På sidan för **konfiguration av enkel inloggning för SAML 2.0** utför du följande steg:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Kopiera standardvärdet för **vidarebefordringsstatus** och klistra in det i textrutan **Vidarebefordransstatus** i  **Grundläggande SAML-konfiguration** på Azure-portalen.

    b. Kopiera värdet för **tjänstinitierad inloggningswebbadress** och klistra in det i textrutan **Inloggnings-URL** i  **Grundläggande SAML-konfiguration** på Azure-portalen.

5. Rulla nedåt på samma sida under **Identifiera användare** och utför följande steg:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. I textrutan **Identity Provider Single Sign-On URL** (URL för enkel inloggning för identitetsprovider) klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen och klickar på **Spara**.

    b. I textrutan **Identity Provider Issuer URL** (Utfärdare av identitetsproviderns URL) klistrar du in värdet för **Azure Ad-identifierare**, som du har kopierat från Azure-portalen och klickar på **Spara**.

    c. I Anteckningar öppnar du det base64-kodade certifikat som du laddade ned från Azure-portalen, kopierar innehållet och klistrar sedan in det i rutan för  **X.509-certifikatet**  och klickar på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** skriver **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Appraisd.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Appraisd**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Appraisd**.

    ![Länken Appraisd i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-appraisd-test-user"></a>Skapa Appraisd-testanvändare

För att Azure AD-användare ska kunna logga in på Appraisd måste de etableras till Appraisd. I Appraisd är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Appraisd som administratör.

2. Längst upp till höger på sidan klickar du på ikonen **Inställningar** och går sedan till **Administrationscenter**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. I verktygsfältet högst upp på sidan klickar du på **Personer** och går därefter till **Lägg till en ny användare**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Utför följande steg i rutan **Lägg till en ny användare**:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. I textrutan **Förnamn** fyller du i användarens förnamn, till exempel **Britta**.

    b. I textrutan **Efternamn** anger du efternamnet på användaren, till exempel **simon**.

    c. I **e-post** text, ange den e-postadressen för användaren som **Brittasimon\@contoso.com**.

    d. Klicka på **Lägg till användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Appraisd-panelen i åtkomstpanelen bör du automatiskt loggas in på Appraisd som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
