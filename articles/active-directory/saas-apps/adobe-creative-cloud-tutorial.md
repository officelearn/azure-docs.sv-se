---
title: 'Självstudier: Azure Active Directory-integrering med Adobe Creative Cloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35bd52904ab081e41cb43a346288234c18a7f43b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60285118"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Självstudier: Azure Active Directory-integrering med Adobe Creative Cloud

I den här självstudiekursen lär du dig hur du integrerar Adobe Creative Cloud med Azure Active Directory (Azure AD).
När du integrerar Adobe Creative Cloud med Azure Active Directory innebär det följande fördelar:

* Du kan styra i Azure Active Directory vilka som har åtkomst till Adobe Creative Cloud.
* Du kan göra så att dina användare automatiskt loggas in på Adobe Creative Cloud (enkel inloggning) med sina Azure Active Directory-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure Active Directory-integrering med Adobe Creative Cloud, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Prenumeration med enkel inloggning med Adobe Creative Cloud aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Adobe Creative Cloud stöder **SP**-initierad enkel inloggning

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Lägga till Adobe Creative Cloud från galleriet

Om du vill konfigurera integreringen av Adobe Creative Cloud i Azure Active Directory måste du lägga till Adobe Creative Cloud från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Adobe Creative Cloud från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Adobe Creative Cloud** i sökrutan, välj **Adobe Creative Cloud** på resultatpanelen och lägg sedan till programmet genom att klicka på knappen **Lägg till**.

    ![Adobe Creative Cloud i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet får du konfigurera och testa enkel inloggning i Azure Active Directory med Adobe Creative Cloud baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure Active Directory-användare och den relaterade användaren i Adobe Creative Cloud upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure Active Directory med Adobe Creative Cloud måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Adobe Creative Cloud](#configure-adobe-creative-cloud-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en testanvändare för Adobe Creative Cloud](#create-adobe-creative-cloud-test-user)** – så att du får en motsvarighet till Britta Simon i Adobe Creative Cloud som är länkad till användarens Azure Active Directory-representation.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning till Azure Active Directory med Adobe Creative Cloud:

1. Välj **Enkel inloggning** på programintegreringssidan för **Adobe Creative Cloud** i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Adobe Creative Cloud-domän och URL:er](common/sp-identifier.png)

    a. I rutan **Inloggnings-URL** anger du värdet som: `https://adobe.com`.

    b. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Identifierarvärdet är inte verkligt. Uppdatera det här värdet med den faktiska identifieraren. Kontakta [kundsupporten för Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html) och be om det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Adobe Creative Cloud-program förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Namn | Källattribut|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-post | user.mail

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

    > [!NOTE]
    > Användare måste ha en giltig Office 365 ExO-licens för att anspråksvärden för e-post ska anges i SAML-svaret.

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

8. I avsnittet **Konfigurera Adobe Creative Cloud** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>Konfigurera enkel inloggning med Adobe Creative Cloud

1. I ett annat webbläsarfönster, loggar du in på [Adobe-administratörskonsolen](https://adminconsole.adobe.com) som administratör.

2. Gå till **Inställningar** i det övre navigeringsfältet och välj sedan **Identitet**. Listan över domäner öppnas. Klicka på länken **Konfigurera** mot din domän. Utför sedan följande steg i avsnittet **Konfiguration av enkel inloggning krävs**. Mer information finns i [Installera en domän](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Inställningar](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Inställningar")

    a. Klicka på **Bläddra** för att ladda upp det nedladdade certifikatet från Azure Active Directory till **IDP-certifikat**.

    b. I textrutan **IDP-utfärdare** anger du det värde för **SAML-entitets-Id** som du kopierade från avsnittet **Konfigurera inloggning** i Azure Portal.

    c. I textrutan **URL för IDP-inloggning** anger du det värde för **URL för SAML SSO-tjänsten** som du kopierade från avsnittet **Konfigurera inloggning** i Azure Portal.

    d. Välj **HTTP – omdirigera** som **IDP-bindning**.

    e. Välj **E-postadress** som **Inloggningsinställning för användare**.

    f. Klicka på knappen **Spara**.

3. Instrumentpanelen kommer nu att presentera XML-filen **”Ladda ner metadata”**. Den innehåller webbadressen för Adobes EntityDescriptor och för AssertionConsumerService. Öppna filen och konfigurera den i Azure Active Directory-programmet.

    ![Konfigurera enkel inloggning på appsidan](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Använd det EntityDescriptor-värde som Adobe försett dig med som **Identifierare** i dialogrutan **Konfigurera appinställningar**.

    b. Använd det AssertionConsumerService-värde Adobe försett dig med som **Svars-URL** i dialogrutan **Konfigurera appinställningar**.

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

I det här avsnittet får du aktivera Britta Simon till att använda enkel inloggning i Azure genom att bevilja henne åtkomst till Adobe Creative Cloud.

1. I Azure Portal väljer du **Företagsprogram**, väljer **Alla program** och sedan **Adobe Creative Cloud**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Adobe Creative Cloud**.

    ![Adobe Creative Cloud-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-adobe-creative-cloud-test-user"></a>Skapa testanvändare för Adobe Creative Cloud

För att göra det möjligt för Azure Active Directory-användare att logga in på Adobe Creative Cloud, måste de etableras i Adobe Creative Cloud. När det gäller Adobe Creative Cloud är etablering en manuell aktivitet.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Utför följande steg för att tillhandahålla ett användarkonto:

1. Logga in på [Adobe-administratörskonsolen](https://adminconsole.adobe.com) som administratör.

2. Lägg till användaren i Adobe-konsolen som externt ID och tilldela dem till en produktprofil. Detaljerad information om att lägga till användare finns i [Lägga till användare i Adobe-administratörskonsolen](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. Ange nu din e-post/upn i Adobe-inloggningsformuläret genom att trycka på fliken, så bör du sammanslutas tillbaka till Azure Active Directory:
   * Webbåtkomst: www\.adobe.com > Logga in
   * I verktyget skrivbordsapp > Logga in
   * I programmet > Hjälp > Logga in

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Adobe Creative Cloud-panelen i åtkomstpanelen bör du automatiskt loggas in till Adobe Creative Cloud som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [Konfigurera en domän (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Konfigurera Azure för användning med Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)
