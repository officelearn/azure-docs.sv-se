---
title: 'Självstudier: Azure Active Directory integrering med LinkedIn-höjning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LinkedIn-höjning.
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
ms.openlocfilehash: 5b7cb8d6ab34a632e36ea2fd1c87005a038bc523
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823711"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Självstudier: Azure Active Directory integrering med LinkedIn-höjning

I den här självstudien får du lära dig hur du integrerar LinkedIn-höjning med Azure Active Directory (Azure AD).
Att integrera LinkedIn-höjning med Azure AD ger följande fördelar:

* Du kan styra i Azure AD som har åtkomst till LinkedIn-höjning.
* Du kan göra det möjligt för användarna att logga in automatiskt till LinkedIn-höjning (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med LinkedIn-höjning behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* LinkedIn-höjning av enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* LinkedIn-höjning stöder **SP-och IDP** -INITIERAd SSO

* LinkedIn-höjning stöder **just-in-Time** User-etablering

* LinkedIn-höjning stöder [ **Automatisk** användar etablering](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Det går inte att lägga till LinkedIn-höjning från galleriet

Om du vill konfigurera integrering av LinkedIn-höjning i Azure AD måste du lägga till LinkedIn-höjning från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till LinkedIn-höjning från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **LinkedIn-höjning**, väljer **LinkedIn Höj** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![LinkedIn-höjning i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med LinkedIn-höjning baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i LinkedIn-höjning.

Om du vill konfigurera och testa enkel inloggning med LinkedIn-behörighet i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera LinkedIn-höjning med enkel inloggning](#configure-linkedin-elevate-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa LinkedIn-Höj test användare](#create-linkedin-elevate-test-user)** – för att få en motsvarighet till Britta Simon i LinkedIn-höjning som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med LinkedIn-höjning:

1. Välj **enkel inloggning**på sidan **LinkedIn Höj** program integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![LinkedIn Höj behörighet för domän-och webb adresser information om enkel inloggning](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du ENTITETS **-ID** -värdet. du kopierar entitets-ID-värde från LinkedIn-portalen som beskrivs senare i den här självstudien.

    b. I text rutan svars- **URL** anger du URL-värdet för **intyg om konsument åtkomst (ACS)** , du kommer att kopiera URL-värdet för intyg om intygs åtkomst (ACS) från LinkedIn-portalen som beskrivs senare i den här självstudien.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![LinkedIn Höj behörighet för domän-och webb adresser information om enkel inloggning](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

6. LinkedIn-höjning förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. LinkedIn-höjning förväntar sig att NameIdentifier mappas med **User. mail**, så du måste redigera mappningen av attribut genom att klicka på ikonen Redigera och ändra attributet mappning.

    ![image](common/edit-attribute.png)

7. Utöver ovan förväntar LinkedIn-programmet att fler attribut skickas tillbaka i SAML-svar. I avsnittet användar anspråk i dialog rutan användarattribut, utför följande steg för att lägga till SAML-token-attributet som visas i tabellen nedan:

    | Namn | Källattribut|
    | -------| -------------|
    | Avdelning | user.department |

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

    ![Länk för hämtning av certifikat](common/metadataxml.png)

9. I avsnittet **Konfigurera LinkedIn-höjning** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-linkedin-elevate-single-sign-on"></a>Konfigurera LinkedIn-höjning av enkel inloggning

1. Logga in på din LinkedIn-klient som administratör i ett annat webbläsarfönster.

1. Klicka på **Globala inställningar** under **Inställningar** i **Kontocenter**. Välj också alternativet **öka och Höj behörighet för AAD-test** i list rutan.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Klicka på **eller klicka här för att läsa in och kopiera enskilda fält från formuläret** och utför följande steg:

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Kopiera **entitets-ID** och klistra in det i text rutan **identifierare** i den **grundläggande SAML-konfigurationen** i Azure Portal.

    b. Kopiera **intyg om kontroll konsument åtkomst (ACS) URL** och klistra in den i text rutan svars- **URL** i den **grundläggande SAML-konfigurationen** i Azure Portal.

1. Gå till avsnittet **LinkedIn-administratörsinställningar**. Ladda upp XML-filen som du har laddat ned från Azure Portal genom att klicka på alternativet överför XML-fil.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Aktivera enkel inloggning genom att klicka på **På**. SSO-status ändras från **inte ansluten** till **ansluten**

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
  
    b. I `brittasimon@yourcompanydomain.extension`fältet **användar namn** . Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till LinkedIn-höjning.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer LinkedIn- **höjning**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **LinkedIn-höjning**.

    ![Länken LinkedIn Höj nivå i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-linkedin-elevate-test-user"></a>Skapa LinkedIn-höjning av test användare

LinkedIn-höjning-applikation stöder just-in-Time-etablering och efter att användare av autentisering skapas i programmet automatiskt. På sidan administratörs inställningar på portalen på LinkedIn-höjning vänder du växeln **automatiskt till att tilldela licenser** till aktiv just-in-Time-etablering. då tilldelas även en licens till användaren. LinkedIn-höjning har även stöd för automatisk användar etablering. du hittar mer information [här](linkedinelevate-provisioning-tutorial.md) om hur du konfigurerar automatisk användar etablering.

   ![Skapa en Azure AD-användare för testning](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen LinkedIn-höjning i åtkomst panelen, bör du loggas in automatiskt på den LinkedIn-höjning som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)