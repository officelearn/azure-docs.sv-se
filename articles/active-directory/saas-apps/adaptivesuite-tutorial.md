---
title: 'Självstudiekurs: Azure Active Directory-integrering med Adaptiva insikter | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adaptive Insights.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf05fc30a2b111a12c0b8e131db5117ec784075b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232125"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Självstudiekurs: Integrera adaptiva insikter med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Adaptive Insights med Azure Active Directory (Azure AD). När du integrerar Adaptive Insights med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Adaptiva insikter.
* Gör att användarna automatiskt loggas in på Adaptive Insights med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Anpassad Insights enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Adaptive Insights stöder **IDP-initierad** SSO

## <a name="adding-adaptive-insights-from-the-gallery"></a>Lägga till adaptiva insikter från galleriet

Om du vill konfigurera integreringen av adaptiva insikter i Azure AD måste du lägga till adaptiva insikter från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Adaptiva insikter** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Adaptiv insights** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Adaptive Insights med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Adaptive Insights.

Så här konfigurerar och testar du Azure AD SSO med Adaptive Insights:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SSO för adaptiva insikter](#configure-adaptive-insights-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
5. **[Skapa Adaptive Insights-testanvändare](#create-adaptive-insights-test-user)** – om du vill ha en motsvarighet till B.Simon i Adaptive Insights som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på sidan **Adaptive Insights-programintegration** och väljer **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Du kan hämta värden för identifierare(entitets-ID) och Svara på URL från sidan Adaptiv insights **SAML SSO-inställningar.**

4. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera anpassade insikter.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Konfigurera SSO för adaptiva insikter

1. I ett annat webbläsarfönster loggar du in på företagets webbplats Adaptive Insights som administratör.

2. Gå till **Administration**.

    ![Administratör](./media/adaptivesuite-tutorial/ic805644.png "Admin")

3. Klicka på **SAML SSO-inställningar**i avsnittet **Användare och roller** .

    ![Hantera SAML SSO-inställningar](./media/adaptivesuite-tutorial/ic805645.png "Hantera SAML SSO-inställningar")

4. Gör följande på sidan **SAML SSO-inställningar:**

    ![INSTÄLLNINGAR FÖR SAML SSO](./media/adaptivesuite-tutorial/ic805646.png "INSTÄLLNINGAR FÖR SAML SSO")

    a. Skriv ett namn för konfigurationen i textrutan **identitetsproviderns namn.**

    b. Klistra in **Azure AD-identifierare** värdet kopieras från Azure-portalen till **identitetsprovidern entity ID** textbox.

    c. Klistra in **värdet för inloggnings-URL** som kopierats från Azure-portalen till **SSO-url-textrutan för identitetsprovidern.**

    d. Klistra in **URL-värdet för utloggning** som kopierats från Azure-portalen till textrutan **Anpassad utloggnings-URL.**

    e. Om du vill ladda upp det nedladdade certifikatet klickar du på **Välj fil**.

    f. Välj följande, för:

     * **SAML-användar-ID**väljer **du Användarens adaptiva insikters användarnamn**.

     * **SAML-användar-ID-plats**väljer **du Användar-ID i NameID för ämne**.

     * **SAML NameID-format**väljer du **E-postadress**.

     * **Aktivera SAML**, välj **Tillåt SAML SSO och direkt adaptiv insights-inloggning**.

    g. Kopiera **SSO-URL för adaptiva insikter** och klistra in i textrutorna **Identifierare(entitets-ID)** och **Svara på URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    h. Klicka på **Spara**.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Adaptive Insights.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Adaptive Insights**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-adaptive-insights-test-user"></a>Skapa testanvändare för Adaptive Insights

Om du vill att Azure AD-användare ska kunna logga in på Adaptiva insikter måste de etableras i Adaptive Insights. När det gäller Adaptive Insights är etablering en manuell aktivitet.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på företagets webbplats **Adaptive Insights** som administratör.

2. Gå till **Administration**.

   ![Administratör](./media/adaptivesuite-tutorial/IC805644.png "Admin")

3. Klicka på **Användare**i avsnittet **Användare och roller** .

   ![Lägg till användare](./media/adaptivesuite-tutorial/IC805648.png "Lägg till användare")

4. Gör följande i avsnittet **Ny användare:**

   ![Skicka](./media/adaptivesuite-tutorial/IC805649.png "Skicka")

   a. Skriv **namn,** **användarnamn,** **e-post**, **Lösenord** för en giltig Azure Active Directory-användare som du vill etablera i relaterade textrutor.

   b. Välj en **roll**.

   c. Klicka på **Skicka**.

> [!NOTE]
> Du kan använda andra adaptive insights-verktyg eller API:er för att skapa adaptiva insikter för att etablera Azure AD-användarkonton.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Adaptiva insikter på åtkomstpanelen bör du automatiskt loggas in på de adaptiva insikter som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

