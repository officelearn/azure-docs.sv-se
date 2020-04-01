---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Nintex Promapp | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Nintex Promapp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 581c850801c153996031378cbf470457264cad3d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76984497"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nintex-promapp"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Nintex Promapp

I den här självstudien får du lära dig hur du integrerar Nintex Promapp med Azure Active Directory (Azure AD). När du integrerar Nintex Promapp med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Nintex Promapp.
* Gör att användarna automatiskt loggas in på Nintex Promapp med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Nintex Promapp enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Nintex Promapp stöder **SP och IDP** initierade SSO
* Nintex Promapp stöder just in time-användaretablering **Just In Time**

## <a name="adding-nintex-promapp-from-the-gallery"></a>Lägga till Nintex Promapp från galleriet

Om du vill konfigurera integreringen av Nintex Promapp i Azure AD måste du lägga till Nintex Promapp från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Nintex Promapp** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Nintex Promapp** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nintex-promapp"></a>Konfigurera och testa en azure AD-inloggning för Nintex Promapp

Konfigurera och testa Azure AD SSO med Nintex Promapp med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Nintex Promapp.

Så här konfigurerar och testar du Azure AD SSO med Nintex Promapp:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Nintex Promapp SSO](#configure-nintex-promapp-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Nintex Promapp-testanvändare](#create-nintex-promapp-test-user)** – om du vill ha en motsvarighet till B.Simon i Nintex Promapp som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta [Azure portal](https://portal.azure.com/)reda på avsnittet Hantera på sidan Hantera på sidan **Programintegrering av** **Nintex Promapp** och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    1. Ange en URL i det här mönstret i rutan **Identifierare:**

        | |
        |--|
        | `https://go.promapp.com/TENANTNAME/`|
        | `https://au.promapp.com/TENANTNAME/`|
        | `https://us.promapp.com/TENANTNAME/`|
        | `https://eu.promapp.com/TENANTNAME/`|
        | `https://ca.promapp.com/TENANTNAME/`|
        |   |

       > [!NOTE]
       > Azure AD-integrering med Nintex Promapp är för närvarande endast konfigurerad för tjänstinitierad autentisering. (Det vill än om du går till en Nintex Promapp-URL initierar autentiseringsprocessen.) Men fältet **Svara på URL** är ett obligatoriskt fält.

    1. Ange en URL i det här mönstret i rutan **Svara url:**

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Ange en WEBBADRESS i det här mönstret i rutan **Logga in på webbadress:**`https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE]
    > Dessa värden är platshållare. Du måste använda den faktiska identifieraren, svars-URL:en och inloggnings-URL:en. Kontakta [Nintex Promapps supportteam](https://www.promapp.com/about-us/contact-us/) för att få värdena. Du kan också referera till de mönster som visas i dialogrutan **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera Nintex Promapp.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Nintex Promapp.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Nintex Promapp**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-nintex-promapp-sso"></a>Konfigurera Nintex Promapp SSO

1. Logga in på din webbplats för Nintex Promapp-företag som administratör.

2. Välj **Admin**högst upp i fönstret:

    ![Välj administratör][12]

3. Välj **Konfigurera:**

    ![Välj Konfigurera][13]

4. Gör följande i dialogrutan **Säkerhet.**

    ![Dialogrutan Säkerhet][14]

    1. Klistra in **inloggningsadressen** som du kopierade från Azure-portalen i **url-rutan SSO-inloggning.**

    1. Välj **Valfritt**i listan **SSO - Enkel inloggningsläge** . Välj **Spara**.

       > [!NOTE]
       > Valfritt läge är endast för testning. När du är nöjd med konfigurationen väljer du **Obligatorisk i** listan **SSO - Enkel inloggningsläge** för att tvinga alla användare att autentisera med Azure AD.

    1. Öppna certifikatet som du hämtade i föregående avsnitt i Anteckningar. Kopiera innehållet i intyget utan den första raden **(-----BEGIN-certifikat-----)** eller den sista raden **(-----END-certifikat-----**). Klistra in certifikatinnehållet i rutan **SSO-x.509-certifikat** och välj sedan **Spara**.

### <a name="create-nintex-promapp-test-user"></a>Skapa Nintex Promapp-testanvändare

I det här avsnittet skapas en användare som heter B.Simon i Nintex Promapp. Nintex Promapp stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Nintex Promapp skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Nintex Promapp på åtkomstpanelen ska du automatiskt loggas in på den Nintex Promapp som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Nintex Promapp med Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png