---
title: 'Självstudier: Azure Active Directory-integrering med SAML SSO for Jira by resolution GmbH | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML SSO for Jira by resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: 3e527965782cc951553a5b5721955d4d3cfe67c6
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065485"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Självstudier: Azure Active Directory-integrering med SAML SSO for Jira by resolution GmbH

I den här självstudien lär du dig hur du integrerar SAML SSO for Jira by resolution GmbH med Azure Active Directory (Azure AD).
Integreringen av SAML SSO for Jira by resolution GmbH med Azure AD medför följande fördelar:

* Du kan styra vem som har åtkomst till SAML SSO for Jira by resolution GmbH från Azure AD.
* Du kan konfigurera inställningar så att dina användare loggas in automatiskt till Jira by resolution GmbH (enkel inloggning) med deras Azure Active Directory-konton (Azure AD).
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med SAML SSO for Jira by resolution GmbH behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En SAML SSO for Jira by resolution GmbH-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAML SSO for Jira by resolution GmbH stöder **SP**- och **IDP**-initierad enkel inloggning

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Lägga till SAML SSO for Jira by resolution GmbH från galleriet

För att konfigurera integreringen av SAML SSO for Jira by resolution GmbH med Azure AD måste du lägga till SAML SSO for Jira by resolution GmbH från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SAML SSO for Jira by resolution GmbH från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **SAML SSO for Jira by resolution GmbH** i sökrutan, välj **SAML SSO for Jira by resolution GmbH** från resultatpanelen och klicka på **Lägg till** för att lägga till programmet.

     ![SAML SSO for Jira by resolution GmbH i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med SAML SSO for Jira by resolution GmbH baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAML SSO for Jira by resolution GmbH upprättas.

För att konfigurera och testa enkel inloggning i Azure AD med SAML SSO for Jira by resolution GmbH måste du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för SAML SSO for Jira by resolution GmbH](#configure-saml-sso-for-jira-by-resolution-gmbh-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SAML SSO for Jira by resolution GmbH-testanvändare](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** – för att skapa en motsvarighet till Britta Simon i SAML SSO for Jira by resolution GmbH som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med SAML SSO for Jira by resolution GmbH:

1. Välj **Enkel inloggning** på sidan för programintegrering med **SAML SSO for Jira by resolution GmbH** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera appen i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Information om enkel inloggning med SAML SSO for Jira by resolution GmbH-domäner och -URL:er](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Skriv en URL med följande mönster i textrutan **Svars-URL**: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning med SAML SSO for Jira by resolution GmbH-domäner och -URL:er](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [kundsupporten för SAML SSO for Jira by resolution GmbH](https://www.resolution.de/go/support) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Set up SAML SSO for Jira by resolution GmbH** (Konfigurera SAML SSO for Jira by resolution GmbH) kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-saml-sso-for-jira-by-resolution-gmbh-single-sign-on"></a>Konfigurera enkel inloggning för SAML SSO for Jira by resolution GmbH

1. Öppna ett nytt webbläsarfönster och logga in på **administrationsportalen för SAML SSO for Jira by resolution GmbH** som administratör.

2. Hovra över kugghjulet och klicka på **Tillägg**.
    
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon1.png)

3. Du omdirigeras till sidan för administratörsåtkomst. Ange **lösenordet** och klicka på **Bekräfta**.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon2.png)

4. I avsnittet Tillägg klickar du på **Hitta nya tillägg**. Sök efter **SAML Single Sign On (SSO) for JIRA** och klicka på **Installera** för att installera det nya SAML-plugin-programmet.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon7.png)

5. Installationen av plugin-programmet startar. Klicka på **Stäng**.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon8.png)

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon9.png)

6.  Klicka på **Hantera**.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon10.png)
    
8. Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon11.png)

9. Klicka på **Add new IdP** (Lägg till ny IdP) på **konfigurationssidan för plugin-programmet SAML SingleSignOn** för att konfigurera inställningarna för identitetsprovidern.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon4.png)

10. Utför följande steg på sidan **Choose your SAML Identity Provider** (Välj din SAML-identitetsprovider):

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon5a.png)
 
    a. Ange **Azure AD** som IdP-typ.
    
    b. Lägg till ett **namn** för identitetsprovidern (t.ex. Azure AD).
    
    c. Lägg till en **beskrivning** av identitetsprovidern (t.ex. Azure AD).
    
    d. Klicka på **Nästa**.
    
11. Klicka på knappen **Nästa** på **konfigurationssidan för identitetsprovidern**.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon5b.png)

12. Utför följande steg på sidan **Import SAML IdP Metadata** (Importera SAML IdP-metadata):

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon5c.png)

    a. Klicka på **Läs in fil** och välj XML-filen med metadata som du hämtade i steg 5.

    b. Klicka på **Importera**.
    
    c. Vänta en kort stund tills importen slutförts.
    
    d. Klicka på **Nästa**.
    
13. Klicka på **Nästa** på sidan **User ID attribute and transformation** (Attribut för användar-ID och transformering).

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon5d.png)
    
14. Spara inställningarna genom att klicka på **Spara och Nästa** på sidan för **användargenerering och uppdatering**.   
    
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon6a.png)
    
15. Hoppa över användartestningen så länge genom att klicka på **Skip test & configure manually** (Hoppa över test och konfigurera manuellt) på sidan **Test your settings** (Testa dina inställningar). Du ska köra testningen i nästa avsnitt, vilket kräver ytterligare några inställningar på Azure-portalen. 
    
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon6b.png)
    
16. Klicka på **OK** i dialogrutan med texten **Skipping the test means...** (Om du hoppar över testet ...).
    
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon6c.png)

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

I det här avsnittet ska du göra det möjligt för Britta Simon att använda enkel inloggning i Azure genom att ge åtkomst till SAML SSO for Jira by resolution GmbH.

1. Välj **Företagsprogram** på Azure-portalen, välj **Alla program** och välj sedan **SAML SSO for Jira by resolution GmbH**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Skriv och välj **SAML SSO for Jira by resolution GmbH** i programlistan.

    ![SAML SSO for Jira by resolution GmbH-länken i listan över program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Skapa SAML SSO for Jira by resolution GmbH-testanvändare

För att Azure AD-användare ska kunna logga in i SAML SSO for Jira by resolution GmbH måste de vara etablerade i SAML SSO for Jira by resolution GmbH.  
I SAML SSO for Jira by resolution GmbH är etableringen en manuell åtgärd.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din SAML SSO for Jira by resolution GmbH-företagswebbplats som administratör.

2. Hovra över kugghjulet och klicka på **Användarhantering**.

    ![Lägga till medarbetare](./media/samlssojira-tutorial/user1.png) 

3. Du omdirigeras till administratörsåtkomstsidan där du kan ange **lösenord** och klicka på knappen **Bekräfta**.

    ![Lägga till medarbetare](./media/samlssojira-tutorial/user2.png) 

4. På avsnittsfliken **Användarhantering** klickar du på **skapa användare**.

    ![Lägga till medarbetare](./media/samlssojira-tutorial/user3.png) 

5. I dialogrutan **”Skapa ny användare”** utför du följande steg:

    ![Lägga till medarbetare](./media/samlssojira-tutorial/user4.png) 

    a. I textrutan för **e-postadress** skriver du användarens e-postadress som Brittasimon@contoso.com.

    b. Skriv det fullständiga namnet för användaren, t.ex. Britta Simon, i textrutan **Fullständigt namn**.

    c. Skriv e-postadressen för användaren i textrutan **Användarnamn** som Brittasimon@contoso.com.

    d. I textrutan **Password** (Lösenord) skriver du lösenordet för användaren.

    e. Klicka på **Skapa användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för SAML SSO for Jira by resolution GmbH på åtkomstpanelen bör du loggas in automatiskt i SAML SSO for Jira by resolution GmbH-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

