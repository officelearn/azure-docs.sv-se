---
title: 'Självstudier: Azure Active Directory-integrering med OpenAthens | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3eca6fc3ab788ee7085c0df5f6c9770858af29ba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57847356"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Självstudier: Azure Active Directory-integrering med OpenAthens

I den här självstudien lär du dig att integrera OpenAthens med Azure Active Directory (Azure AD).
Genom att integrera OpenAthens med Azure AD får du följande fördelar:

* Du kan styra vem som har åtkomst till OpenAthens från Azure AD.
* Du kan göra så att dina användare automatiskt loggas in på OpenAthens (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med OpenAthens behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* OpenAthens-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* OpenAthens stöder **IDP**-initierad enkel inloggning

* OpenAthens stöder **just-in-time**-användaretablering

## <a name="adding-openathens-from-the-gallery"></a>Lägga till OpenAthens från galleriet

För att konfigurera integrering av OpenAthens i Azure AD behöver du lägga till OpenAthens från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till OpenAthens från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **OpenAthens**, väljer **OpenAthens** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![OpenAthens i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med OpenAthens baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i OpenAthens upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med OpenAthens, behöver du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för OpenAthens](#configure-openathens-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa OpenAthens-testanvändare](#create-openathens-test-user)** – för att ha en motsvarighet för Britta Simon i OpenAthens som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med OpenAthens:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering för **OpenAthens** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

5. I avsnittet **Grundläggande SAML-konfiguration** laddar du upp **metadatafilen för tjänstleverantör**. Hur du gör det beskrivs senare i den här självstudien.

    a. Klicka på **Ladda upp metadatafil**.

    ![openathens upload metadata](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![Openathens browse upload metadata](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp fylls **identifierarvärdet** i automatiskt i textrutan i avsnittet **Grundläggande SAML-konfiguration**:

    ![Information om enkel inloggning med OpenAthens-domäner och URL:er](common/idp-identifier.png)

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-openathens-single-sign-on"></a>Konfigurera enkel inloggning med OpenAthens

1. Logga in på företagswebbplatsen för OpenAthens som administratör i ett annat webbläsarfönster.

2. Välj **Anslutningar** i listan på fliken **Hantering**. 

    ![Konfigurera enkel inloggning](./media/openathens-tutorial/tutorial_openathens_application1.png)

3. Välj **SAML 1.1/2.0**, och klicka sedan på knappen **Konfigurera**.

    ![Konfigurera enkel inloggning](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
4. Om du vill lägga till konfigurationen väljer du knappen **Bläddra** för att överföra .xml-metadatafilen som du laddade ned från Azure-portalen, och väljer sedan **Lägg till**.

    ![Konfigurera enkel inloggning](./media/openathens-tutorial/tutorial_openathens_application3.png)

5. Utför följande steg under fliken **Information**.

    ![Konfigurera enkel inloggning](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. I **Mappning för visningsnamn** väljer du **Använd attribut**.

    b. I textrutan **Attribut för visningsnamn** fyller du i värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. I **Unik användarmappning** väljer du **Använd attribut**.

    d. I textrutan **Unikt användarattribut** fyller du i värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. Markera alla tre kryssruta i **Status**.

    f. I **Skapa lokala konton** väljer du **automatiskt**.

    g. Välj **Spara ändringar**.

    h. Från fliken **</> Förlitande part** ska du nu kopiera **Metadata-URL:en** och öppna den i webbläsaren för att ladda ned **XML-filen för SP-metadata**. Ladda upp SP-metadatafilen på avsnittet **Grundläggande SAML-konfiguration** i Azure AD.

    ![Konfigurera enkel inloggning](./media/openathens-tutorial/tutorial_openathens_application5.png)

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till OpenAthens.

1. Välj **Företagsprogram** i Azure-portalen, välj **Alla program**, och välj sedan **OpenAthens**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **OpenAthens**.

    ![OpenAthens-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-openathens-test-user"></a>Skapa OpenAthens-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i OpenAthens. OpenAthens stöder **just-in-time-etablering av användare**, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i OpenAthens skapas en ny efter autentisering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på OpenAthens-panelen i åtkomstpanelen bör du automatiskt loggas in på OpenAthens som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

