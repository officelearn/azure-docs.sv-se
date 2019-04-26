---
title: 'Självstudier: Azure Active Directory-integrering med Soloinsight-CloudGate SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Soloinsight-CloudGate SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e8b2b4d1a660fe2f1289bba6fa596d08ec824b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60341478"
---
# <a name="tutorial-azure-active-directory-integration-with-soloinsight-cloudgate-sso"></a>Självstudier: Azure Active Directory-integrering med Soloinsight-CloudGate SSO

I den här självstudien får du lära dig hur du integrerar Soloinsight-CloudGate SSO med Azure Active Directory (AD Azure).
Integreringen av Soloinsight-CloudGate med Azure AD medför följande fördelar:

* Du kan styra vem som har åtkomst till Soloinsight-CloudGate SSO från Azure AD.
* Du kan göra det möjligt för användarna att logga in automatiskt till Soloinsight-CloudGate SSO med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Soloinsight-CloudGate SSO behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En prenumeration på Soloinsight-CloudGate SSO med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Soloinsight-CloudGate SSO stöder **SP**-initierad enkel inloggning

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Lägga till Soloinsight-CloudGate SSO från galleriet

För att konfigurera integreringen av Soloinsight-CloudGate SSO med Azure AD måste du lägga till Soloinsight-CloudGate SSO från galleriet i din lista över hanterade SaaS-appar.

**Du lägger till Soloinsight-CloudGate SSO från galleriet genom att följa dessa steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Soloinsight-CloudGate SSO** i sökrutan, välj **Soloinsight-CloudGate SSO** i resultatrutan och klicka sedan på **Lägg till** för att lägga till programmet.

     ![Soloinsight-CloudGate SSO i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning mellan Azure AD och Soloinsight-CloudGate SSO baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Soloinsight-CloudGate SSO upprättas.

För att konfigurera och testa enkel inloggning mellan Azure AD och Soloinsight CloudGate SSO måste du göra följande:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso-single-sign-on)** – för att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en testanvändare för Soloinsight CloudGate-SSO](#create-soloinsight-cloudgate-sso-test-user)** – för att skapa en motsvarighet till Britta Simon i Soloinsight-CloudGate SSO som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning mellan Azure AD och Soloinsight-CloudGate SSO:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Soloinsight-CloudGate SSO** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om domäner och URL:er för enkel inloggning med Soloinsight-CloudGate SSO](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.sigateway.com/login`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.sigateway.com/process/sso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en och identifieraren på det sätt som beskrivs senare i avsnittet **Konfigurera enkel inloggning för Soloinsight-CloudGate SSO** i självstudien.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga URL:er beroende på dina behov i avsnittet **Konfigurera Soloinsight-CloudGate SSO**.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-soloinsight-cloudgate-sso-single-sign-on"></a>Konfigurera enkel inloggning för Soloinsight-CloudGate SSO

1. För att hämta värdena som ska klistras in på Azure-portalen när du konfigurerar grundläggande SAML loggar du in på webbportalen för CloudGate med dina autentiseringsuppgifter och öppnar sedan inställningarna för enkel inloggning, som du hittar på följande sökväg **Start > Administration > Systeminställningar > Allmänt**.

    ![Inställningar för enkel inloggning med CloudGate](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

2. **URL för SAML-konsument**

    * Kopiera länkarna som är tillgängliga mot **URL:en för SAML-konsument** och **omdirigerings-URL:en** och klistra in dem i fälten  **Identifierare (entitets-ID)** och **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

3. **SAML-signeringscertifikat**

    * Gå till källan för certifikatfilen (Base64) som hämtades från listorna med SAML-signeringscertifikat på Azure-portalen och högerklicka på den. Välj alternativet **Redigera med Notepad++** i listan. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Kopiera innehållet i certifikatfilen (Base64) i Notepad++.

        ![Certifikatkopia](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Klistra in innehållet i fältet **Certifikat** i inställningarna för enkel inloggning på CloudGate-webbportalen och klicka på Spara.

        ![Certifikatportal](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

4. **Standardgrupp**

    * Välj **Business Admin** (Företagsadministration) från listrutan för alternativet **Standardgrupp** på CloudGate-webbportalen

        ![Standardgrupp](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

5. **AD-identifierare och inloggnings-URL**

    * Den kopierade **inloggnings-URL:en** i avsnittet med **konfigurationer för enkel inloggning med Soloinsight CloudGate** på Azure-portalen ska anges i motsvarande avsnitt på CloudGate-webbportalen. 

    * Klistra in länken i **Inloggnings-URL** från Azure-portalen i fältet **AD Login URL** (Inloggnings-URL för AD) på CloudGate-webbportalen.
     
    * Klistra in länken i **Azure AD-identifierare** från Azure-portalen i fältet **AD-identifierare** på CloudGate-webbportalen

        ![AD-inloggning](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** skriver **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du göra det möjligt för Britta Simon att använda enkel inloggning med Azure genom att bevilja åtkomst till Soloinsight CloudGate SSO.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Soloinsight-CloudGate SSO**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Soloinsight-CloudGate SSO** i programlistan.

    ![Soloinsight-CloudGate SSO-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Skapa en testanvändare för Soloinsight-CloudGate SSO

För att skapa en testanvändare väljer du **Anställda** på huvudmenyn på CloudGate-webbportalen och fyller i formuläret Lägg till ny medarbetare. Auktoritetsnivån som ska tilldelas till testanvändaren är **Business Admin** (Företagsadministration). Klicka på **Skapa** när alla obligatoriska fält har fyllts i.

![Medarbetartest](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen för Soloinsight-CloudGate SSO på åtkomstpanelen bör du loggas in automatiskt i Soloinsight-CloudGate SSO som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

