---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Freshservice | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e08ef72dca09f873ad1cfcc91e132063b88406b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227522"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Freshservice

I den här självstudien får du lära dig hur du integrerar Freshservice med Azure Active Directory (Azure AD). När du integrerar Freshservice med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Freshservice.
* Gör att användarna automatiskt loggas in på Freshservice med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Ny tjänst enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Freshservice stöder **IDP**-initierad enkel inloggning

## <a name="adding-freshservice-from-the-gallery"></a>Lägga till Freshservice från galleriet

För att konfigurera integreringen av Freshservice till Azure AD behöver du lägga till Freshservice från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Freshservice** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Freshservice** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>Konfigurera och testa en azure AD-inloggning för Freshservice

Konfigurera och testa Azure AD SSO med Freshservice med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Freshservice.

Så här konfigurerar och testar du Azure AD SSO med Freshservice:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Freshservice SSO](#configure-freshservice-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Freshservice-testanvändare](#create-freshservice-test-user)** – om du vill ha en motsvarighet till B.Simon i Freshservice som är länkad till Azure AD-representationen för användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<democompany>.freshservice.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Hämta dessa värden genom att kontakta [supportteamet för Freshservice-klienten](https://support.freshservice.com/). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Freshservice kräver SHA-256 fingeravtryck för att få SSO att fungera. För att få SHA-256 fingeravtryck, utför följande steg:

    ![Fingeravtryck](./media/freshservice-tutorial/ic790821.png "Fingeravtryck")

    1. Öppna [länken](https://www.samltool.com/fingerprint.php) i olika webbläsare.

    1. Öppna filen Laddat certifikat (Base64) i Anteckningar och klistra in innehåll i textrutan **X.509 cert.**

    1. För algoritmen väljer du **sha256** i listrutan.

    1. Klicka på **BERÄKNA FINGERAVTRYCK**.

    1. Klicka på kopieringsikonen för att kopiera det genererade **FingerPrint** och spara det på datorn.

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera Freshservice** på **Azure-portalen.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Freshservice.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **Freshservice**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-freshservice-sso"></a>Konfigurera Freshservice SSO

1. Om du vill automatisera konfigurationen i Freshservice måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren, klicka på **Setup Freshservice** kommer att leda dig till Freshservice ansökan. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Freshservice. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-6.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Freshservice manuellt öppnar du ett nytt webbläsarfönster och loggar in på webbplatsen för Freshservice-företaget som administratör och utför följande steg:

4. På menyn längst upp klickar du på **Admin**.

    ![Administratör](./media/freshservice-tutorial/ic790814.png "Admin")

5. I **kundportalen** klickar du på **Säkerhet**.

    ![Säkerhet](./media/freshservice-tutorial/ic790815.png "Säkerhet")

6. Gör följande i avsnittet **Säkerhet:**

    ![Enkel inloggning](./media/freshservice-tutorial/ic790816.png "Enkel inloggning")

    a. Växla **Enkel inloggning**.

    b. Välj **SAML SSO**.

    c. I textrutan för **inloggnings-URL för SAML** klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    d. Klistra in värdet för url för **utloggning**i textrutan **Logout** URL , som du har kopierat från Azure-portalen.

    e. I textrutan Fingeravtryck i **säkerhetscertifikat** klistrar du in **fingertrycksvärdet,** som du har genererat tidigare.

    f. Klicka på **Spara**

### <a name="create-freshservice-test-user"></a>Skapa Freshservice-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på FreshService måste de etableras i FreshService. När det gäller FreshService är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **FreshService-företagswebbplats** som administratör.

2. På menyn längst upp klickar du på **Admin**.

    ![Administratör](./media/freshservice-tutorial/ic790814.png "Admin")

3. I avsnittet **Användarhantering** klickar du på **Beställare**.

    ![Beställare](./media/freshservice-tutorial/ic790818.png "Beställare")

4. Klicka på **Ny beställare**.

    ![Nya beställare](./media/freshservice-tutorial/ic790819.png "Nya beställare")

5. I avsnittet **Ny beställare** utför du följande steg:

    ![Ny beställare](./media/freshservice-tutorial/ic790820.png "Ny beställare")  

    a. Ange attributen **Förnamn** och **E-post** för ett giltigt Azure Active Directory-konto som du vill etablera i de relaterade textrutorna.

    b. Klicka på **Spara**.

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt
    >  

> [!NOTE]
> Du kan använda andra verktyg eller API:er för att skapa nya Konton för FreshService för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Freshservice-panelen i åtkomstpanelen bör du automatiskt loggas in på Freshservice som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Freshservice med Azure AD](https://aad.portal.azure.com/)