---
title: 'Självstudier: Azure Active Directory-integrering med LinkedIn höjer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LinkedIn höjer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49e5b6f52b92889ccc7c46f091ea1b90d43b0307
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704801"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Självstudier: Azure Active Directory-integrering med LinkedIn höjer

I den här självstudien får lära du att integrera LinkedIn höjer med Azure Active Directory (AD Azure).
Integrera LinkedIn höjer med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till LinkedIn höjer.
* Du kan aktivera användarna att vara automatiskt inloggad till LinkedIn höjer (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med LinkedIn höjer, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* LinkedIn höjer enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för LinkedIn höjer **SP och IDP** -initierad SSO

* Har stöd för LinkedIn höjer **Just In Time** etableringen av användare

* Har stöd för LinkedIn höjer [ **automatisk** etableringen av användare](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Att lägga till LinkedIn höjer från galleriet

Om du vill konfigurera integreringen av LinkedIn utöka till Azure AD, som du behöver lägga till LinkedIn höjer från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till LinkedIn höjer från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **LinkedIn höjer**väljer **LinkedIn höjer** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![LinkedIn höjer i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med LinkedIn höjer baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i LinkedIn höjer upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med LinkedIn höjer, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera LinkedIn höjer enkel inloggning](#configure-linkedin-elevate-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa LinkedIn höjer testanvändare](#create-linkedin-elevate-test-user)**  – du har en motsvarighet för Britta Simon i LinkedIn höjer som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med LinkedIn höjer:

1. I den [Azure-portalen](https://portal.azure.com/)på den **LinkedIn höjer** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![LinkedIn höjer domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I den **identifierare** text, ange den **entitets-ID** värde, ska du kopiera entitets-ID-värdet från Linkedin-Portal som beskrivs senare i den här självstudien.

    b. I den **svars-URL** text, ange den **Assertion konsument Access (ACS) Url** värde, du kommer att kopiera Assertion konsument Access (ACS) Url-värdet från portalen Linkedin beskrivs senare i den här självstudien.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![LinkedIn höjer domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

6. LinkedIn utöka programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar en lista över standardattribut, var som **nameidentifier** mappas med **user.userprincipalname**. LinkedIn höjer program som förväntar nameidentifier mappas med **user.mail**, så du behöver redigera attribut mappar genom att klicka på redigeringsikonen och ändra attribut mappar.

    ![image](common/edit-attribute.png)

7. Förutom ovanstående LinkedIn höjer program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet användaranspråk på den **användarattribut** dialogrutan utför följande steg för att lägga till SAML-token attributet som visas i den tabellen nedan:

    | Namn | Källattribut|
    | -------| -------------|
    | avdelning | user.department |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

8. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

9. På den **konfigurera LinkedIn höjer** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-linkedin-elevate-single-sign-on"></a>Konfigurera LinkedIn höjer enkel inloggning

1. I ett annat webbläsarfönster inloggning till LinkedIn höjer-klienten som administratör.

1. Klicka på **Globala inställningar** under **Inställningar** i **Kontocenter**. Markera också **utöka - höjer AAD Test** från den nedrullningsbara listan.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Klicka på **eller klicka här om du vill läsa in och kopiera enskilda fält i formuläret** och utför följande steg:

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Kopiera **entitets-Id** och klistra in den i den **identifierare** textrutan den **SAML grundkonfiguration** i Azure-portalen.

    b. Kopiera **Assertion konsument Access (ACS) Url** och klistra in den i den **svars-URL** textrutan den **grundläggande SAML-konfiguration** i Azure-portalen.

1. Gå till avsnittet **LinkedIn-administratörsinställningar**. Ladda upp XML-filen som du har hämtat från Azure portal genom att klicka på alternativet ladda upp XML-fil.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Aktivera enkel inloggning genom att klicka på **På**. SSO status kommer att ändras från **inte ansluten** till **ansluten**

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LinkedIn höjer.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **LinkedIn höjer**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **LinkedIn höjer**.

    ![Länken LinkedIn höjer i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-linkedin-elevate-test-user"></a>Skapa LinkedIn höjer testanvändare

LinkedIn utöka programmet stöder bara i tid användaretablering och -autentiserade användare kommer att skapas i programmet automatiskt. På administratören sidan Inställningar på LinkedIn höjer portal andra växeln **automatiskt tilldela licenser** till aktiva Just-in-time-etablering och detta kommer också tilldela en licens till användaren. LinkedIn höjer stöder även automatisk användaretablering, kan du hitta mer information om [här](linkedinelevate-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

   ![Skapa en Azure AD-användare för testning](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen LinkedIn höjer i åtkomstpanelen, bör det vara loggas in automatiskt att upphöja LinkedIn som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)