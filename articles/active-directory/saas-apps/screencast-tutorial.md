---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Screencast-O-Matic | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Screencast-O-Matic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc887e95b6fa6f8b17fbbb3dbaae5105385a07fa
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74132149"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Screencast-O-Matic

I den här självstudien får du lära dig hur du integrerar Screencast-O-Matic med Azure Active Directory (Azure AD). När du integrerar Screencast-O-Matic med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Screencast-O-Matic.
* Gör att användarna automatiskt loggas in på Screencast-O-Matic med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Screencast-O-Matic enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Screencast-O-Matic stöder **SP** initierade SSO
* Screencast-O-Matic stöder just in time-användaretablering **Just In Time**

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Lägga till Screencast-O-Matic från galleriet

Om du vill konfigurera integreringen av Screencast-O-Matic i Azure AD måste du lägga till Screencast-O-Matic från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Screencast-O-Matic** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Screencast-O-Matic** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-screencast-o-matic"></a>Konfigurera och testa en enkel Azure AD-inloggning för Screencast-O-Matic

Konfigurera och testa Azure AD SSO med Screencast-O-Matic med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Screencast-O-Matic.

Så här konfigurerar och testar du Azure AD SSO med Screencast-O-Matic:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Screencast-O-Matic SSO](#configure-screencast-o-matic-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Screencast-O-Matic-testanvändare](#create-screencast-o-matic-test-user)** – om du vill ha en motsvarighet till B.Simon i Screencast-O-Matic som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Screencast-O-Matic-programintegrering** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Screencast-O-Matic Client supportteam](mailto:support@screencast-o-matic.com) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera Screencast-O-Matic.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Screencast-O-Matic.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Screencast-O-Matic**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-screencast-o-matic-sso"></a>Konfigurera Screencast-O-Matic SSO

1. Om du vill automatisera konfigurationen i Screencast-O-Matic måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

1. När du har lagt till tillägget i webbläsaren, klicka på **Konfigurera Screencast-O-Matic** kommer att leda dig till Screencast-O-Matic ansökan. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Screencast-O-Matic. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-11.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

1. Om du vill konfigurera Screencast-O-Matic manuellt öppnar du ett nytt webbläsarfönster och loggar in på företagets webbplats Screencast-O-Matic som administratör och utför följande:

1. Klicka på **Prenumeration**.

    ![Abonnemanget](./media/screencast-tutorial/tutorial_screencast_sub.png)

1. Klicka på **Installationsprogrammet**under Avsnittet **Åtkomst.**

    ![Åtkomsten](./media/screencast-tutorial/tutorial_screencast_setup.png)

1. Gör följande på sidan **Installationsåtkomst.**

1. Skriv ditt instansnamn i den angivna textrutan under Avsnittet **Åtkomst-URL.**

    ![Åtkomsten](./media/screencast-tutorial/tutorial_screencast_access.png)

1. Välj **Kräv domänanvändare** under **avsnittet SAML-användarbegränsning (valfritt).**

1. Under **Upload IDP Metadata XML File**klickar du på Välj **fil** för att ladda upp metadata som du har hämtat från Azure-portalen.

1. Klicka på **OK**.

    ![Åtkomsten](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-screencast-o-matic-test-user"></a>Skapa Screencast-O-Matic-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Screencast-O-Matic. Screencast-O-Matic stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Screencast-O-Matic skapas en ny efter autentisering. Om du behöver skapa en användare manuellt kontaktar du [Screencast-O-Matic Client support team](mailto:support@screencast-o-matic.com).

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Screencast-O-Matic på åtkomstpanelen ska du automatiskt loggas in på den Screencast-O-Matic som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Screencast-O-Matic med Azure AD](https://aad.portal.azure.com/)