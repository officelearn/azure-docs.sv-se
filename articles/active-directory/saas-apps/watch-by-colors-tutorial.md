---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Watch by Colors | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Watch by Colors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 586a029c-fb8d-4233-b280-103b9ba7102d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d8fdc8ba2337a3be49a4645e48a45120fb0ccec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026088"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Watch by Colors

I den här självstudien får du lära dig hur du integrerar Watch by Colors med Azure Active Directory (Azure AD). När du integrerar Watch by Colors med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Watch by Colors.
* Gör att användarna automatiskt loggas in på Watch by Colors med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Titta efter färger enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Watch by Colors stöder **SP och IDP** initierad SSO

## <a name="adding-watch-by-colors-from-the-gallery"></a>Lägga till titta efter färger från galleriet

Om du vill konfigurera integreringen av Watch by Colors i Azure AD måste du lägga till Watch by Colors från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Titta efter färger** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **Titta efter färger** på resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Konfigurera och testa Azure AD enkel inloggning för Watch by Colors

Konfigurera och testa Azure AD SSO med Watch by Colors med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Watch by Colors.

Om du vill konfigurera och testa Azure AD SSO med Watch by Colors slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Watch by Colors SSO](#configure-watch-by-colors-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa bevakning efter färger testanvändare](#create-watch-by-colors-test-user)** - att ha en motsvarighet till B.Simon i Watch by Colors som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan Programintegrering av [Watch](https://portal.azure.com/)by Colors på sidan Programintegrering av **Bevakning** och välj enkel **inloggning**. **Manage**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat i **IDP-initierat** läge och nödvändiga url:er är redan förifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på **knappen Spara.**

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i textrutan **Sign-on-URL:**`https://app.colorscorporation.com/login`

1. Klicka på knappen Kopiera i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** för att kopiera url till App **Federationsmetadata** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Watch by Colors.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj Titta efter **färger**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-watch-by-colors-sso"></a>Konfigurera bevakning efter färger SSO

1. Om du vill automatisera konfigurationen i Watch by Colors måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren, klicka på **setup Watch by Colors** kommer att leda dig till Watch by Colors ansökan. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på Watch by Colors. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-5.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Watch by Colors manuellt öppnar du ett nytt webbläsarfönster och loggar in på företagets webbplats Watch by Colors som administratör och utför följande steg:

4. Klicka på**sSO**för > **profilkontoinställningar** > längst upp till höger på sidan . **profile**

    ![Konfigurationen Titta efter färger](./media/watch-by-colors-tutorial/config01.png)

5. Gör följande på sidan **SSO (Single Sign On):**

    ![Konfigurationen Titta efter färger](./media/watch-by-colors-tutorial/config02.png)

    a. Växla **Aktivera SAML** till **ON**.

    b. Klistra in **url-url-url:en**i textrutan **URL** som du har kopierat från Azure-portalen.

    c. Klicka på **Importera**och fylls i automatiskt på sidan i följande fält.

    d. Klicka på **Spara**.

### <a name="create-watch-by-colors-test-user"></a>Testa användaren Skapa bevakning efter färger

Om du vill att Azure AD-användare ska kunna logga in på Watch by Colors måste de etableras i Watch by Colors. I Watch by Colors är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in för att titta efter färger som säkerhetsadministratör.

1. Klicka på **profil** > **Användare** > **Lägg till användare**längst upp till höger på sidan.

    ![Konfigurationen Titta efter färger](./media/watch-by-colors-tutorial/config03.png)

1. Gör följande på sidan **Användarinformation:**

    ![Konfigurationen Titta efter färger](./media/watch-by-colors-tutorial/config04.png)

    a. I textrutan **Förnamn** anger du förnamnet för användaren som **B**.

    b. I textrutan **Efternamn** anger du efternamn på användaren som **Simon**.

    c. I textrutan **E-post** anger du användarens e-postadress som `B.Simon@contoso.com`.

    d. Ange lösenordet i textrutan **Lösenord.**

    e. Välj **Kontobehörigheter** enligt din organisation.

    f. Klicka på **Spara**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Titta efter färger på åtkomstpanelen bör du automatiskt loggas in på den bevakning av färger som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova titta efter färger med Azure AD](https://aad.portal.azure.com/)

