---
title: 'Självstudiekurs: Azure Active Directory-integrering med Appraisd | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08453928ab000cf906c451fa6c1cd619a00ee4ca
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67561206"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Självstudiekurs: Integrera appraisd med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Appraisd med Azure Active Directory (Azure AD). När du integrerar Appraisd med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Appraisd.
* Gör att användarna automatiskt loggas in på Appraisd med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Utvärderad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. Appraisd stöder **SP och IDP** initierade SSO.

## <a name="adding-appraisd-from-the-gallery"></a>Lägga till Appraisd från galleriet

För att konfigurera integrering av Appraisd i Azure AD måste du lägga till Appraisd från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Appraisd** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Appraisd** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Appraisd med en testanvändare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Appraisd.

Om du vill konfigurera och testa Azure AD SSO med Appraisd slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera appraisd](#configure-appraisd)** för att konfigurera SSO-inställningarna på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B. Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B. Simon för att använda Azure AD enkel inloggning.
5. **[Skapa appraisd testanvändare](#create-appraisd-test-user)** att ha en motsvarighet till B. Simon i Appraisd som är kopplad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Appraisd** Hantera på sidan Hantera på sidan [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**. **Manage**
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat och nödvändiga url:er är redan förifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen Spara och utföra följande steg:

    a. Klicka på **Ange ytterligare URL:er**.

    b. Skriv en URL i textrutan **Vidarebefordransstatus**: `<TENANTCODE>`

    c. Om du vill konfigurera programmet i **SP**-initierat läge skriver du en URL med hjälp av följande mönster i textrutan **Inloggnings-URL**: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Du får det faktiska värdet för inloggnings-URL och värdet för vidarebefordransstatus på konfigurationssidan för Appraisd SSO som beskrivs senare i självstudien.

1. Appraisd-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar till konfigurationen av SAML-tokenattribut. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Appraisd ansökan förväntar **namnidentifierare** som ska mappas med **user.mail**, så du måste redigera attributmappningen genom att klicka på **Redigera** ikonen och ändra attributmappningen.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera appraisd** baserat på dina krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Konfigurera appraisd

1. Om du vill automatisera konfigurationen i Appraisd måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren, klicka på **Setup Appraisd** kommer att leda dig till Appraisd ansökan. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på Appraisd. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Appraisd manuellt öppnar du ett nytt webbläsarfönster och loggar in på din webbplats för appraisd som administratör och utför följande steg:

4. Längst upp till höger på sidan klickar du på **ikonen Inställningar** och navigerar sedan till **Konfiguration**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. På vänster sida av menyn klickar du på **Enkel inloggning med SAML**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. På sidan för **konfiguration av enkel inloggning för SAML 2.0** utför du följande steg:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Kopiera **värdet för standardrelätillstånd** och klistra in det i relay **state-textrutan** i grundläggande **SAML-konfiguration** på Azure-portalen.

    b. Kopiera värdet **för tjänstinitierad inloggnings-URL** och klistra in det i textrutan Sign-on URL i **grundläggande SAML-konfiguration** på **Azure-portalen.**

7. Rulla nedåt på samma sida under **Identifiera användare** och utför följande steg:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. I textrutan **Identity Provider Single Sign-On URL** (URL för enkel inloggning för identitetsprovider) klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen och klickar på **Spara**.

    b. Klistra in värdet för **Azure AD-identifierare**i **url-textrutan för identitetsprovider** och klicka på **Spara**.

    c. Öppna det bas-64-kodade certifikat som du hämtade från Azure-portalen i Anteckningar, kopiera innehållet och klistra in det sedan i rutan **X.509-certifikat** och klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B. Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B. Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Appraisd.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I listan med program väljer **Appraisd**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B. Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-appraisd-test-user"></a>Skapa Appraisd-testanvändare

För att Azure AD-användare ska kunna logga in på Appraisd måste de etableras till Appraisd. I Appraisd är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Appraisd som administratör.

2. Längst upp till höger på sidan klickar du på **ikonen Inställningar** och navigerar sedan till **Administrationscenter**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Klicka på **Kontakter**högst upp på sidan och navigera sedan till Lägg till **en ny användare**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Utför följande steg i rutan **Lägg till en ny användare**:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. I textrutan **Förnamn** anger du förnamnet på användaren som **Britta**.

    b. I textrutan **Efternamn** anger du efternamn för användare som **simon**.

    c. I textrutan **E-post** anger du användarens e-postadress som `B. Simon@contoso.com`.

    d. Klicka på **Lägg till användare**.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Appraisd på åtkomstpanelen ska du automatiskt loggas in på den appraisd som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
