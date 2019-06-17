---
title: 'Självstudier: Azure Active Directory-integrering med Soloinsight-CloudGate SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Soloinsight-CloudGate SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2dc6dcbcdee8df93f34cf4d68b5e08453554bc8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090017"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Självstudier: Integrera Soloinsight CloudGate enkel inloggning med Azure Active Directory

I de här självstudierna lär du dig att integrera Soloinsight CloudGate enkel inloggning med Azure Active Directory (AD Azure). När du integrerar Soloinsight CloudGate enkel inloggning med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Soloinsight CloudGate SSO.
* Ge dina användare att automatiskt inloggad till Soloinsight CloudGate SSO med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/).
* Aktiverat prenumeration Soloinsight CloudGate SSO enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för enkel inloggning Soloinsight CloudGate **SP** -initierad SSO.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Lägga till Soloinsight-CloudGate SSO från galleriet

För att konfigurera integreringen av Soloinsight-CloudGate SSO med Azure AD måste du lägga till Soloinsight-CloudGate SSO från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Soloinsight CloudGate SSO** i sökrutan.
1. Välj **Soloinsight CloudGate SSO** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Soloinsight CloudGate SSO med hjälp av en testanvändare kallas **Britta Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Soloinsight CloudGate SSO för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med Soloinsight CloudGate SSO, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera Soloinsight CloudGate SSO](#configure-soloinsight-cloudgate-sso)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Soloinsight CloudGate SSO testanvändare](#create-soloinsight-cloudgate-sso-test-user)**  har en motsvarighet för Britta Simon i Soloinsight CloudGate enkel inloggning som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Soloinsight CloudGate SSO** programsidan integration, hitta den **hantera** och väljer **enkelinloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** ange värdena för följande fält:

    1. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.sigateway.com/login`

    1. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en och identifieraren på det sätt som beskrivs senare i avsnittet **Konfigurera enkel inloggning för Soloinsight-CloudGate SSO** i självstudien.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Base64)** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. På den **Konfigurera enkel inloggning Soloinsight CloudGate** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Configure Soloinsight-CloudGate SSO

1. Om du vill automatisera konfigurationen inom Soloinsight CloudGate SSO, måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![Mina appar-tillägg](common/install-myappssecure-extension.png)

2. När du lägger till tillägg till webbläsaren, klickar på **installationsprogrammet Soloinsight CloudGate SSO** omdirigerar dig till Soloinsight CloudGate SSO-program. Ange administratörsautentiseringsuppgifter för att logga in på Soloinsight CloudGate SSO därifrån. Webbläsartillägget automatiskt att konfigurera program för dig. och automatisera steg 3-8.

    ![Installationskonfiguration](common/setup-sso.png)

3. Om du vill konfigurera Soloinsight CloudGate enkel inloggning manuellt, öppna ett nytt webbläsarfönster och logga in på webbplatsen Soloinsight CloudGate SSO företagets som administratör och utför följande steg:

4. Om du vill hämta de värden som är för att klistras in i Azure-portalen när du konfigurerar grundläggande SAML, logga in på webbportalen CloudGate med dina autentiseringsuppgifter och sedan komma åt SSO-inställningarna som finns på följande sökväg **Start > Administration > Inställningar > Allmänt**.

    ![Inställningar för enkel inloggning med CloudGate](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **URL för SAML-konsument**

    * Kopiera länkarna som är tillgängliga mot den **URL för Saml-konsument** och **omdirigerings-URL** fält och klistra in dem i Azure-portalen **SAML grundkonfiguration** för  **Identifierare (entitets-ID)** och **svars-URL** respektive fält.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **SAML-signeringscertifikat**

    * Gå till källan för certifikat (Base64)-filen som hämtades från Azure-portalen SAML-signeringscertifikat listor och högerklickar du på den. Välj alternativet **Redigera med Notepad++** i listan. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Kopiera innehållet i certifikatfilen (Base64) i Notepad++.

        ![Certifikatkopia](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Klistra in innehållet i fältet **Certifikat** i inställningarna för enkel inloggning på CloudGate-webbportalen och klicka på Spara.

        ![Certifikatportal](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Standardgrupp**

    * Välj **Business Admin** (Företagsadministration) från listrutan för alternativet **Standardgrupp** på CloudGate-webbportalen

        ![Standardgrupp](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD-identifierare och inloggnings-URL**

    * Den kopierade **inloggnings-URL** från Azure portal **Konfigurera enkel inloggning Soloinsight CloudGate** inställningskonfigurationer anges i avsnittet CloudGate Enkelinloggning på portalen.

    * Klistra in den **inloggnings-URL** länk från Azure-portalen i webbportalen CloudGate **AD inloggnings-URL** fält.

    * Klistra in den **Azure AD-identifierare** länk från Azure-portalen i webbportalen CloudGate **AD identifierare** fält

        ![AD-inloggning](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas Britta Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Soloinsight CloudGate SSO.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. Välj **Soloinsight-CloudGate SSO** i programlistan.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **Britta Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Skapa en testanvändare för Soloinsight-CloudGate SSO

För att skapa en testanvändare väljer du **Anställda** på huvudmenyn på CloudGate-webbportalen och fyller i formuläret Lägg till ny medarbetare. Auktoritetsnivån som ska tilldelas till testanvändaren är **Business Admin** (Företagsadministration). Klicka på **Skapa** när alla obligatoriska fält har fyllts i.

![Medarbetartest](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Soloinsight CloudGate SSO i åtkomstpanelen, bör det vara loggas in automatiskt till Soloinsight CloudGate SSO som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)