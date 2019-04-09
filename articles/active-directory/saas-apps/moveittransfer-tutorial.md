---
title: 'Självstudier: Azure Active Directory-integrering med MOVEit - överföring i Azure AD-integrering | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MOVEit överföringen – Azure AD-integrering.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: d7020299bbd52f5e7ba22809847815cb04048cb6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259413"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Självstudier: Azure Active Directory-integrering med MOVEit - överföring i Azure AD-integrering

Lär dig hur du integrerar MOVEit - överföring i Azure AD-integrering med Azure Active Directory (AD Azure) i den här självstudien.
Integrera MOVEit - överföring i Azure AD-integrering med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till MOVEit - överföring i Azure AD-integrering.
* Du kan aktivera användarna att vara automatiskt inloggad MOVEit - överföring i Azure AD-integrering (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med MOVEit - överföring i Azure AD-integrering behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* MOVEit överföring - enkel inloggning för Azure AD-integrering aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för MOVEit - överföring i Azure AD-integrering **SP** -initierad SSO

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Att lägga till MOVEit - överföring i Azure AD-integrering från galleriet

Om du vill konfigurera integreringen av MOVEit - överföring i Azure AD-integrering i Azure AD som du behöver lägga till MOVEit - överföring i Azure AD-integrering från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till MOVEit - överföring i Azure AD-integrering från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **MOVEit - överföring i Azure AD-integrering**väljer **MOVEit - överföring i Azure AD-integrering** resultatet panelen klickar **Lägg till** för att lägga till den programmet.

     ![MOVEit - överföring i Azure AD-integrering i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med MOVEit - överföring i Azure AD-integrering baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning till arbete, en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i MOVEit överförings - måste Azure AD-integrering ska upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med MOVEit överföring – Azure AD-integrering, måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera MOVEit - överföring i Azure AD-integrering för enkel inloggning](#configure-moveit-transfer---azure-ad-integration-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa MOVEit - överföring i Azure AD-integrering testanvändare](#create-moveit-transfer---azure-ad-integration-test-user)**  – du har en motsvarighet för Britta Simon i MOVEit överförings - Azure AD-integrering som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med MOVEit - överföring i Azure AD-integrering:

1. I den [Azure-portalen](https://portal.azure.com/)på den **MOVEit - överföring i Azure AD-integrering** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har överförts den **identifierare** och **svars-URL** värdet hämtar automatiskt ifylld i **SAML grundkonfiguration** avsnittet:

    ![MOVEit - överföring i Azure AD-integrering domän och URL: er enkel inloggning för information](common/sp-identifier-reply.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://contoso.com`

    > [!NOTE]
    > Den **inloggnings-URL** värdet är inte verkliga. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [MOVEit - överföring i Azure AD-integrering klientstöd](https://community.ipswitch.com/s/support) -teamet för att hämta värdet. Du kan ladda ned den **metadatafil för Service Provider** från den **URL för tjänstmetadata providern** som beskrivs senare i den **konfigurera MOVEit Transfer - Azure AD-integrering enskild Inloggning** avsnittet av självstudiekursen. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera MOVEit - överföring i Azure AD-integrering** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>Konfigurera MOVEit - överföring i Azure AD-integrering för enkel inloggning

1. Logga in på din MOVEit överföring-klient som administratör.

2. I det vänstra navigeringsfönstret klickar du på **Inställningar**.

    ![Inställningar för avsnittet på App-sida](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Klicka på **enkel inloggning** länken, som finns under **säkerhetsprinciper -> användarautentisering**.

    ![Security principerna på appen på klientsidan](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Klicka på länken Metadata-URL för att hämta för Metadatadokumentet.

    ![URL för tjänstmetadata Provider](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Kontrollera **entityID** matchar **identifierare** i den **SAML grundkonfiguration** avsnittet.
   * Kontrollera **AssertionConsumerService** plats-URL: en matchar **SVARS-URL** i den **SAML grundkonfiguration** avsnittet.
    
     ![Konfigurera enkel inloggning på appsidan](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Klicka på **Lägg till identitetsprovider** för att lägga till en ny Provider för federerad identitet.

    ![Lägg till identitetsprovider](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Klicka på **Bläddra...**  för att välja metadatafilen som du laddade ned från Azure-portalen, klicka sedan på **Lägg till identitetsprovider** att ladda upp den hämta filen.

    ![SAML Identity Provider](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Välj ”**Ja**” som **aktiverat** i den **redigera federerad identitet providerinställningar...**  och klicka på **spara**.

    ![Federerad identitet providerinställningar](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. I den **redigera federerad identitet providern användarinställningar** utför följande åtgärder:
    
    ![Redigera inställningar för federerad identitet Provider](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Välj **SAML NameID** som **inloggningsnamn**.
    
    b. Välj **andra** som **fullständigt namn** och i den **attributnamnet** textrutan anger du värdet: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Välj **andra** som **e-post** och i den **attributnamnet** textrutan anger du värdet: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Välj **Ja** som **skapa automatiskt konto på inloggning**.
    
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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till MOVEit - överföring i Azure AD-integrering.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **MOVEit - överföring i Azure AD-integrering**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **MOVEit - överföring i Azure AD-integrering**.

    ![MOVEit överföringen – Azure AD-integrering länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Skapa MOVEit överföring - testanvändare i Azure AD-integrering

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i MOVEit överförings - Azure AD-integrering. MOVEit - överföring i Azure AD-integrering har stöd för just-in-time-etablering, som du har aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt MOVEit - överföring i Azure AD-integrering om det inte finns ännu.

>[!NOTE]
>Om du vill skapa en användare manuellt kan du behöva kontakta den [MOVEit överföring - supportteamet för Azure AD-integrering klienten](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på MOVEit överföringen - panelen för Azure AD-integrering i åtkomstpanelen, du bör vara loggas in automatiskt MOVEit överföringen – Azure AD-integrering som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

