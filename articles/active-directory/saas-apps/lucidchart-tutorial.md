---
title: 'Självstudiekurs: Azure Active Directory-integrering med Lucidchart | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Lucidchart.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3eace60445dc9d52f9690da74360282efbb4cbe5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76291219"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Lucidchart

I den här självstudien får du lära dig hur du integrerar Lucidchart med Azure Active Directory (Azure AD). När du integrerar Lucidchart med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Lucidchart.
* Gör att användarna automatiskt loggas in på Lucidchart med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Lucidchart enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Lucidchart stöder **SP** initierade SSO
* Lucidchart stöder just in time-användaretablering **Just In Time**
* När du har konfigurerat Lucidchart kan du framtvinga sessionskontroller, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollerna sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-lucidchart-from-the-gallery"></a>Lägga till Lucidchart från galleriet

Om du vill konfigurera integreringen av Lucidchart i Azure AD måste du lägga till Lucidchart från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Lucidchart** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Lucidchart** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-lucidchart"></a>Konfigurera och testa en azure AD-inloggning för Lucidchart

Konfigurera och testa Azure AD SSO med Lucidchart med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Lucidchart.

Så här konfigurerar och testar du Azure AD SSO med Lucidchart:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Lucidchart SSO](#configure-lucidchart-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Lucidchart-testanvändare](#create-lucidchart-test-user)** – om du vill ha en motsvarighet till B.Simon i Lucidchart som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan Hantera på sidan [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**. **Lucidchart**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

   Skriv en URL **som:**`https://chart2.office.lucidchart.com/saml/sso/azure`

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Lucidchart.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Lucidchart.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Lucidchart**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-lucidchart-sso"></a>Konfigurera Lucidchart SSO

1. I ett annat webbläsarfönster loggar du in på din webbplats för Lucidchart-företag som administratör.

2. Klicka på **Team**på menyn högst upp .

    ![Team](./media/lucidchart-tutorial/ic791190.png "Team")

3. Klicka på **Program \> hantera SAML**.

    ![Hantera SAML](./media/lucidchart-tutorial/ic791191.png "Hantera SAML")

4. Gör följande på dialogsidan **för SAML-autentiseringsinställningar:**

    a. Välj **Aktivera SAML-autentisering**och klicka sedan på **Valfri**.

    ![Inställningar för SAML-autentisering](./media/lucidchart-tutorial/ic791192.png "Inställningar för SAML-autentisering")

    b. Skriv domänen i textrutan **Domän** och klicka sedan på **Ändra certifikat**.

    ![Ändra certifikat](./media/lucidchart-tutorial/ic791193.png "Ändra certifikat")

    c. Öppna den nedladdade metadatafilen, kopiera innehållet och klistra sedan in den i textrutan **Ladda upp metadata.**

    ![Ladda upp metadata](./media/lucidchart-tutorial/ic791194.png "Ladda upp metadata")

    d. Välj **Lägg till nya användare automatiskt i gruppen**och klicka sedan på Spara **ändringar**.

    ![Spara ändringar](./media/lucidchart-tutorial/ic791195.png "Spara ändringar")

### <a name="create-lucidchart-test-user"></a>Skapa lucidcharttestanvändare

Det finns inget åtgärdsobjekt som du kan konfigurera användaretablering till Lucidchart.  När en tilldelad användare försöker logga in på Lucidchart med hjälp av åtkomstpanelen kontrollerar Lucidchart om användaren finns.  

Om det inte finns något användarkonto tillgängligt ännu skapas det automatiskt av Lucidchart.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Lucidchart på åtkomstpanelen ska du automatiskt loggas in på den Lucidchart som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Lucidchart med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hur man skyddar Lucidchart med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
