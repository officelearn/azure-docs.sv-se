---
title: 'Självstudier: Azure Active Directory-integrering med LinkedIn Learning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3717ed095c162255b0c0cdf09cac13fe2033979a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60260499"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Självstudier: Azure Active Directory-integrering med LinkedIn Learning

I den här självstudien får lära du att integrera LinkedIn Learning med Azure Active Directory (Azure AD).
Att integrera LinkedIn Learning med Azure AD ger dig följande fördelar:

* Du kan via Azure AD styra vem som har åtkomst till LinkedIn Learning.
* Du kan göra så att dina användare automatiskt loggas in på LinkedIn Learning (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med LinkedIn Learning behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* LinkedIn Learning-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* LinkedIn Learning stöder **SP- och IDP**-initierad enkel inloggning
* LinkedIn Learning stöder **just-in-time**-användaretablering

## <a name="adding-linkedin-learning-from-the-gallery"></a>Lägga till LinkedIn Learning från galleriet

När du ska konfigurera integrering av LinkedIn Learning i Azure AD måste du lägga till LinkedIn Learning från galleriet till din lista över hanterade SaaS-appar.

**Lägg till LinkedIn Learning från galleriet på följande sätt:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **LinkedIn Learning** i sökrutan, välj **LinkedIn Learning** på resultatpanelen och lägg sedan till programmet genom att klicka på knappen **Lägg till**.

     ![LinkedIn Learning i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet får du konfigurera och testa Azure AD enkel inloggning med LinkedIn Learning baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation etableras mellan en Azure AD-användare och den relaterade användaren i LinkedIn Learning.

Om du vill konfigurera och testa Azure AD enkel inloggning med LinkedIn Learning måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för LinkedIn Learning](#configure-linkedin-learning-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa LinkedIn Learning-testanvändare](#create-linkedin-learning-test-user)**  – för att ha en motpart till Britta Simon i LinkedIn Learning som är länkad till användarens Azure AD-representation.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera Azure AD enkel inloggning med LinkedIn Learning genom att utföra följande steg:

1. Välj **Enkel inloggning** på programintegreringssidan för **LinkedIn Learning** i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera appen i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Information om LinkedIn Learning-domän och URL:er för enkel inloggning](common/idp-intiated.png)

    a. Ange det **entitets-ID** som kopierats från LinkedIn-portalen i textrutan **Identifierare**. 

    b. Ange den **ACS-URL** som kopierats från LinkedIn-portalen i textrutan **Svars-URL**.

    c. Om du vill konfigurera programmet i **SP-initierat** läge klickar du sedan på alternativet **Ange ytterligare URL:er** i avsnittet för den **Grundläggande SAML-konfiguration** där du ska ange din inloggnings-URL. Skapa din inloggnings-URL genom att kopiera **ACS-URL:en** och ersätta /saml/ med /login/. När du har gjort det ska inloggnings-URL:en ha följande mönster:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Information om LinkedIn Learning-domän och URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Dessa värden är inte verkliga värden. Du uppdaterar de här värdena med den faktiska identifieraren och svars-URL:en, vilket förklaras senare i kursavsnittet **Konfigurera enkel inloggning för LinkedIn Learning**.

5. LinkedIn Learning-programmet förväntar sig SAML-intyget i ett visst format, vilket innebär att du måste lägga till anpassade attributmappningar i din SAML-tokenattributskonfiguration. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. LinkedIn Learning-programmet förväntar sig att **nameidentifier** mappas med **user.mail**, så du behöver redigera attributmappningen genom att klicka på ikonen **Redigera** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

6. Utöver ovanstående förväntar sig LinkedIn Learning-programmet att några fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:
    
    | Namn | Källattribut |
    | ---------------| --------------- |
    | e-post  | user.mail  |
    | avdelning  | user.department  |
    | firstname  | user.givenname  |
    | lastname  | user.surname  |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-linkedin-learning-single-sign-on"></a>Konfigurera enkel inloggning för LinkedIn Learning

1. Logga in till din LinkedIn Learning-klient som administratör i ett annat webbläsarfönster.

2. Klicka på **Globala inställningar** under **Inställningar** i **Kontocenter**. Markera dessutom **Inlärning – standard** i den nedrullningsbara listan.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Klicka på  **ELLER klicka här om du vill läsa in och kopiera enskilda fält i formuläret** och kopiera **entitets-Id** och **ACS-URL** och klistra in dem i avsnittet  **Grundläggande SAML-konfiguration** i Azure Portal.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Gå till avsnittet **LinkedIn-administratörsinställningar**. Överför den XML-fil du laddade ned från Azure Portal genom att klicka på alternativet **Ladda upp XML-fil**.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Aktivera enkel inloggning genom att klicka på **På**. SSO-statusen ändras från **Inte ansluten** till **Ansluten**

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

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

I det här avsnittet ska göra det möjligt för Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till Zscaler Internet Access Administrator.

1. Välj **Företagsprogram**, **Alla program** och sedan **LinkedIn Learning** i Azure Portal.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Skriv och välj **LinkedIn Learning** i programlistan.

    ![LinkedIn Learning länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-linkedin-learning-test-user"></a>Skapa en testanvändare för LinkedIn Learning

LinkedIn Learning-programmet stöder just-in-time-användaretablering och efter autentiseringen skapas användare automatiskt i programmet. På LinkedIn Learning-portalens sida för administratörsinställningar ändrar du växeln **Tilldela licenser automatiskt** till aktiv just-in-time-etablering, varvid använderen även tilldelas en licens.

   ![Skapa en testanvändare för Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på LinkedIn Learning i åtkomstpanelen bör du bli automatiskt inloggad på den LinkedIn Learning som du konfigurerade enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

