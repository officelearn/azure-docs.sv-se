---
title: 'Självstudie: Azure Active Directory integrering med Deskradar | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Deskradar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 5ff3e014-b79f-4a6c-bb0b-38462bea5d10
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd5e3d6f8763af8c14d189321c6129876f48ab85
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158326"
---
# <a name="tutorial-integrate-deskradar-with-azure-active-directory"></a>Självstudie: integrera Deskradar med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Deskradar med Azure Active Directory (Azure AD). När du integrerar Deskradar med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Deskradar.
* Gör det möjligt för användarna att logga in automatiskt till Deskradar med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* Deskradar för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Deskradar stöder **SP-och IDP** -initierad SSO.

## <a name="adding-deskradar-from-the-gallery"></a>Lägga till Deskradar från galleriet

För att konfigurera integreringen av Deskradar till Azure AD behöver du lägga till Deskradar från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Deskradar** i sökrutan.
1. Välj **Deskradar** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Deskradar med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Deskradar.

Om du vill konfigurera och testa Azure AD SSO med Deskradar, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera DESKRADAR SSO](#configure-deskradar-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Deskradar-testanvändare](#create-deskradar-test-user)** – för att ha en motsvarighet för Britta Simon i Deskradar som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Deskradar** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://YOURDOMAIN.deskradar.cloud`

    b. I textrutan **svars-URL** skriver du en URL med följande mönster: `https://YOURDOMAIN.deskradar.cloud/auth/sso/saml/consume`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://YOURDOMAIN.deskradar.cloud/auth/sso/saml/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Ersätt **YOURDOMAIN** med din Deskradar-instansdomän. Hämta dessa värden genom att kontakta [supportteamet för Deskradar-klienten](mailto:support@deskradar.com). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Deskradar-programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![mallar](common/edit-attribute.png)

1. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Namn | Källattribut|
    | ---------------| --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-post | user.userprincipalname |
    | | |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **OK**.

    g. Klicka på **Save** (Spara).

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Deskradar** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-deskradar-sso"></a>Konfigurera Deskradar SSO

1. Om du vill automatisera konfigurationen i Deskradar måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren, klickar du på **installations Deskradar** för att dirigera dig till Deskradar-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Deskradar. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera Deskradar manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Deskradar-företags webbplats som administratör och utför följande steg:

1. Öppna panelen **Team** genom att klicka på ikonen i sidofältet.

1. Växla till fliken **Autentisering**.

1. På fliken **SAML 2.0** utför du följande steg:

    ![Deskradar-konfiguration](./media/deskradar-tutorial/14-paste-urls.jpg)

    a. Aktivera **SAML**-autentiseringsmetoden.

    b. I textrutan **SAML SSO URL** (URL för enkel inloggning med SAML) anger du värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    c. I textrutan **Identity Provider Issuer** (Utfärdare av identitetsprovider) anger du värdet för den **Azure AD-identifierare** som du har kopierat från Azure-portalen.

1. Öppna den nedladdade **Certifikat (Base64)** -filen med ett redigeringsprogram och kopiera och klistra in innehållet i fältet **Offentligt certifikat** i Deskradar.

    ![Deskradar-konfiguration](./media/deskradar-tutorial/15-paste-certificate.jpg)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **Ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Deskradar.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **Deskradar**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-deskradar-test-user"></a>Skapa Deskradar-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Deskradar. Kontakta [supportteamet för Deskradar-klienten](mailto:support@deskradar.com) för att lägga till användare i Deskradar-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Deskradar på åtkomst panelen, bör du loggas in automatiskt på den Deskradar som du ställer in SSO för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)