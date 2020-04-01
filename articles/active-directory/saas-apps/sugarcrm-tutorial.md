---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Sugar CRM | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Sugar CRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fae7b80fd4d2fcec32bbef5e4cdf18e576412a86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74231965"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Sugar CRM

I den här självstudien får du lära dig hur du integrerar Sugar CRM med Azure Active Directory (Azure AD). När du integrerar Sugar CRM med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Sugar CRM.
* Gör det möjligt för användarna att automatiskt loggas in i Sugar CRM med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Sugar CRM enkel inloggning (SSO) aktiverat abonnemang.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Sugar CRM stöder **SP** initierade SSO

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-sugar-crm-from-the-gallery"></a>Lägga sugar CRM från galleriet

Om du vill konfigurera integreringen av Sugar CRM i Azure AD måste du lägga till Sugar CRM från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Sugar CRM** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Sugar CRM** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sugar-crm"></a>Konfigurera och testa en azure AD-inloggning för Sugar CRM

Konfigurera och testa Azure AD SSO med Sugar CRM med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Sugar CRM.

Så här konfigurerar och testar du Azure AD SSO med Sugar CRM:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Sugar CRM SSO](#configure-sugar-crm-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Sugar CRM-testanvändare](#create-sugar-crm-test-user)** - om du vill ha en motsvarighet till B.Simon i Sugar CRM som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta [Azure portal](https://portal.azure.com/)reda på avsnittet Hantera på sidan **Hantera** på sidan Sugar **CRM-program**och välj enkel inloggning . **Sugar CRM**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:

    | |
    |--|
    | `https://<companyname>.sugarondemand.com`|
    | `https://<companyname>.trial.sugarcrm`|

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    | |
    |--|
    | `https://<companyname>.sugarondemand.com/<companyname>`|
    | `https://<companyname>.trial.sugarcrm.com/<companyname>`|
    | `https://<companyname>.trial.sugarcrm.eu/<companyname>`|

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med en faktisk inloggnings-URL och svars-URL. Kontakta [Sugar CRM Client supportteam](https://support.sugarcrm.com/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera Sugar CRM.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Sugar CRM.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Sugar CRM**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-sugar-crm-sso"></a>Konfigurera Sugar CRM SSO

1. Logga in på webbplatsen Sugar CRM som administratör i ett annat webbläsarfönster.

1. Gå till **Admin**.

    ![Administratör](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. Klicka på **Lösenordshantering**i avsnittet **Administration** .

    ![Administration](./media/sugarcrm-tutorial/ic795889.png "Administration")

1. Välj **Aktivera SAML-autentisering**.

    ![Administration](./media/sugarcrm-tutorial/ic795890.png "Administration")

1. Gör följande i avsnittet **SAML-autentisering**:

    ![SAML-autentisering](./media/sugarcrm-tutorial/ic795891.png "SAML-autentisering")  

    a. Klistra in värdet **för inloggnings-URL**i textrutan **Inloggnings-URL** , som du har kopierat från Azure-portalen.
  
    b. Klistra in värdet för url för **utloggning**i **SLO-URL-textrutan** som du har kopierat från Azure-portalen.
  
    c. Öppna ditt bas-64-kodade certifikat i anteckningar, kopiera innehållet i det i Urklipp och klistra sedan in hela certifikatet i textrutan **X.509 Certifikat.**
  
    d. Klicka på **Spara**.

### <a name="create-sugar-crm-test-user"></a>Skapa Sugar CRM-testanvändare

För att Azure AD-användare ska kunna logga in på Sugar CRM måste de etableras i Sugar CRM. När det gäller Sugar CRM, etablering är en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på webbplatsen **för Sugar CRM** som administratör.

1. Gå till **Admin**.

    ![Administratör](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. Klicka på **Användarhantering**i avsnittet **Administration** .

    ![Administration](./media/sugarcrm-tutorial/ic795893.png "Administration")

1. Gå till **Användare \> Skapa ny användare**.

    ![Skapa ny användare](./media/sugarcrm-tutorial/ic795894.png "Skapa ny användare")

1. Gör följande på fliken **Användarprofil:**

    ![Ny användare](./media/sugarcrm-tutorial/ic795895.png "Ny användare")

    * Skriv **användarnamn,** **efternamn**och **e-postadress** för en giltig Azure Active Directory-användare i relaterade textrutor.
  
1. Som **status**väljer du **Aktiv**.

1. Gör följande på fliken Lösenord:

    ![Ny användare](./media/sugarcrm-tutorial/ic795896.png "Ny användare")

    a. Skriv lösenordet i den relaterade textrutan.

    b. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra verktyg för att skapa sugar CRM-användarkonton eller API:er som tillhandahålls av Sugar CRM för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på sugar CRM-panelen på åtkomstpanelen ska du automatiskt loggas in på den Sugar CRM som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Sugar CRM med Azure AD](https://aad.portal.azure.com/)

