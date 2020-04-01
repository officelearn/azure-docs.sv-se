---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Litmos | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Litmos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a935ef6d14b4de67964c555e0ffa610bbe992459
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "70171573"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmos"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Litmos

I den här självstudien får du lära dig hur du integrerar Litmos med Azure Active Directory (Azure AD). När du integrerar Litmos med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Litmos.
* Gör att användarna automatiskt loggas in på Litmos med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Litmos enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Litmos stöder **IDP**-initierad enkel inloggning
* Litmos stöder **JIT**-användaretablering (Just-In-Time)

## <a name="adding-litmos-from-the-gallery"></a>Lägga till Litmos från galleriet

För att konfigurera integreringen av Litmos med Azure AD måste du lägga till Litmos från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Litmos** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Litmos** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-litmos"></a>Konfigurera och testa en azure AD-inloggning för Litmos

Konfigurera och testa Azure AD SSO med Litmos med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Litmos.

Så här konfigurerar och testar du Azure AD SSO med Litmos:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Litmos SSO](#configure-litmos-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Litmos-testanvändare](#create-litmos-test-user)** – om du vill ha en motsvarighet till B.Simon i Litmos som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Litmos** Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<companyname>.litmos.com/account/Login`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL:en, som beskrivs senare i självstudien, eller kontakta [Litmos kundsupport](https://www.litmos.com/contact-us) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Litmos** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Litmos.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Litmos** i listan med program.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-litmos-sso"></a>Konfigurera Litmos SSO

1. Öppna ett nytt webbläsarfönster och logga in på din Litmos-företagswebbplats som administratör.

2. Klicka på **Konton** i navigeringsfältet till vänster.

    ![Avsnittet Konton på appsidan][22]

3. Klicka på fliken **Integreringar**.

    ![Fliken Integrering][23]

4. Rulla ned till **Tredjepartsintegrationer** på fliken **Integrationer** och klicka på fliken **SAML 2.0**.

    ![Avsnittet SAML 2.0][24]

5. Kopiera värdet under **SAML-slutpunkten för Litmos är:** och klistra in det i textrutan **Svars-URL** i avsnittet **Litmos-domän och -webbadresser** på Azure-portalen.

    ![SAML-slutpunkt][26]

6. Utför följande steg i **Litmos**-programmet:

    ![Litmos-program][25]

    a. Klicka på **Aktivera SAML**.

    b. Öppna ditt base-64-kodade certifikat i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in det i textrutan **SAML X.509-certifikat**.

    c. Klicka på **Spara ändringar**.

### <a name="create-litmos-test-user"></a>Skapa Litmos-testanvändare

Målet med det här avsnittet är att skapa en användare med namnet Britta Simon i Litmos. Litmos-programmet stöder JIT-etablering (Just-in-Time). Det innebär att ett användarkonto skapas automatiskt om det behövs vid ett försök att komma åt programmet via åtkomstpanelen.

**Skapa en användare med namnet Britta Simon i Litmos genom att utföra följande steg:**

1. Öppna ett nytt webbläsarfönster och logga in på din Litmos-företagswebbplats som administratör.

2. Klicka på **Konton** i navigeringsfältet till vänster.

    ![Avsnittet Konton på appsidan][22]

3. Klicka på fliken **Integreringar**.

    ![Fliken Integrationer][23]

4. Rulla ned till **Tredjepartsintegrationer** på fliken **Integrationer** och klicka på fliken **SAML 2.0**.

    ![SAML 2.0][24]

5. Välj **Skapa användare automatiskt**.
  
    ![Skapa användare automatiskt][27]

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Litmos-panelen på åtkomstpanelen bör du automatiskt loggas in i Litmos-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Litmos med Azure AD](https://aad.portal.azure.com/)

[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png