---
title: 'Självstudier: Azure Active Directory-integrering med Clarizen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Clarizen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 581f0883757444a29a1d941305d055204167701a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208426"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Självstudier: Azure Active Directory-katalogintegrering med Clarizen

I den här självstudien lär du dig att integrera Clarizen med Azure Active Directory (Azure AD).
Genom att integrera Clarizen med Azure AD får du följande fördelar:

* Du kan styra vem som har åtkomst till Clarizen från Azure AD.
* Du kan konfigurera inställningar så att dina användare loggas in automatiskt i Clarizen (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Clarizen, behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Clarizen-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Clarizen stöder **IDP**-initierad enkel inloggning

## <a name="adding-clarizen-from-the-gallery"></a>Lägga till Clarizen från galleriet

För att konfigurera integrering av Clarizen i Azure AD behöver du lägga till Clarizen från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Clarizen från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Clarizen**, väljer **Clarizen** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Clarizen i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Clarizen baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Clarizen upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD för Clarizen, behöver du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Clarizen](#configure-clarizen-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Clarizen-testanvändare](#create-clarizen-test-user)** – för att ha en motsvarighet för Britta Simon i Clarizen som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Clarizen:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Clarizen** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Information om enkel inloggning med Clarizen-domäner och -URL:er](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du in ett värde: `Clarizen`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

    > [!NOTE]
    > Det här är inte de verkliga värdena. Du måste använda den faktiska identifieraren och svars-URL:en. Här föreslår vi att du använder det unika värdet för en sträng som identifierare. För att få de faktiska värdena kan du kontakta [supportteamet för Clarizen](https://success.clarizen.com/hc/en-us/requests/new).

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Clarizen** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-clarizen-single-sign-on"></a>Konfigurera enkel inloggning med Clarizen

1. Öppna ett nytt webbläsarfönster och logga in på din Clarizen-företagswebbplats som administratör.

1. Klicka på ditt användarnamn och klicka sedan på **Inställningar**.

    ![Klicka på ”Inställningar” under ditt användarnamn](./media/clarizen-tutorial/tutorial_clarizen_001.png "Inställningar")

1. Klicka på fliken **Globala inställningar**. Bredvid **Sammansluten autentisering** klickar du sedan på **Redigera**.

    ![Fliken ”Globala inställningar”](./media/clarizen-tutorial/tutorial_clarizen_002.png "Globala inställningar")

1. I dialogrutan **Sammansluten autentisering** utför du följande steg:

    ![Dialogrutan ”Sammansluten autentisering”](./media/clarizen-tutorial/tutorial_clarizen_003.png "Sammansluten autentisering")

    a. Välj **Aktivera sammansluten autentisering**.

    b. Klicka på **Ladda upp** för att ladda upp det nedladdade certifikatet.

    c. I rutan **Inloggnings-URL** anger du värdet för **inloggnings-URL** från konfigurationsfönstret för Azure AD-programmet.

    d. I rutan **Utloggnings-URL** anger du värdet för **utloggnings-URL** från konfigurationsfönstret för Azure AD-programmet.

    e. Välj **Använd POST**.

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

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Clarizen.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Clarizen**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Clarizen**.

    ![Clarizen-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-clarizen-test-user"></a>Skapa Clarizen-testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Clarizen.

**Om du behöver skapa användare manuellt så gör du följande:**

Om du vill göra det möjligt för Azure AD-användare att logga in på Clarizen måste du först etablera användarkonton. När det gäller Clarizen är etablering en manuell uppgift.

1. Logga in på din Clarizen-företagsplats som administratör.

2. Klicka på **Personer**.

    ![Klicka på ”Personer”](./media/clarizen-tutorial/create_aaduser_001.png "Personer")

3. Klicka på **Bjud in användare**.

    ![Knappen ”Bjud in användare”](./media/clarizen-tutorial/create_aaduser_002.png "Bjud in användare")

1. I dialogrutan **Bjud in personer** utför du följande steg:

    ![Dialogrutan ”Bjud in personer”](./media/clarizen-tutorial/create_aaduser_003.png "Bjud in personer")

    a. I rutan **E-post** anger du e-postadressen för Britta Simon-kontot.

    b. Klicka på **Bjud in**.

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.


### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Clarizen-panelen i åtkomstpanelen så borde du automatiskt loggas in på Clarizen som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
