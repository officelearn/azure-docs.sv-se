---
title: 'Självstudier: Azure Active Directory-integrering med Procore SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Procore SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bba33792bd35227fa64ef556e811cb0bddbab74d
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544729"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Självstudier: Azure Active Directory-integrering med Procore SSO

I den här självstudien får du lära dig hur du integrerar Procore enkel inloggning med Azure Active Directory (AD Azure).
Integrera Procore enkel inloggning med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Procore SSO.
* Du kan aktivera användarna att vara automatiskt inloggad till Procore SSO (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Procore SSO, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Procore SSO enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för procore SSO **IDP** -initierad SSO

## <a name="adding-procore-sso-from-the-gallery"></a>Att lägga till Procore SSO från galleriet

Om du vill konfigurera integreringen av Procore enkel inloggning till Azure AD, som du behöver lägga till Procore SSO från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Procore SSO från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Procore SSO**väljer **Procore SSO** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Procore SSO i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Procore SSO baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Procore SSO ska upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Procore enkel inloggning, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Procore SSO enkel inloggning](#configure-procore-sso-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Procore SSO testanvändare](#create-procore-sso-test-user)**  – du har en motsvarighet för Britta Simon i Procore enkel inloggning som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Procore enkel inloggning:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Procore SSO** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![Procore SSO-domän och URL: er med enkel inloggning för information](common/preintegrated.png)

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera Procore SSO** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-procore-sso-single-sign-on"></a>Konfigurera Procore SSO enkel inloggning

1. Att konfigurera enkel inloggning på **Procore SSO** sida, logga in på webbplatsen procore företag som administratör.

2. I verktygslådan listrutan ned, klickar du på **Admin** att öppna inställningssidan för enkel inloggning.

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/procore_tool_admin.png)

3. Klistra in värden i rutorna enligt beskrivningen nedan-

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. I den **enkel inloggning på utfärdar-URL** text rutan, klistra in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    b. I den **SAML-inloggning på mål-URL** rutan, klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.

    c. Öppna nu den **XML-Metadata för Federation** ned ovan från Azure-portalen och kopiera certifikatet i taggen med namnet **X509Certificate**. Klistra in det kopierade värdet till den **Single Sign On x509 certifikat** box.

4. Klicka på **Spara ändringar**.

5. När du har dessa inställningar du behöver skicka den **domännamn** (t.ex **contoso.com**) via som du loggar in Procore till den [Procore supportteamet](https://support.procore.com/) och de kommer Aktivera federerad enkel inloggning för domänen.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Procore SSO.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Procore SSO**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Procore SSO**.

    ![Länken Procore SSO i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-procore-sso-test-user"></a>Skapa Procore SSO testanvändare

Följ de stegen nedan för att skapa en Procore testanvändare på Procore SSO-sida.

1. Logga in på webbplatsen procore företag som administratör.    

2. I verktygslådan listrutan ned, klickar du på **Directory** att öppna sidan företagets katalog.

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Klicka på **lägga till en Person** alternativet för att öppna formuläret och ange utföra följande alternativ -

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/Procore_user_add.png)

    a. I den **Förnamn** textrutan typ användarens förnamn som **Britta**.

    b. I den **efternamn** textrutan typ användarens efternamn som **Simon**.

    c. I den **e-postadress** textrutan typ användarens e-postadress som BrittaSimon@contoso.com.

    d. Välj **behörighet mallen** som **tillämpa behörighet mallen senare**.

    e. Klicka på **Skapa**.

4. Kontrollera och uppdatera informationen för den nyligen tillagda kontakten.

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/Procore_user_check.png)

5. Klicka på **spara och skicka inbjudan** (om det krävs en inbjudan via e-post) eller **spara** (spara direkt) för att slutföra registreringen för användaren.
    
    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Procore SSO i åtkomstpanelen, bör det vara loggas in automatiskt till Procore SSO som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

