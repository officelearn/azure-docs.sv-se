---
title: 'Självstudier: Azure Active Directory-integrering med Cisco Webex | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.openlocfilehash: 61ea0874f76fb054ddb1ae23d42a7161bea2f96b
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064329"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Självstudier: Azure Active Directory-integrering med Cisco Webex

Lär dig hur du integrerar Cisco Webex med Azure Active Directory (AD Azure) i den här självstudien.
När du integrerar Cisco Webex med Azure AD får du följande fördelar:

* I Azure AD kan du styra vem som har åtkomst till Cisco Webex.
* Du kan göra så att dina användare automatiskt loggas in på Cisco Webex (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Cisco Webex behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Cisco Webex-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Cisco Webex har stöd för **SP**-initierad enkel inloggning

* Cisco Webex stöder **just-in-time**-användaretablering

* Cisco Webex stöder **automatisk** användaretablering

## <a name="adding-cisco-webex-from-the-gallery"></a>Lägga till Cisco Webex från galleriet

För att konfigurera integrering av Cisco Webex i Azure AD måste du lägga till Cisco Webex från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Cisco Webex från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Cisco Webex**, väljer **Cisco Webex** på resultatpanelen och klickar på **Lägg till** för att lägga till programmet.

     ![Cisco Webex i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Cisco Webex baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Cisco Webex upprättas.

Konfigurera och testa enkel inloggning med Azure AD med Cisco Webex genom att slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Cisco Webex](#configure-cisco-webex-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Cisco Webex-testanvändare](#create-cisco-webex-test-user)** – för att ha en motsvarighet till Britta Simon i Cisco Webex som är länkad till Azure AD-representationen för användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med Cisco Webex:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Cisco Webex**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. I ett annat webbläsarfönster loggar du in på företagswebbplatsen Cisco Webex som administratör.

4. Klicka på **Settings** (Inställningar) på menyn till vänster.

    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial_cisco_webex_10.png)

5. På inställningssidan rullar du nedåt och klickar på **Modify** (Ändra) under **Authentication** (Autentisering).

    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial_cisco_webex_14.png)

6. Välj **Integrate a 3rd-party identity provider. (Avancerat)** (Integrera en tredjeparts identitetsprovider. (Avancerat)) och gå till nästa skärm.

    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial_cisco_webex_15.png)

7. På sidan **Export Directory Metadata** (Exportera katalog för metadata) klickar du på **Download Metadata File** (Ladda ned metadatafil) för att ladda ned metadatafilen.

    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial_cisco_webex_16.png)

8. På Azure-portalen går du till sidan **Konfigurera enkel inloggning med SAML** och klickar på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

9. I avsnittet **Grundläggande SAML-konfiguration** laddar du upp den nedladdade filen med **tjänstleverantörmetadata** och konfigurerar programmet med följande steg:

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen för tjänstleverantör har laddats upp fylls värdena för **Identifierare** och **Svars-URL** i automatiskt i avsnittet **Grundläggande SAML-konfiguration**:

    ![Information om enkel inloggning med Cisco Webex-domänen och URL:er](common/sp-identifier-reply.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<SUBDOMAIN>.webex.com/`

    > [!Note]
    > Värdet för inloggnings-URL är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Cisco Webex-supporten](https://www.webex.co.in/support/support-overview.html) och be om det här värdet.

10. Cisco Webex-programmet förväntar ett visst format för SAML-försäkran. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

11. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:
    
    | Namn | Källattribut|
    | ---------------| --------------- | --------- |
    |   förnamn    | user.givenname |
    |   lastname    | user.surname |
    |   uid    | user.mail |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

12. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

13. I avsnittet **om att konfigurera Cisco Webex** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-cisco-webex-single-sign-on"></a>Konfigurera enkel inloggning för Cisco Webex

1. Använd filbläddraren på administratörssidan på Cisco Webex-företagswebbplatsen för att hitta och ladda upp Azure AD-metadatafilen. Välj sedan **Require certificate signed by a certificate authority in Metadata (more secure)** (Kräv signerat certifikat av certifikatutfärdare i metadata (säkrare)) och fortsätt till nästa skärm. 

    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial_cisco_webex_11.png)

2. Välj **Test SSO Connection** (Testa den enkla inloggningen) och autentisera med Azure AD-inloggning när en ny webbläsarflik öppnas.

3. Gå tillbaka till webbläsarfliken **Cisco Cloud Collaboration Management** (Ciscos samarbetshantering i molnet). Om testet lyckades väljer du **This test was successful. Enable Single Sign-On option** (Testet lyckades. Aktivera alternativet för enkel inloggning) och klicka på **Save** (Spara).

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

I det här avsnittet ska du göra det möjligt för Britta Simon att använda enkel inloggning i Azure genom att bevilja åtkomst till Cisco Webex.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Cisco Webex**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Cisco Webex** i listan över program.

    ![Cisco Webex-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-cisco-webex-test-user"></a>Skapa en testanvändare i Cisco Webex

I det här avsnittet ska du skapa en användare med namnet Britta Simon i Cisco Webex. Cisco Webex stöder just-in-time-etablering och automatisk användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Cisco Webex skapas en ny när du försöker komma åt Cisco Webex.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Cisco Webex-panelen i åtkomstpanelen bör du automatiskt loggas in på Cisco Webex som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

