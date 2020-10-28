---
title: 'Självstudie: Azure Active Directory integration med SolarWinds Service Desk (tidigare Samanage) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SolarWinds Service Desk (tidigare Samanage).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: ab720430af0341f3a42d9f4d4dc19b9469872211
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675605"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Självstudie: Azure Active Directory integration med SolarWinds Service Desk (tidigare Samanage)

I den här självstudien får du lära dig hur du integrerar SolarWinds med Azure Active Directory (Azure AD).
Genom att integrera SolarWinds med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SolarWinds.
* Du kan göra det möjligt för användarna att logga in automatiskt till SolarWinds (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SolarWinds Service Desk (tidigare Samanage) behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Samanage-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SolarWinds stöder **SP** -INITIERAd SSO

## <a name="adding-solarwinds-from-the-gallery"></a>Lägga till SolarWinds från galleriet

Om du vill konfigurera integreringen av SolarWinds i Azure AD måste du lägga till SolarWinds från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till SolarWinds från galleriet:**

1. Välj **Azure Active Directory** ikon i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program** .

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **SolarWinds** , väljer **SolarWinds** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![SolarWinds i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med SolarWinds baserat på en test användare som kallas **Britta Simon** .
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i SolarWinds upprättas.

Om du vill konfigurera och testa enkel inloggning med SolarWinds i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SolarWinds Service Desk enkel inloggning](#configure-solarwinds-single-sign-on)** -för att konfigurera de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SolarWinds Service Desk test User](#create-solarwinds-test-user)** – för att få en motsvarighet till Britta Simon i SolarWinds Service Desk som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med SolarWinds i Azure AD:

1. Välj **enkel inloggning** på sidan **SolarWinds** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Samanage-domän och information om URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Detta förklaras senare i självstudien. Kontakta [supportteamet för Samanage-klienten](https://www.samanage.com/support) om du behöver mer information. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat** , klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera SolarWinds** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

<a name="configure-solarwinds-single-sign-on"></a>

### <a name="configure-solarwinds-service-desk-single-sign-on"></a>Konfigurera SolarWinds Service Desk Single Sign-On

1. Logga in på din SolarWinds-företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **Instrumentpanelen** och välj **Konfiguration** i det vänstra navigeringsfönstret.
   
    ![Instrumentpanel](./media/samanage-tutorial/tutorial_samanage_001.png "Instrumentpanel")

3. Klicka på **Enkel inloggning** .
   
    ![Enkel inloggning](./media/samanage-tutorial/tutorial_samanage_002.png "för Aha!")

4. Gå till avsnittet **Login using SAML** (Logga in via SAML) och utför följande steg:
   
    ![Logga in med SAML](./media/samanage-tutorial/tutorial_samanage_003.png "Logga in med SAML")
 
    a. Klicka på **Enable Single Sign-On with SAML** (Aktivera enkel inloggning med SAML).  
 
    b. I textrutan **Identity Provider URL** (URL för identitetsprovider) klistrar du in värdet för den **Azure AD-identifierare** som du har kopierat från Azure-portalen.    
 
    c. Bekräfta att **inloggnings-URL:en** matchar **inloggnings-URL:en** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.
 
    d. I textrutan för **utloggnings-URL:en** anger du värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.
 
    e. I textrutan **SAML Issuer** (SAML-utfärdare) skriver du in den URI för app-ID som angetts i din identitetsprovider.
 
    f. Öppna ditt base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopierar innehållet i Urklipp och klistra sedan in den i textrutan **Paste your Identity Provider x.509 Certificate below** (Klistra in x.509-certifikat för din identitetsprovider nedan).
 
    ex. Klicka på **skapa användare om de inte finns i SolarWinds** .
 
    h. Klicka på **Uppdatera** .

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory** , välj **Users** och sedan **Alla användare** .

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon** .
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa** .

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SolarWinds.

1. I Azure Portal väljer du **företags program** , väljer **alla program** och väljer sedan **SolarWinds** .

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **SolarWinds** .

3. På menyn till vänster väljer du **Användare och grupper** .

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning** .

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela** .

### <a name="create-solarwinds-test-user"></a>Skapa SolarWinds test användare

För att Azure AD-användare ska kunna logga in på SolarWinds måste de tillhandahållas i SolarWinds.  
När det gäller SolarWinds är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din SolarWinds företags webbplats som administratör.

2. Klicka på **Instrumentpanelen** och välj **Konfiguration** i det vänstra navigeringsfönstret.
   
    ![Installation](./media/samanage-tutorial/tutorial_samanage_001.png "Installation")

3. Klicka på fliken **Användare**
   
    ![Användare](./media/samanage-tutorial/tutorial_samanage_006.png "Användare")

4. Klicka på **Ny användare** .
   
    ![Ny användare](./media/samanage-tutorial/tutorial_samanage_007.png "Ny användare")

5. Skriv **Namn** och **E-postadress** för ett Azure Active Directory-konto som du vill etablera och klicka på **Skapa användare** .
   
    ![Skapa användare](./media/samanage-tutorial/tutorial_samanage_008.png "Skapa användare")
   
   >[!NOTE]
   >Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt. Du kan använda andra verktyg för SolarWinds av användar konton eller API: er som tillhandahålls av SolarWinds för att etablera Azure Active Directory användar konton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SolarWinds på åtkomst panelen, bör du loggas in automatiskt på den SolarWinds som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)