---
title: 'Självstudier: Azure Active Directory-integrering med Boomi | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8092724a8ebf04342e00d054cc78fe26b48d2f82
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842710"
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Självstudier: Azure Active Directory-integrering med Boomi

I den här självstudien lär du dig att integrera Boomi med Azure Active Directory (AD Azure).
Genom att integrera Boomi med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Boomi.
* Du kan göra så att dina användare automatiskt loggas in på Boomi (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Boomi behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Boomi-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Boomi stöder **IDP**-initierad enkel inloggning

## <a name="adding-boomi-from-the-gallery"></a>Lägga till Boomi från galleriet

För att konfigurera integreringen av Boomi till Azure AD behöver du lägga till Boomi från galleriet till listan över hanterade SaaS-appar.

**Lägg till Boomi från galleriet genom att utföra följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Boomi**, väljer **Boomi** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Boomi i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med Boomi baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Boomi upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Boomi behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Boomi](#configure-boomi-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Boomi-testanvändare](#create-boomi-test-user)** – för att ha en motsvarighet för Britta Simon i Boomi som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Boomi:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Boomi**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Boomi-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du in en URL: `https://platform.boomi.com/`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Hämta det här värdet genom att kontakta [supportteamet för Boomi-klienten](https://boomi.com/company/contact/). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Boomi-programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Namn |  Källattribut|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

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

8. I avsnittet **Konfigurera Boomi** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-boomi-single-sign-on"></a>Konfigurera enkel inloggning för Boomi

1. I ett annat webbläsarfönster loggar du in på din Boomi-företagsplats som administratör. 

2. Gå till **Företagsnamn** och sedan till **Konfigurera**.

3. Klicka på fliken **SSO Options** (SSO-alternativ) och utför stegen nedan.

    ![Konfigurera enkel inloggning på appsidan](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Markera kryssrutan **Enable SAML Single Sign-On** (Aktivera enkel inloggning med SAML).

    b. Klicka på **Importera** för att ladda upp det nedladdade certifikatet från Azure AD till **Certifikat för identitetsprovider**.

    c. I textrutan **Inloggnings-URL för identitetsprovider** anger du värdet för **inloggnings-URL** från konfigurationsfönstret för Azure AD-programmet.

    d. För **Federation Id Location** (Plats för federations-ID) markerar du alternativknappen **Federation Id is in FEDERATION_ID Attribute element** (Federations-ID finns i attributelementet FEDERATION_ID).

    e. Klicka på knappen **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Boomi.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Boomi**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Boomi**.

    ![Boomi-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-boomi-test-user"></a>Skapa Boomi-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på Boomi måste de etableras i Boomi. När det gäller Boomi är etablering en manuell aktivitet.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Gör följande för att etablera ett användarkonto:

1. Logga in på din Boomi-företagsplats som administratör.

2. När du loggat in går du till **Användarhantering** och sedan till **Användare**.

    ![Användare](./media/boomi-tutorial/tutorial_boomi_001.png "Användare")

3. Klicka på ikonen **+** så öppnas dialogrutan **Add/Maintain User Roles** (Lägg till/underhåll användarroller).

    ![Användare](./media/boomi-tutorial/tutorial_boomi_002.png "Användare")

    ![Användare](./media/boomi-tutorial/tutorial_boomi_003.png "Användare")

    a. I textrutan **User e-mail address** (E-post för användare) skriver du användarens e-postadress som BrittaSimon@contoso.com.

    b. I textrutan **Förnamn** skriver du förnamnet på användaren: Britta.

    c. I textrutan **Efternamn** skriver du efternamnet: Simon.

    d. Ange användarens **Federations-ID**. Varje användare ha ett Federation-ID som unikt identifierar användare i kontot.

    e. Tilldela rollen **Standardanvändare** till användaren. Tilldela inte rollen Administratör, eftersom som skulle ge användaren normal Atmosphere-åtkomst samt åtkomst via enkel inloggning.

    f. Klicka på **OK**.

    > [!NOTE]
    > Användaren får inte något e-postvälkomstmeddelande med ett lösenord som kan användas för att logga in på AtomSphere-kontot eftersom lösenordet hanteras via identitetsprovidern. Du kan använda andra verktyg eller API:er för Boomi-kontoskapande som tillhandahålls av Boomi för att etablera AAD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Boomi-panelen i åtkomstpanelen bör du automatiskt loggas in på Boomi som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

