---
title: 'Självstudier: Azure Active Directory-integrering med iLMS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9bcb465f76e09675333e6e608249cba11f722e3
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274663"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Självstudier: Azure Active Directory-integrering med iLMS

I den här självstudien får du lära dig hur du integrerar iLMS med Azure Active Directory (AD Azure).
Integrera iLMS med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till iLMS.
* Du kan aktivera användarna att vara automatiskt inloggad till iLMS (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med iLMS, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* iLMS enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* har stöd för iLMS **SP och IDP** -initierad SSO

## <a name="adding-ilms-from-the-gallery"></a>Att lägga till iLMS från galleriet

För att konfigurera integrering av iLMS i Azure AD, som du behöver lägga till iLMS från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till iLMS från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **iLMS**väljer **iLMS** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![iLMS i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med iLMS baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i iLMS upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med iLMS, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera iLMS enkel inloggning](#configure-ilms-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare iLMS](#create-ilms-test-user)**  – du har en motsvarighet för Britta Simon i iLMS som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med iLMS:

1. I den [Azure-portalen](https://portal.azure.com/)på den **iLMS** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![iLMS domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I den **identifierare** textrutan, klistra in den **identifierare** värde som du kopierar från **tjänstleverantör** avsnittet för SAML-inställningar i administrationsportalen för iLMS.

    b. I den **svars-URL** textrutan, klistra in den **slutpunkt (URL)** värde som du kopierar från **tjänstleverantör** avsnittet för SAML-inställningar i iLMS admin-portalen med följande mönster `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![iLMS domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I den **inloggnings-URL** textrutan, klistra in den **slutpunkt (URL)** värde som du kopierar från **tjänstleverantör** avsnittet för SAML-inställningar i administrationsportalen för iLMS som `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

6. Om du vill aktivera JIT etablering förväntar programmets iLMS SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen **Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Du måste aktivera **skapa Un-recognized användarkonto** i iLMS att mappa dessa attribut. Följ instruktionerna [här](http://support.inspiredelearning.com/customer/portal/articles/2204526) att få en uppfattning på konfigurationen av attribut.

7. Förutom ovanstående iLMS program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut|
    | --------|------------- |
    | division | user.department |
    | region | User.state |
    | avdelning | user.jobtitle |

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

9. På den **konfigurera iLMS** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ilms-single-sign-on"></a>Konfigurera iLMS enkel inloggning

1. Logga in på i ett annat webbläsarfönster din **iLMS administrationsportalen** som administratör.

2. Klicka på **SSO:SAML** under **inställningar** flik för att öppna SAML-inställningar och utför följande steg:

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/1.png)

3. Expandera den **tjänstleverantör** avsnitt och kopiera den **identifierare** och **slutpunkt (URL)** värde.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/2.png) 

4. Under **identitetsprovidern** klickar du på **importera Metadata**.

5. Välj den **Federationsmetadata** har laddat ned från Azure-portalen från **SAML-signeringscertifikat** avsnittet.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Om du vill aktivera JIT etablering för att skapa iLMS konton för un-identifiera användarna, följer du dessa steg:

    a. Kontrollera **skapa icke erkända användarkonto**.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mappa attribut i Azure AD med attribut i iLMS. Ange namnet på attribut eller standardvärdet i attributkolumnen.

    c. Gå till **affärsregler** fliken och utför följande steg:

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/5.png)

    d. Kontrollera **skapa Un-recognized regioner, avdelningar och avdelningar** skapa regioner, avdelningar och avdelningar som inte redan finns vid tidpunkten för enkel inloggning.

    e. Kontrollera **uppdatering användaren profilen under inloggning** att ange om användarens profil uppdateras med varje enkel inloggning.

    f. Om den **uppdatering tomma värden för icke obligatoriska fält i användarprofilen** alternativet är markerat, valfritt profil fält som är tom vid inloggningen kommer även orsaka användarens iLMS profil som innehåller tomma värden för dessa fält.

    g. Kontrollera **skicka fel e-postmeddelande** och ange e-postadress för användaren där du vill ta emot fel e-postmeddelandet.

7. Klicka på knappen **Spara** för att spara inställningarna.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/save.png)

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till iLMS.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **iLMS**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **iLMS**.

    ![Länken iLMS i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ilms-test-user"></a>Skapa iLMS testanvändare

Programmet stöder bara i tid användaretablering och -autentiserade användare skapas automatiskt i programmet. JIT fungerar om du har klickat på den **skapa Un-recognized användarkonto** kryssrutan under SAML konfigurationsinställning i administrationsportalen för iLMS.

Följ stegen nedan om du vill skapa en användare manuellt:

1. Logga in på webbplatsen iLMS företag som administratör.

2. Klicka på **registrera användare** under **användare** fliken för att öppna **registrera användaren** sidan.

   ![Lägga till medarbetare](./media/ilms-tutorial/3.png)

3. På den **registrera användaren** utför följande steg.

    ![Lägga till medarbetare](./media/ilms-tutorial/create_testuser_add.png)

    a. I den **Förnamn** textrutan typnamn först som Britta.

    b. I den **efternamn** textrutan anger efternamn som Simon.

    c. I den **e-post-ID** textrutan typ e-postadressen för användaren som BrittaSimon@contoso.com.

    d. I den **Region** listrutan Välj värdet för regionen.

    e. I den **Division** listrutan Välj värdet för av.

    f. I den **avdelning** listrutan Välj värdet för avdelning.

    g. Klicka på **Spara**.

    > [!NOTE]
    > Du kan skicka e-post för registrering till användaren genom att välja **skicka e-post från registrering** kryssrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen iLMS i åtkomstpanelen, bör det vara loggas in automatiskt till iLMS som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)