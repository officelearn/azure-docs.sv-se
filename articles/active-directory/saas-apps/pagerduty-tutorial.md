---
title: 'Självstudiekurs: Azure Active Directory-integrering med PagerDuty | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 99d9988a02b046562c2517df8a81b8ef5c778ef2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683518"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med PagerDuty

I den här självstudien får du lära dig hur du integrerar PagerDuty med Azure Active Directory (Azure AD). När du integrerar PagerDuty med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till PagerDuty.
* Gör att användarna automatiskt loggas in på PagerDuty med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* PagerDuty enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* PagerDuty stöder **SP** initierade SSO
* När du har konfigurerat PagerDuty kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-pagerduty-from-the-gallery"></a>Lägga till PagerDuty från galleriet

Om du vill konfigurera integreringen av PagerDuty i Azure AD måste du lägga till PagerDuty från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **PagerDuty** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **PagerDuty** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>Konfigurera och testa en azure AD-inloggning för PagerDuty

Konfigurera och testa Azure AD SSO med PagerDuty med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i PagerDuty.

Om du vill konfigurera och testa Azure AD SSO med PagerDuty slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera PagerDuty SSO](#configure-pagerduty-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa PagerDuty-testanvändare](#create-pagerduty-test-user)** – om du vill ha en motsvarighet till B.Simon i PagerDuty som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta [Azure portal](https://portal.azure.com/)reda på avsnittet Hantera på sidan Hantera på sidan PagerDuty-program och välj enkel inloggning på sidan **PagerDuty-programintegration** . **Manage** **single sign-on**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenant-name>.pagerduty.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenant-name>.pagerduty.com`

    c. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL, identifierare och svars-URL. Kontakta [PagerDuty Client support team](https://www.pagerduty.com/support/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera PagerDuty.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till PagerDuty.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **PagerDuty**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-pagerduty-sso"></a>Konfigurera PagerDuty SSO

1. I ett annat webbläsarfönster loggar du in på din Pagerduty-företagswebbplats som administratör.

2. Klicka på **Kontoinställningar**högst upp.

    ![Kontoinställningar](./media/pagerduty-tutorial/ic778535.png "Kontoinställningar")

3. Klicka **på Enkel inloggning**.

    ![Enkel inloggning](./media/pagerduty-tutorial/ic778536.png "Enkel inloggning")

4. Gör följande på sidan **Aktivera enkel inloggning (SSO):**

    ![Aktivera enkel inloggning](./media/pagerduty-tutorial/ic778537.png "Aktivera enkel inloggning")

    a. Öppna ditt bas-64-kodade certifikat som hämtats från Azure-portalen i anteckningar, kopiera innehållet i det i Urklipp och klistra sedan in det i textrutan **X.509 Certifikat**
  
    b. I textrutan för **inloggnings-URL** klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.
  
    c. I textrutan **Logout URL** (Utloggnings-URL) klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    d. Välj **Tillåt inloggning av användarnamn/lösenord**.

    e. Välj **Kryssrutan Kräv exakt autentiseringskontextjämn.**

    f. Klicka på **Spara ändringar**.

### <a name="create-pagerduty-test-user"></a>Skapa PagerDuty-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på PagerDuty måste de etableras i PagerDuty. När det gäller PagerDuty är etablering en manuell aktivitet.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för att skapa sidortjed för att etablera Azure Active Directory-användarkonton.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **Pagerduty-klient.**

2. Klicka på **Användare**i menyn högst upp .

3. Klicka på **Lägg till användare**.
   
    ![Lägg till användare](./media/pagerduty-tutorial/ic778539.png "Lägg till användare")

4.  Gör följande i dialogrutan **Bjud in ditt team:**
   
    ![Bjud in ditt team](./media/pagerduty-tutorial/ic778540.png "Bjud in ditt team")

    a. Skriv **för- och efternamn** för användare som **B.Simon**. 
   
    b. Ange **E-postadress** för användare som **\@b.simon contoso.com**.
   
    c. Klicka på **Lägg till**och sedan på **Skicka inbjudningar**.
   
    > [!NOTE]
    > Alla tillagda användare får en inbjudan om att skapa ett PagerDuty-konto.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen PagerDuty på åtkomstpanelen ska du automatiskt loggas in på den PagerDuty som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova PagerDuty med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du PagerDuty med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

