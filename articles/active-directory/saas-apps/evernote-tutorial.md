---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Evernote | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55216e9eb4bd5cda2635564480751459f05e8309
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "71121631"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Evernote

I den här självstudien får du lära dig hur du integrerar Evernote med Azure Active Directory (Azure AD). När du integrerar Evernote med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Evernote.
* Gör det möjligt för användarna att automatiskt loggas in på Evernote med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Evernote enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Evernote har stöd för **SP- och IDP**-initierad enkel inloggning

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-evernote-from-the-gallery"></a>Lägga till Evernote från galleriet

För att konfigurera integrering av Evernote i Azure AD behöver du lägga till Evernote från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Evernote** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Evernote** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Konfigurera och testa en enkel Azure AD-inloggning för Evernote

Konfigurera och testa Azure AD SSO med Evernote med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Evernote.

Om du vill konfigurera och testa Azure AD SSO med Evernote slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Evernote SSO](#configure-evernote-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Evernote-testanvändare](#create-evernote-test-user)** – om du vill ha en motsvarighet till B.Simon i Evernote som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Evernote-program** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    I textrutan **Identifierare** skriver du en URL: `https://www.evernote.com/saml2`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i textrutan **Sign-on-URL:**`https://www.evernote.com/Login.action`

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. Om du vill ändra **signeringsalternativen** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat.**

    ![image](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Välj alternativet **Signera SAML-svar och kontroll** för **signeringsalternativ**.

    b. Klicka på **Spara**

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera Evernote.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Evernote.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **Evernote**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-evernote-sso"></a>Konfigurera Evernote SSO

1. Om du vill automatisera konfigurationen i Evernote måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren, klicka på **setup Evernote** kommer att leda dig till Evernote ansökan. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Evernote. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-6.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Evernote manuellt öppnar du ett nytt webbläsarfönster och loggar in på evernote-företagets webbplats som administratör och utför följande steg:

4. Gå till **”Administratörskonsolen”**

    ![Administratörskonsolen](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. Från **”Administratörskonsolen”** går du till **”Säkerhet”** och väljer **”Enkel inloggning”**

    ![Inställning för enkel inloggning](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Konfigurera följande värden:

    ![Inställning för certifikat](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Aktivera SSO:** SSO är aktiverat som standard (klicka på Inaktivera enkel inloggning för att ta bort **SSO-kravet)**

    b. Klistra in det värde för **inloggnings-URL** som du har kopierat från Azure-portalen i textrutan **SAML HTTP Request URL** (HTTP-begärande-URL för SAML).

    c. Öppna det certifikat som laddats ned från Azure AD i Anteckningar, kopiera innehållet, inklusive ”BEGIN CERTIFICATE” (början på certifikatet) och ”END CERTIFICATE” (slutet på certifikatet) och klistra in det i textrutan **X.509-certifikat**. 

    d. Klicka på **Spara ändringar**

### <a name="create-evernote-test-user"></a>Skapa Evernote testanvändare

För att Azure AD-användare ska kunna logga in på Evernote måste de etableras i Evernote.  
När det gäller Evernote är etablering en manuell uppgift.

**Utför följande steg för att tillhandahålla ett användarkonto:**

1. Logga in på Evernote-företagets webbplats som administratör.

2. Klicka på **”Administratörskonsolen”**.

    ![Administratörskonsolen](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Från **”Administratörskonsolen”** går du till **”Lägg till användare”**.

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0001.png)

4. **Lägg till gruppmedlemmar** i textrutan **E-post**, skriv e-postadressen för användarkontot och klicka på **Bjud in.**

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. När inbjudan har skickats får Azure Active Directory-kontoinnehavaren ett e-postmeddelande för att tacka ja till inbjudan.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Evernote-panelen på åtkomstpanelen bör du automatiskt loggas in i Evernote som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Evernote med Azure AD](https://aad.portal.azure.com/)

