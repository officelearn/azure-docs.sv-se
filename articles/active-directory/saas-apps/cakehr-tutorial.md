---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med CakeHR | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och CakeHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c9bbdb1-ac47-4fb8-a1cc-1e647b0323a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0860411c95e48a16d75df4aeeedf3405a5b1835
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72595026"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cakehr"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med CakeHR

I den här självstudien får du lära dig hur du integrerar CakeHR med Azure Active Directory (Azure AD). När du integrerar CakeHR med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till CakeHR.
* Gör att användarna automatiskt loggas in på CakeHR med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Abonnemanget för enkel inloggning (SSO) med 2000-onsdagar har aktiverats.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* CakeHR stöder **SP** initierade SSO

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-cakehr-from-the-gallery"></a>Lägga till CakeHR från galleriet

Om du vill konfigurera integreringen av CakeHR i Azure AD måste du lägga till CakeHR från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **CakeHR** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **CakeHR** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cakehr"></a>Konfigurera och testa en enda Azure AD-inloggning för CakeHR

Konfigurera och testa Azure AD SSO med CakeHR med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i CakeHR.

Så här konfigurerar och testar du Azure AD SSO med CakeHR:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera CakeHR SSO](#configure-cakehr-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa CakeHR-testanvändare](#create-cakehr-test-user)** – om du vill ha en motsvarighet till B.Simon i CakeHR som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. På [Azure portal](https://portal.azure.com/)sidan Integrering av CakeHR-program på sidan **CakeHR-program** hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<yourcakedomain>.cake.hr/`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<yourcakedomain>.cake.hr/services/saml/consume`
    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med en faktisk inloggnings-URL och svars-URL. Kontakta [CakeHR Client supportteam](mailto:info@cake.hr) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. Kopiera **tumavtrycksvärdet** i avsnittet **SAML-signeringscertifikat** och spara det på anteckningarna.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera CakeHR** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till CakeHR.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **CakeHR**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-cakehr-sso"></a>Konfigurera CakeHR SSO

1. Om du vill automatisera konfigurationen i CakeHR måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

1. När du har lagt till tillägget i webbläsaren, klicka på **Ställ in CakeHR** kommer att hänvisa dig till CakeHR ansökan. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på CakeHR. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-5.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

1. Om du vill konfigurera CakeHR manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Tårttimmars företagswebbplats som administratör och utför följande steg:

1. Klicka på **Profil** längst upp till höger på sidan och navigera sedan till **Inställningar**.

    ![CakeHR-konfiguration](./media/cakehr-tutorial/config01.png)

1. Från menyns vänstra sida klickar du på **INTEGRATIONS** > **SAML SSO** och utför följande steg:

    ![CakeHR-konfiguration](./media/cakehr-tutorial/config02.png)

    a. Skriv `cake.hr`i textrutan **Entitets-ID** .

    b. Klistra in värdet **för inloggnings-URL**i textrutan **Autentiserings-URL** som du har kopierat från Azure-portalen.

    c. Klistra in **tumavtrycksvärdet** som du har kopierat från Azure-portalen i textrutan **Nyckelfingeravtryck (SHA1-format).**

    d. Markera rutan **Aktivera enkel inloggning.**

    e. Klicka på **Spara**.

### <a name="create-cakehr-test-user"></a>Skapa CakeHR-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på CakeHR måste de etableras i CakeHR. I CakeHR är etablering en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på CakeHR som säkerhetsadministratör.

2. Klicka på **FÖRETAG** > **LÄGG**TILL från menyns vänstra sida.

    ![CakeHR-konfiguration](./media/cakehr-tutorial/config03.png)

3. Gör följande i popup-fönstret **Lägg till ny medarbetare:**

     ![CakeHR-konfiguration](./media/cakehr-tutorial/config04.png)

    a. I textrutan **Fullständigt namn** anger du namnet på användaren som B.Simon.

    b. I textrutan **Arbete via e-post** anger du e-postmeddelandet för användaren som `B.Simon@contoso.com`.

    c. Klicka på **SKAPA KONTO**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på CakeHR-panelen i åtkomstpanelen ska du automatiskt loggas in på den CakeHR som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova CakeHR med Azure AD](https://aad.portal.azure.com/)
