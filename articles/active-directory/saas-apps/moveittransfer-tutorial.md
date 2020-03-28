---
title: 'Självstudiekurs: Azure Active Directory-integrering med MOVEIT Transfer – Azure AD-integrering | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MOVEit Transfer - Azure AD-integrering.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 43383e82d983c998d159728997da4757cc364999
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161318"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Självstudiekurs: Azure Active Directory-integrering med MOVEIT Transfer - Azure AD-integrering

I den här självstudien får du lära dig hur du integrerar MOVEIT Transfer - Azure AD-integrering med Azure Active Directory (Azure AD).
Genom att integrera MOVEIT Transfer – Azure AD-integrering med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till MOVEit Transfer - Azure AD-integrering.
* Du kan aktivera dina användare automatiskt inloggade på MOVEit Transfer - Azure AD-integrering (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med MOVEIT Transfer - Azure AD-integrering behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* MOVEIT Transfer – Azure AD-integrering enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* MOVEit Transfer - Azure **SP** AD-integrering stöder SP-initierad SSO

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Lägga till MOVEIT Transfer - Azure AD-integrering från galleriet

Om du vill konfigurera integreringen av MOVEIT Transfer – Azure AD-integrering i Azure AD måste du lägga till MOVEIT Transfer - Azure AD-integrering från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till MOVEIT Transfer - Azure AD-integrering från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **MOVEIT Transfer - Azure AD-integrering**i sökrutan , välj **MOVEit Transfer - Azure AD-integrering** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![MOVEIT Transfer - Azure AD-integrering i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med MOVEit Transfer - Azure AD-integrering baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i MOVEit Transfer - Azure AD-integrering upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med MOVEit Transfer – Azure AD-integrering måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera MOVEit Transfer - Azure AD-integration Single Sign-On](#configure-moveit-transfer---azure-ad-integration-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa MOVEit Transfer - Azure AD-integreringstestanvändare](#create-moveit-transfer---azure-ad-integration-test-user)** - för att ha en motsvarighet till Britta Simon i MOVEit Transfer - Azure AD-integrering som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med MOVEit Transfer – Azure AD-integrering:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **MOVEIT Transfer - Azure AD-integreringsprogram** . **Single sign-on**

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

    c. När metadatafilen har överförts fylls **url-värdet identifierare** och **svar** i automatiskt i avsnittet **Grundläggande SAML-konfiguration:**

    ![MOVEIT Transfer - Azure AD-integration Domän och WEBBADRESSER enkel inloggningsinformation](common/sp-identifier-reply.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://contoso.com`

    > [!NOTE]
    > **Värdet för inloggnings-URL** är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [MOVEit Transfer - Azure AD-integrering klientsupportteam](https://community.ipswitch.com/s/support) för att få värdet. Du kan hämta **metadatafilen för tjänstprovidern** från **url:en för tjänsteleverantörens metadata** som förklaras senare i avsnittet **Konfigurera MOVEIT Transfer - Azure AD-integrering Enkel inloggning i** självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera MOVEIT Transfer - Azure AD-integrering.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>Konfigurera MOVEIT Transfer – Enkel sign-on-integrering av Azure AD-integrering

1. Logga in på din MOVEIT Transfer-klient som administratör.

2. I det vänstra navigeringsfönstret klickar du på **Inställningar**.

    ![Avsnittet Inställningar på appsidan](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Klicka på länken **Enkel inloggning,** som finns under **Säkerhetsprinciper -> Användare Auth**.

    ![Säkerhetsprinciper på appsidan](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Klicka på länken Metadata-URL för att hämta metadatadokumentet.

    ![Url till tjänstleverantörens metadata](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Verifiera **entityID-matchningar** **identifierare** i avsnittet **Grundläggande SAML-konfiguration** .
   * Verifiera **url för kontrollKonsumerService-plats** matchar **SVARS-URL:en** i avsnittet **Grundläggande SAML-konfiguration.**
    
     ![Konfigurera enkel inloggning på appsidan](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Klicka på Knappen Lägg till **identitetsprovider** om du vill lägga till en ny federerad identitetsprovider.

    ![Lägg till identitetsprovider](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Klicka på **Bläddra...** om du vill välja den metadatafil som du hämtade från Azure-portalen och klicka sedan på **Lägg till identitetsprovider** för att ladda upp den nedladdade filen.

    ![SAML-identitetsprovider](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Välj "**Ja**" som **aktiverat** på sidan **Redigera federerade identitetsprovider...** och klicka på **Spara**.

    ![Inställningar för federerad identitetsprovider](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. Utför följande åtgärder på sidan **Redigera federerade identitetsproviders användarinställningar:**
    
    ![Redigera inställningar för federerade identitetsprovider](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Välj **SAML NameID** som **inloggningsnamn**.
    
    b. Välj **Annat** som **Fullständigt namn** och i textrutan **Attributnamn** placerar du värdet: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Välj **Annat** som **e-post** och i textrutan **Attributnamn** placerar du värdet: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Välj **Ja** som **konto för automatisk återskapande på inloggning**.
    
    e. Klicka på knappen **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** skriver **du\@brittasimon yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till MOVEIT Transfer - Azure AD-integrering.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **MOVEIT Transfer - Azure AD-integrering**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **MOVEIT Transfer - Azure AD-integrering**i programlistan .

    ![MOVEIT Transfer - Azure AD-integrationslänken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Skapa MOVEIT Transfer - Azure AD-integreringstestanvändare

Syftet med det här avsnittet är att skapa en användare som heter Britta Simon i MOVEit Transfer - Azure AD-integrering. MOVEIT Transfer - Azure AD-integrering stöder just-in-time-etablering, som du har aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas under ett försök att komma åt MOVEit Transfer - Azure AD-integrering om den inte finns ännu.

>[!NOTE]
>Om du behöver skapa en användare manuellt måste du kontakta supportteamet för [MOVEit Transfer - Azure AD-integreringsklient](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen MOVEIT Transfer - Azure AD-integrering på åtkomstpanelen bör du automatiskt loggas in på MOVEIT Transfer - Azure AD-integreringen som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

