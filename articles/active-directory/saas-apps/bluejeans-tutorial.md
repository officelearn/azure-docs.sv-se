---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med BlueJeans för Azure AD | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BlueJeans för Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc38f63c5b6361122c236543320b91d22faa70a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72595051"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med BlueJeans för Azure AD

I den här självstudien får du lära dig hur du integrerar BlueJeans för Azure AD med Azure Active Directory (Azure AD). När du integrerar BlueJeans för Azure AD med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till BlueJeans för Azure AD.
* Aktivera dina användare så att de automatiskt loggas in på BlueJeans för Azure AD med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* BlueJeans för Azure AD enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* BlueJeans for Azure **SP** AD stöder SP-initierad SSO

* BlueJeans for Azure AD stöder [ **automatiserad** användaretablering](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>Lägga till BlueJeans för Azure AD från galleriet

Om du vill konfigurera integreringen av BlueJeans för Azure AD i Azure AD måste du lägga till BlueJeans för Azure AD från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **BlueJeans för Azure AD** i sökrutan i avsnittet Lägg till **från galleriet.**
1. Välj **BlueJeans för Azure AD** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Konfigurera och testa en azure AD-inloggning för BlueJeans för Azure AD

Konfigurera och testa Azure AD SSO med BlueJeans för Azure AD med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i BlueJeans för Azure AD.

Om du vill konfigurera och testa Azure AD SSO med BlueJeans för Azure AD slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera BlueJeans för Azure AD SSO](#configure-bluejeans-for-azure-ad-sso)** – för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa BlueJeans för Azure AD-testanvändare](#create-bluejeans-for-azure-ad-test-user)** – om du vill ha en motsvarighet till B.Simon i BlueJeans för Azure AD som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)på sidan **BlueJeans for Azure AD-programintegrering** letar du reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<companyname>.bluejeans.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [BlueJeans för Azure AD-klientsupportteam](https://support.bluejeans.com/contact) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera BlueJeans för Azure AD.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till BlueJeans för Azure AD.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **BlueJeans för Azure AD**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-bluejeans-for-azure-ad-sso"></a>Konfigurera BlueJeans för Azure AD SSO

1. I ett annat webbläsarfönster loggar du in på din **BlueJeans för Azure AD-företagswebbplats** som administratör.

2. Gå till **ADMIN \>GRUPPINSTÄLLNINGAR \> SÄKERHET**.

    ![Administratör](./media/bluejeans-tutorial/ic785868.png "Admin")

3. I avsnittet **SÄKERHET** utför du följande steg:

    ![SAML Enkel inloggning](./media/bluejeans-tutorial/ic785869.png "SAML Enkel inloggning")

    a. Välj **SAML Single Sign On** (SAML enkel inloggning).

    b. Välj **aktivera automatisk etablering**.

4. Gå vidare med följande steg:

    ![Sökväg till certifikat](./media/bluejeans-tutorial/ic785870.png "Sökväg till certifikat")

    a. Klicka på **Välj fil** för att ladda upp det base-64-kodade certifikatet som du har laddat ned från Azure-portalen.

    b. I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    c. I textrutan **Password Change URL** (URL för Byt lösenord) klistrar du in värdet för **URL:en för Byt lösenord** som du har kopierat från Azure-portalen.

    d. I textrutan för **utloggnings-URL:en** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

5. Gå vidare med följande steg:

    ![Spara ändringar](./media/bluejeans-tutorial/ic785874.png "Spara ändringar")

    a. Skriv `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`i textrutan **Användar-ID** .

    b. I textrutan **E-post** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Klicka på **SPARA ÄNDRINGAR** för att spara ändringarna.

### <a name="create-bluejeans-for-azure-ad-test-user"></a>Skapa BlueJeans för Azure AD-testanvändare

Syftet med det här avsnittet är att skapa en användare som heter B.Simon i BlueJeans för Azure AD. BlueJeans för Azure AD stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](bluejeans-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på din **BlueJeans för Azure AD-företagswebbplats** som administratör.

2. Gå till **ADMIN \> HANTERA ANVÄNDARE \> LÄGG TILL ANVÄNDARE**.

    ![Administratör](./media/bluejeans-tutorial/ic785877.png "Admin")

    > [!IMPORTANT]
    > Fliken **LÄGG TILL ANVÄNDARE** är endast tillgänglig om **Aktivera automatisk etablering** är avmarkerat på fliken **SÄKERHET**.

3. Gör följande i avsnittet **LÄGG TILL ANVÄNDARE:**

    ![Lägg till användare](./media/bluejeans-tutorial/ic785886.png "Lägg till användare")

    a. I textrutan **Förnamn** anger du förnamnet för användaren som **B**.

    b. I textrutan **Efternamn** anger du efternamn på användaren som **Simon**.

    c. I textrutan **Välj en BlueJeans för Azure AD Användarnamn** anger du användarnamnet för användare som **Brittasimon**

    d. I textrutan **Skapa ett lösenord** anger du ditt lösenord.

    e. I textrutan **Företag** skriver du ditt företag.

    f. I textrutan **E-postadress** anger du användarens e-postadress, som `b.simon\@contoso.com`.

    g. Ange ditt **mötes-ID i dialogrutan Skapa en BlueJeans för Azure AD-mötes-ID.**

    h. I textrutan för att **välja ett lösenord för moderator** anger du ditt lösenord.

    i. Klicka på **FORTSÄTT**.

    ![Lägg till användare](./media/bluejeans-tutorial/ic785887.png "Lägg till användare")

    J. Klicka på **LÄGG TILL ANVÄNDARE**.

> [!NOTE]
> Du kan använda andra BlueJeans för Azure AD-verktyg för att skapa användarkonton eller API:er som tillhandahålls av BlueJeans för Azure AD för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen BlueJeans för Azure AD på åtkomstpanelen bör du automatiskt loggas in på BlueJeans för Azure AD som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova BlueJeans för Azure AD med Azure AD](https://aad.portal.azure.com/)

