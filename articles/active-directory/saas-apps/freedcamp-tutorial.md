---
title: 'Självstudiekurs: Azure Active Directory-integrering med Freedcamp | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Freedcamp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: bfc73563-017d-458f-b634-162f93e03b74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de1ae135df4cd070fe9c6ee322e304caa1e17c9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67101919"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Självstudiekurs: Integrera Freedcamp med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Freedcamp med Azure Active Directory (Azure AD). När du integrerar Freedcamp med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Freedcamp.
* Gör att användarna automatiskt loggas in på Freedcamp med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Freedcamp enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. Freedcamp stöder **SP och IDP** initierade SSO.

## <a name="adding-freedcamp-from-the-gallery"></a>Lägga till Freedcamp från galleriet

Om du vill konfigurera integreringen av Freedcamp i Azure AD måste du lägga till Freedcamp från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Freedcamp** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Freedcamp** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Freedcamp med en testanvändare som heter **Britta Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Freedcamp.

Om du vill konfigurera och testa Azure AD SSO med Freedcamp slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera Freedcamp](#configure-freedcamp)** för att konfigurera SSO-inställningarna på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** så att Britta Simon kan använda azure AD-enkel inloggning.
5. **[Skapa Freedcamp-testanvändare](#create-freedcamp-test-user)** för att ha en motsvarighet till Britta Simon i Freedcamp som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    1. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Användare kan också ange url-värdena med avseende på sin egen `freedcamp.com`kunddomän och de kanske inte nödvändigtvis av mönstret , de kan ange alla kunddomänspecifika värde, specifika för deras programinstans. Du kan också kontakta [Freedcamp Client supportteam](mailto:devops@freedcamp.com) för ytterligare information om url mönster.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Freedcamp** baserat på dina krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Konfigurera Freedcamp

1. Om du vill automatisera konfigurationen i Freedcamp måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren, klicka på **Setup Freedcamp** kommer att leda dig till Freedcamp ansökan. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Freedcamp. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-5.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Freedcamp manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Freedcamp-företagswebbplats som administratör och utför följande steg:

4. Klicka på **profilen** längst upp till höger och navigera sedan till **Mitt konto**.

    ![Konfiguration av Freedcamp](./media/freedcamp-tutorial/config01.png)

5. Från menyns vänstra sida klickar du på **SSO** och på sidan **SSO-anslutningar** utför du följande steg:

    ![Konfiguration av Freedcamp](./media/freedcamp-tutorial/config02.png)

    a. Skriv rubriken i textrutan **Rubrik.**

    b. I textrutan **Entitets-ID** klistrar du in azure **AD-identifierare,** som du har kopierat från Azure-portalen.

    c. I textrutan **Inloggningsadress** klistrar du in värdet **för inloggningsadressen,** som du har kopierat från Azure-portalen.

    d. Öppna Base64-kodat certifikat i anteckningar, kopiera innehållet och klistra in det i textrutan **Certifikat.**

    e. Klicka på **Skicka**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter Britta Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Freedcamp.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Freedcamp**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-freedcamp-test-user"></a>Skapa Freedcamp-testanvändare

Om du vill aktivera Azure AD-användare loggar du in på Freedcamp, de måste etableras i Freedcamp. I Freedcamp är etablering en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. I ett annat webbläsarfönster loggar du in på Freedcamp som säkerhetsadministratör.

2. I det övre hörnet av sidan klickar du på **profilen** och navigerar sedan till **Manage System**.

    ![Konfiguration av Freedcamp](./media/freedcamp-tutorial/config03.png)

3. Gör följande på höger sida av sidan Hantera system:

    ![Konfiguration av Freedcamp](./media/freedcamp-tutorial/config04.png)

    a. Klicka på **Lägg till eller bjud in användare**.

    b. I textrutan **E-post** anger du `Brittasimon@contoso.com`e-postmeddelandet för användaren som .

    c. Klicka på **Lägg till användare**.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Freedcamp på åtkomstpanelen ska du automatiskt loggas in på den Freedcamp som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)