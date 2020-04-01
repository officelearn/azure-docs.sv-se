---
title: 'Självstudiekurs: Azure Active Directory-integrering med Soloinsight-CloudGate SSO | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Soloinsight-CloudGate SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b88822f164e0fe8cdf55eddfa981644f725e01f3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159928"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Självstudiekurs: Integrera Soloinsight-CloudGate SSO med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Soloinsight-CloudGate SSO med Azure Active Directory (Azure AD). När du integrerar Soloinsight-CloudGate SSO med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Soloinsight-CloudGate SSO.
* Aktivera dina användare automatiskt inloggad på Soloinsight-CloudGate SSO med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri provperiod [här.](https://azure.microsoft.com/pricing/free-trial/)
* Soloinsight-CloudGate SSO enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. Soloinsight-CloudGate SSO stöder **SP** initierade SSO.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Lägga till Soloinsight-CloudGate SSO från galleriet

För att konfigurera integreringen av Soloinsight-CloudGate SSO med Azure AD måste du lägga till Soloinsight-CloudGate SSO från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Soloinsight-CloudGate SSO** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Soloinsight-CloudGate SSO** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Soloinsight-CloudGate SSO med en testanvändare som heter **Britta Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Soloinsight-CloudGate SSO.

Så här konfigurerar och testar du Azure AD SSO med Soloinsight-CloudGate SSO:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso)** för att konfigurera SSO-inställningarna på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** så att Britta Simon kan använda azure AD-enkel inloggning.
5. **[Skapa Soloinsight-CloudGate SSO-testanvändare](#create-soloinsight-cloudgate-sso-test-user)** för att ha en motsvarighet till Britta Simon i Soloinsight-CloudGate SSO som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)på sidan **Soloinsight-CloudGate** SSO-programintegrering hittar du avsnittet **Hantera** och väljer **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    1. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.sigateway.com/login`

    1. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en och identifieraren på det sätt som beskrivs senare i avsnittet **Konfigurera enkel inloggning för Soloinsight-CloudGate SSO** i självstudien.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera Soloinsight-CloudGate SSO.**

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Konfigurera Soloinsight-CloudGate SSO

1. Om du vill automatisera konfigurationen i Soloinsight-CloudGate SSO måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren klickar du på **Setup Soloinsight-CloudGate SSO** leder dig till Soloinsight-CloudGate SSO-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Soloinsight-CloudGate SSO. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-8.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Soloinsight-CloudGate SSO manuellt öppnar du ett nytt webbläsarfönster och loggar in på webbplatsen Soloinsight-CloudGate SSO som administratör och utför följande steg:

4. Om du vill hämta de värden som ska klistras in i Azure-portalen när du konfigurerar Basic SAML loggar du in på CloudGate-webbportalen med dina autentiseringsuppgifter och öppnar sedan SSO-inställningarna, som finns på följande sökväg **Hem>Administration>Systeminställningar>Allmänt**.

    ![Inställningar för enkel inloggning med CloudGate](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **URL för SAML-konsument**

    * Kopiera länkarna som är tillgängliga mot **Saml Consumer-URL:en** och **URL-fälten Omdirigera** och klistra in dem i azure portal **Basic SAML-konfigurationsavsnittet** för **identifierare (entitets-ID)** respektive **Svars-URL.for** the Saml Consumer URL fields and paste them in the Azure portal Basic SAML Configuration section for Identifier (Entity ID) and Reply URL fields respectively.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **SAML-signeringscertifikat**

    * Gå till källan till filen Certifikat (Base64) som hämtades från AZURE PORTAL SAML Signing Certificate listor och högerklicka på den. Välj alternativet **Redigera med Notepad++** i listan. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Kopiera innehållet i certifikatfilen (Base64) i Notepad++.

        ![Certifikatkopia](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Klistra in innehållet i fältet **Certifikat** i inställningarna för enkel inloggning på CloudGate-webbportalen och klicka på Spara.

        ![Certifikatportal](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Standardgrupp**

    * Välj **Business Admin** (Företagsadministration) från listrutan för alternativet **Standardgrupp** på CloudGate-webbportalen

        ![Standardgrupp](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD-identifierare och inloggnings-URL**

    * Den kopierade **inloggnings-URL:en** från **Azure-portalen Konfigurera Soloinsight-CloudGate SSO-konfigurationer** ska anges i avsnittet CloudGate Web Portal SSO-inställningar.

    * Klistra in länken **Inloggnings-URL** från Azure-portalen i fältet URL för CloudGate Web Portal **AD-inloggning.**

    * Klistra in **azure AD-identifierare** från Azure-portalen i fältet CloudGate Web Portal **AD Identifier**

        ![AD-inloggning](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter Britta Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Soloinsight-CloudGate SSO.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Soloinsight-CloudGate SSO** i programlistan.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Skapa en testanvändare för Soloinsight-CloudGate SSO

För att skapa en testanvändare väljer du **Anställda** på huvudmenyn på CloudGate-webbportalen och fyller i formuläret Lägg till ny medarbetare. Auktoritetsnivån som ska tilldelas till testanvändaren är **Business Admin** (Företagsadministration). Klicka på **Skapa** när alla obligatoriska fält har fyllts i.

![Medarbetartest](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Soloinsight-CloudGate SSO på åtkomstpanelen ska du automatiskt loggas in på soloinsight-CloudGate SSO som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)