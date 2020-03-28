---
title: 'Självstudiekurs: Azure Active Directory-integrering med Chargebee | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Chargebee.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 033d413d-1656-4d9c-a606-dd33c23948f9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2bbaf3d527ad1e58914c6b3f9c8b5b4ea57ae08
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68931858"
---
# <a name="tutorial-integrate-chargebee-with-azure-active-directory"></a>Självstudiekurs: Integrera Chargebee med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Chargebee med Azure Active Directory (Azure AD). När du integrerar Chargebee med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Chargebee.
* Gör att användarna automatiskt loggas in på Chargebee med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Chargebee enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Chargebee stöder **SP och IDP** initierade SSO

## <a name="adding-chargebee-from-the-gallery"></a>Lägga till Chargebee från galleriet

Om du vill konfigurera integreringen av Chargebee i Azure AD måste du lägga till Chargebee från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Chargebee** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Chargebee** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-chargebee"></a>Konfigurera och testa azure AD enkel inloggning för Chargebee

Konfigurera och testa Azure AD SSO med Chargebee med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Chargebee.

Så här konfigurerar och testar du Azure AD SSO med Chargebee:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
2. **[Konfigurera Chargebee SSO](#configure-chargebee-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Chargebee-testanvändare](#create-chargebee-test-user)** - om du vill ha en motsvarighet till B.Simon i Chargebee som är länkad till Azure AD-representationen av användaren.
3. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<domainname>.chargebee.com`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://app.chargebee.com/saml/<domainname>/acs`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<domainname>.chargebee.com`

    > [!NOTE]
    > `<domainname>`är namnet på den domän som användaren skapar efter att ha gjort anspråk på kontot. Vid annan information, kontakta [Chargebee Client support team](mailto:support@chargebee.com). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera Chargebee.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Chargebee.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Chargebee**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-chargebee-sso"></a>Konfigurera Chargebee SSO

1. Öppna ett nytt webbläsarfönster och logga in på chargebee-företagets webbplats som administratör.

4. Klicka på **Inställningar** > **Säkerhetshantering** > **Manage**från menyns vänstra sida.

    ![Konfiguration av Chargebee](./media/chargebee-tutorial/config01.png)

5. Gör följande på **popup-fönstret Enkel** inloggning:

    ![Konfiguration av Chargebee](./media/chargebee-tutorial/config02.png)

    a. Välj **SAML**.

    b. I textrutan **Inloggningsadress** klistrar du in värdet **för inloggningsadressen,** som du har kopierat från Azure-portalen.

    c. Öppna Base64-kodat certifikat i anteckningar, kopiera innehållet och klistra in det i textrutan **SAML-certifikat.**

    d. Klicka på **Bekräfta**.

### <a name="create-chargebee-test-user"></a>Skapa Chargebee-testanvändare

Om du vill aktivera Azure AD-användare loggar du in på Chargebee, de måste etableras i Chargebee. I Chargebee är etablering en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. I ett annat webbläsarfönster loggar du in på Chargebee som säkerhetsadministratör.

2. Från menyns vänstra sida klickar du på **Kunder** och navigerar sedan till **Skapa en ny kund.**

    ![Konfiguration av Freedcamp](./media/chargebee-tutorial/config03.png)

3. På sidan **Ny kund** fyller du i respektive fält som visas nedan och klickar på **Skapa kund** för att skapa användare.

    ![Konfiguration av Freedcamp](./media/chargebee-tutorial/config04.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Chargebee-panelen på åtkomstpanelen ska du automatiskt loggas in på den Chargebee som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

