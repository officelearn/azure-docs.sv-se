---
title: 'Självstudier: Azure Active Directory enkel inloggning (SSO) med slack | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c2d877a1dc611e02e9fbc245df230ca669a2ae4
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70171433"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Självstudier: Azure Active Directory enkel inloggning (SSO) med slack

I den här självstudien får du lära dig hur du integrerar slack med Azure Active Directory (Azure AD). När du integrerar slack med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till slack.
* Gör det möjligt för användarna att logga in automatiskt till slack med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Aktiverings prenumeration med enkel inloggning (SSO) för slack.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Slack har stöd för **SP**-initierad enkel inloggning
* Slack stöder **just-in-time**-användaretablering
* Slack har stöd för [**automatisk** användaretablering](https://docs.microsoft.com/en-gb/azure/active-directory/saas-apps/slack-provisioning-tutorial)

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-slack-from-the-gallery"></a>Lägga till Slack från galleriet

För att kunna konfigurera integreringen av Slack i Azure AD måste du lägga till Slack från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **slack** i sökrutan.
1. Välj **slack** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-slack"></a>Konfigurera och testa enkel inloggning med Azure AD för slack

Konfigurera och testa Azure AD SSO med slack med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i slack.

Om du vill konfigurera och testa Azure AD SSO med slack slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -testuser-för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SLACK SSO](#configure-slack-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa slack-test användare](#create-slack-test-user)** – om du vill ha en motsvarighet till B. Simon i slack som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för **slack** -programmets integrering och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.slack.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL: `https://slack.com`

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Slack-kundsupporten](https://slack.com/help/contact) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. I avsnittet **Konfigurera slack** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till slack.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **Slack** i programlistan.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-slack-sso"></a>Konfigurera slack SSO

1. Logga in på ditt slack-företags webbplats som administratör i ett annat webbläsarfönster.

2. Gå till **Microsoft Azure AD** och sedan till **Teaminställningar**.

     ![Konfigurera enkel inloggning på App-sida](./media/slack-tutorial/tutorial_slack_001.png)

3. I avsnittet **Teaminställningar** klickar du på fliken **Autentisering** och sedan på **Ändra inställningar**.

    ![Konfigurera enkel inloggning på App-sida](./media/slack-tutorial/tutorial_slack_002.png)

4. Gör följande i dialogrutan **Inställningar för SAML-autentisering**:

    ![Konfigurera enkel inloggning på App-sida](./media/slack-tutorial/tutorial_slack_003.png)

    a.  I textrutan **SAML 2.0 Endpoint (HTTP)** (SAML 2.0-slutpunkt (HTTP)) klistrar du in värdet för **inloggnings-URL:en**, som du har kopierat från Azure-portalen.

    b.  I textrutan **Identity Provider Issuer** (Utfärdare av identitetsprovider) klistrar du in värdet för **Azure Ad-identifierare**, som du har kopierat från Azure-portalen.

    c.  Öppna den nedladdade certifikatfilen i Anteckningar, kopiera innehållet i den i Urklipp och klistra in den i textrutan **Offentligt certifikat**.

    d. Konfigurera de tre inställningarna ovan efter behov för ditt Slack-team. Mer information om inställningarna finns i **Slacks konfigurationsguide för enkel inloggning** här. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Klicka på **Spara konfiguration**.

### <a name="create-slack-test-user"></a>Skapa Slack-testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i slack. Slack har stöd för just-in-time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas vid ett försök att få åtkomst till Slack, om den inte redan finns. Slack stöder även automatisk användaretablering. Mer information finns [här](slack-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta [Slack-supporten](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect är synkroniseringsprogrammet som kan synkronisera lokala Active Directory identiteter till Azure AD och dessa synkroniserade användare kan också använda programmen precis som andra moln användare.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Slack-panelen i åtkomstpanelen bör du automatiskt loggas in på Slack som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova slack med Azure AD](https://aad.portal.azure.com/)