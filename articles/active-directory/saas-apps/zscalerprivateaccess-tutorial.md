---
title: 'Självstudiekurs: Azure Active Directory-integrering med Zscaler Private Access (ZPA) | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler Private Access (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0a1538f640bb4722eca1d4f3a80125837593bab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67085748"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Självstudiekurs: Integrera Zscaler Private Access (ZPA) med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Zscaler Private Access (ZPA) med Azure Active Directory (Azure AD). När du integrerar Zscaler Private Access (ZPA) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Zscaler Private Access (ZPA).
* Aktivera dina användare automatiskt inloggade på Zscaler Private Access (ZPA) med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Zscaler Private Access (ZPA) enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. Zscaler Private Access (ZPA) stöder **SP-initierad** SSO.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Lägga till Zscaler Private Access (ZPA) från galleriet

Om du vill konfigurera integreringen av Zscaler Private Access (ZPA) i Azure AD måste du lägga till Zscaler Private Access (ZPA) från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Zscaler Private Access (ZPA)** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Zscaler Private Access (ZPA)** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Zscaler Private Access (ZPA) med en testanvändare som heter **Britta Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Zscaler Private Access (ZPA).

Så här konfigurerar och testar du Azure AD SSO med Zscaler Private Access (ZPA) slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera Zscaler Private Access (ZPA)](#configure-zscaler-private-access-zpa)** för att konfigurera SSO-inställningarna på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** så att Britta Simon kan använda azure AD-enkel inloggning.
5. **[Skapa Zscaler Private Access (ZPA) testanvändare](#create-zscaler-private-access-zpa-test-user)** för att ha en motsvarighet till Britta Simon i Zscaler Private Access (ZPA) som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på **Zscaler Private Access (ZPA)** och väljer **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    1. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. Skriv en URL i textrutan **Identifierare (enhets-ID):**`https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > Värdet **Logga in på WEBBADRESSN** är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Zscaler Private Access (ZPA) Client support team](https://help.zscaler.com/zpa-submit-ticket) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera Zscaler Private Access (ZPA)** baserat på dina krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Konfigurera Zscaler Private Access (ZPA)

1. Om du vill automatisera konfigurationen i Zscaler Private Access (ZPA) måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren klickar du på **Setup Zscaler Private Access (ZPA)** som leder dig till Zscaler Private Access (ZPA) programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Zscaler Private Access (ZPA). Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-6.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Zscaler Private Access (ZPA) manuellt öppnar du ett nytt webbläsarfönster och loggar in på Zscaler Private Access -företagets webbplats (ZSCALEr Private Access) som administratör och utför följande steg:

4. Klicka på **Administration** till vänster på menyn och navigera till **avsnittet AUTENTISERING** på **IdP-konfiguration**.

    ![Zscaler administratör för privat åtkomst administratör](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. Klicka på **Lägg till IdP-konfiguration**längst upp till höger . 

    ![Zscaler Private Access-administratörs-IDP](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. Gör följande på sidan **Lägg till IdP-konfiguration:**
 
    ![Zscaler Private Access-administratör välj](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Klicka på **Välj fil** om du vill överföra den nedladdade metadatafilen från Azure AD i fältet Ladda **upp IdP-metadatafil.**

    b. Den läser **IdP-metadata** från Azure AD och fyller i all fältinformation som visas nedan.

    ![Zscaler Privat åtkomstadministratör config](./media/zscalerprivateaccess-tutorial/config.png)

    c. Välj din domän från fältet **Domäner.**
    
    d. Klicka på **Spara**.

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

I det här avsnittet ska du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Zscaler Private Access (ZPA).

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Zscaler Private Access (ZPA) i**programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-zscaler-private-access-zpa-test-user"></a>Skapa Zscaler Private Access -testanvändare (ZPA)

I det här avsnittet skapar du en användare som heter Britta Simon i Zscaler Private Access (ZPA). Arbeta med [Zscaler Private Access (ZPA) supportteam](https://help.zscaler.com/zpa-submit-ticket) för att lägga till användarna i Zscaler Private Access (ZPA) plattform.

### <a name="test-sso"></a>Testa SSO

När du väljer Zscaler Private Access (ZPA) på åtkomstpanelen ska du automatiskt loggas in på Zscaler Private Access (ZPA) som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)