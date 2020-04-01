---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Zoom | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zoom.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f9d727154adf0a2099d7a9144c109cef9c91238
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "70743973"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Zoom

I den här självstudien får du lära dig hur du integrerar Zoom med Azure Active Directory (Azure AD). När du integrerar Zoom med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Zoom.
* Gör att användarna automatiskt loggas in för att zooma med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Zooma en enda inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Zoom stöder **SP** initierade SSO och 
* Zoom stöder [ **automatisk** användaretablering](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial).

## <a name="adding-zoom-from-the-gallery"></a>Lägga till Zoom från galleriet

För att kunna konfigurera integreringen av Zoom i Azure AD måste du lägga till Zoom från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Zooma** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Zooma** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoom"></a>Konfigurera och testa en azure AD-inloggning för zoom

Konfigurera och testa Azure AD SSO med Zoom med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Zoom.

Om du vill konfigurera och testa Azure AD SSO med Zoom slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
2. **[Konfigurera Zoom SSO](#configure-zoom-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa zoomtestanvändare](#create-zoom-test-user)** – om du vill ha en motsvarighet till B.Simon i Zoom som är länkad till Azure AD-representationen av användaren.
3. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Zoomprogram** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.zoom.us`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `<companyname>.zoom.us`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Zooms supportteam](https://support.zoom.us/hc/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera zoomar** baserat på dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

> [!NOTE]
> Mer information om hur du konfigurerar roll i Azure AD finns i [Konfigurera rollanspråket som utfärdats i SAML-token för företagsprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

> [!NOTE]
> Zoom kan förvänta sig ett gruppanspråk i SAML-nyttolasten. Om du har skapat några grupper kontaktar du supportteamet för [Zoomklienten](https://support.zoom.us/hc/) med gruppinformationen så att de kan konfigurera gruppinformationen när de är. Du måste också ange object ID för att [zooma klientsupport så](https://support.zoom.us/hc/) att de kan konfigurera objekt-ID:t i slutet. Information om hur du hämtar object-ID:et finns i [Konfigurera zoom med Azure](https://support.zoom.us/hc/articles/115005887566).

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Zoom.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Zooma**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-zoom-sso"></a>Konfigurera Zoom SSO

1. Logga in på zoomföretagets webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på fliken **Enkel inloggning**.

    ![Fliken Enkel inloggning](./media/zoom-tutorial/ic784700.png "Enkel inloggning")

3. Klicka på fliken **Säkerhetskontroll** och gå sedan till inställningarna för **Enkel inloggning**.

4. Gör följande i avsnittet Enkel inloggning:

    ![Avsnitt med enkel inloggning](./media/zoom-tutorial/ic784701.png "Enkel inloggning")

    a. I textrutan **Sign-in page URL** (Inloggnings-URL) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    b. För **URL-värdet för utspelssidan** måste du gå till Azure-portalen och klicka på **Azure Active Directory** till vänster och sedan navigera till **Appregistreringar**.

    ![Azure Active Directory-knappen](./media/zoom-tutorial/appreg.png)

    c. Klicka på **Slutpunkter**

    ![Knappen Slutpunkt](./media/zoom-tutorial/endpoint.png)

    d. Kopiera **SLUTPUNKTEN FÖR UT SIGN-UT-UTSKRIFT AV SAML-P** OCH KLISTRA IN den i **textrutan för utskrivningssidan för utspelningssidan.**

    ![Knappen Kopiera slutpunkt](./media/zoom-tutorial/endpoint1.png)

    e. Öppna ditt Base64-kodade certifikat i Anteckningar, kopiera innehållet till Urklipp och klistra in det i textrutan **Certifikat för identitetsprovider**.

    f. Klistra in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen i textrutan **Utfärdare.** 

    g. Klicka på **Spara**.

    > [!NOTE]
    > Mer information finns i zoomdokumentationen[https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>Skapa Zoom-testanvändare

Syftet med det här avsnittet är att skapa en användare som heter B.Simon i Zoom. Zoom stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial) om hur du konfigurerar automatisk användaretablering.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta [Zoom Client support team](https://support.zoom.us/hc/)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Zoom-panelen i åtkomstpanelen bör du automatiskt loggas in på Zoom som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova zooma med Azure AD](https://aad.portal.azure.com/)
