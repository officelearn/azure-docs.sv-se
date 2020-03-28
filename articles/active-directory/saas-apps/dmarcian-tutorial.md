---
title: 'Självstudiekurs: Azure Active Directory-integrering med dmarcian | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602c885deca429b56417181971ced495831ba5d3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68823691"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Självstudiekurs: Integrera dmarcian med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar dmarcian med Azure Active Directory (Azure AD). När du integrerar dmarcian med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till dmarcian.
* Gör att användarna automatiskt loggas in på dmarcian med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* dmarcian single sign-on (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* dmarcian har stöd för **SP- och IDP**-initierad enkel inloggning

## <a name="adding-dmarcian-from-the-gallery"></a>Lägga till dmarcian från galleriet

För att konfigurera integrering av dmarcian i Azure AD behöver du lägga till dmarcian från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **dmarcian** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **dmarcian** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med dmarcian med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i dmarcian.

Om du vill konfigurera och testa Azure AD SSO med dmarcian slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera dmarcian SSO](#configure-dmarcian-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
5. **[Skapa dmarcian testanvändare](#create-dmarcian-test-user)** - att ha en motsvarighet till B.Simon i dmarcian som är kopplad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **dmarcian** Hantera på sidan Hantera på sidan [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**. **Manage**
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Du kommer att uppdatera de här värdena med faktisk identifierare, svars-URL och inloggnings-URL. Detta förklaras senare i självstudien.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>Konfigurera dmarcian SSO

1. Om du vill automatisera konfigurationen inom dmarcian måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren, klicka på **Setup dmarcian** kommer att leda dig till dmarcian ansökan. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på dmarcian. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-6.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera dmarcian manuellt öppnar du ett nytt webbläsarfönster och loggar in på webbplatsen för det dmarcian företaget som administratör och utför följande steg:

4. Klicka på **Profil** i det övre högra hörnet och navigera till **Inställningar**.

    ![Inställningarna](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. Rulla nedåt och klicka på avsnittet **Enkel inloggning** följt av **Konfigurera**.

    ![Enskild](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. På sidan **Enkel inloggning med SAML** anger du **Status** till **Aktiverad** och utför följande steg:

    ![Autentiseringen](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * I avsnittet **Add dmarcian to your Identity Provider** (Lägg till dmarcian i identitetsprovidern) klickar du på **COPY** (Kopiera) för att kopiera **URL för konsumenttjänst för försäkran** för din instans och klistrar in den i textrutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    * I avsnittet **Add dmarcian to your Identity Provider** (Lägg till dmarcian i identitetsprovidern) klickar du på **COPY** (Kopiera) för att kopiera **Entitets-ID** för din instans och klistrar in den i textrutan **Identifier** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    * I avsnittet **Konfigurera autentisering** klistrar du i textrutan **Identity Provider Metadata** (Metadata för identitetsprovider) in den **URL för appfederationsmetadata** som du har kopierat från Azure-portalen.

    * I avsnittet **Konfigurera autentisering** klistrar du i textrutan **Attribute Statements** (Attributinstruktioner) in URL:en `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * I avsnittet **Set up Login URL** (Konfigurera inloggnings-URL) kopierar du **Inloggnings-URL** för din instans och klistrar in den i textrutan **Inloggnings-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

        > [!Note]
        > Du kan ändra **Inloggnings-URL** enligt din organisation.

    * Klicka på **Spara**.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till dmarcian.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **dmarcian**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-dmarcian-test-user"></a>Skapa dmarcian-testanvändare

För att göra det möjligt för Azure AD-användare att logga in i dmarcian måste de etableras till dmarcian. I dmarcian är etablering en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på dmarcian som säkerhetsadministratör.

2. Klicka på **Profil** i det övre högra hörnet och gå till **Hantera användare**.

    ![Användaren](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. På höger sida av avsnittet **SSO Users** (Användare av enkel inloggning) klickar du på **Lägg till ny användare**.

    ![Lägg till användare](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. I popup-fönstret **Lägg till ny användare** utför du följande steg:

    ![Den nya användaren](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. I textrutan **Ny användare e-post** anger du e-post för användare som **\@brittasimon contoso.com**.

    b. Om du vill ge administratörsrättigheter till användaren väljer du **Make User an Admin** (Gör användaren till administratör).

    c. Klicka på **Lägg till användare**.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på dmarcian-panelen i åtkomstpanelen bör du automatiskt loggas in på dmarcian som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

