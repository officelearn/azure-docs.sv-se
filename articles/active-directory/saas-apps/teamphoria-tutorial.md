---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Teamphoria | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2631b34f5658c9d4f76ca26d378bc63fe59ad156
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373254"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamphoria"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Teamphoria

I den här självstudien får du lära dig hur du integrerar Teamphoria med Azure Active Directory (Azure AD). När du integrerar Teamphoria med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Teamphoria.
* Gör att användarna automatiskt loggas in i Teamphoria med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Teamphoria enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Teamphoria stöder **SP** initierade SSO

## <a name="adding-teamphoria-from-the-gallery"></a>Lägga till Teamphoria från galleriet

Om du vill konfigurera integreringen av Teamphoria i Azure AD måste du lägga till Teamphoria från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Teamfori** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Teamfori** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-teamphoria"></a>Konfigurera och testa en azure AD-inloggning för Teamphoria

Konfigurera och testa Azure AD SSO med Teamphoria med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Teamphoria.

Om du vill konfigurera och testa Azure AD SSO med Teamphoria slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Teamphoria SSO](#configure-teamphoria-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Teamphoria testanvändare](#create-teamphoria-test-user)** - att ha en motsvarighet till B.Simon i Teamphoria som är kopplad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **Hantera** på sidan [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**. **Manage**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Teamphoria Client support team](https://www.teamphoria.com/) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera teamfori** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Teamphoria.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Teamphoria**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-teamphoria-sso"></a>Konfigurera Teamphoria SSO

1. Om du vill automatisera konfigurationen i Teamphoria måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren klickar du på **Konfigurera Teamphoria** leder dig till Teamphoria-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Teamphoria. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-6.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Teamphoria manuellt öppnar du ett nytt webbläsarfönster och loggar in på teamforiföretagets webbplats som administratör och utför följande steg:

4. Gå till alternativet **ADMIN SETTINGS** i det vänstra verktygsfältet och klicka på **ENKEL INLOGGNING** under fliken Konfigurera.

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/admin_sso_configure.png)

5. Klicka på **Lägg till nya identitetsprovider** alternativ i det övre högra hörnet för att öppna formuläret för att lägga till inställningarna för SSO.

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/add_new_identity_provider.png)

6. Ange detaljerna i fälten enligt beskrivningen nedan-

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **VISNINGSNAMN:** Ange visningsnamnet för insticksprogrammet på administratörssidan.

    b. **KNAPPNAMN:** Namnet på fliken som visas på inloggningssidan för inloggning via SSO.

    c. **CERTIFIKAT:** Öppna certifikatet som hämtats tidigare från Azure-portalen i anteckningar, kopiera innehållet i samma och klistra in det här i rutan.

    d. **STARTPUNKT:** Klistra in **inloggnings-URL:en** som kopierats tidigare från Azure-portalen.

    e. Växla alternativet till **PÅ** och klicka på **SPARA**.

### <a name="create-teamphoria-test-user"></a>Skapa teamforitestanvändare

För att Azure AD-användare ska kunna logga in på Teamphoria måste de etableras i Teamphoria. När det gäller Teamphoria är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på teamforiföretagets webbplats som administratör.

1. Klicka på **admin-inställningar** i det vänstra verktygsfältet och under fliken **HANTERA** Klicka på **ANVÄNDARE** för att öppna administratörssidan för användare.

    ![Lägga till medarbetare](./media/teamphoria-tutorial/admin_manage_users.png)

1. Klicka på alternativet **MANUELL INBJUDAN.**

    ![Bjud in personer](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. På den här sidan utför du följande åtgärd.

    ![Bjud in personer](./media/teamphoria-tutorial/manual_user_invite.png)

    a. I textrutan **E-postadress** anger du **e-postadressen** till den användare som B.Simon.

    b. Ange förnamnet för användaren som **B**i textrutan **FÖRnamn** .

    c. I textrutan **EFTERNAMN** anger du efternamnet på användaren som **Simon**.

    d. Klicka på **BJUD IN 1 ANVÄNDARE**. Användaren måste acceptera inbjudan för att få skapas i systemet.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på teamforipanelen på åtkomstpanelen ska du automatiskt loggas in på den teamfori som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Teamphoria med Azure AD](https://aad.portal.azure.com/)

