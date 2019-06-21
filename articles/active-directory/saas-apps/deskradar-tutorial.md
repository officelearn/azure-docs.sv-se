---
title: 'Självstudier: Azure Active Directory-integrering med Deskradar | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Deskradar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 5ff3e014-b79f-4a6c-bb0b-38462bea5d10
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95451bff6074c0beb220bf3c6edb5570a5258b1a
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310280"
---
# <a name="tutorial-integrate-deskradar-with-azure-active-directory"></a>Självstudier: Integrera Deskradar med Azure Active Directory

I de här självstudierna lär du dig att integrera Deskradar med Azure Active Directory (AD Azure). När du integrerar Deskradar med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Deskradar.
* Ge dina användare att automatiskt inloggad till Deskradar med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/).
* Aktiverat prenumeration Deskradar enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för Deskradar **SP och IDP** -initierad SSO.

## <a name="adding-deskradar-from-the-gallery"></a>Att lägga till Deskradar från galleriet

För att konfigurera integreringen av Deskradar till Azure AD behöver du lägga till Deskradar från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Deskradar** i sökrutan.
1. Välj **Deskradar** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Deskradar med en testanvändare kallas **B.Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Deskradar för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med Deskradar, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Deskradar SSO](#configure-deskradar-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Deskradar-testanvändare](#create-deskradar-test-user)** – för att ha en motsvarighet för Britta Simon i Deskradar som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Deskradar** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://YOURDOMAIN.deskradar.cloud`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://YOURDOMAIN.deskradar.cloud/auth/sso/saml/consume`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://YOURDOMAIN.deskradar.cloud/auth/sso/saml/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Ersätt **YOURDOMAIN** med din Deskradar-instansdomän. Hämta dessa värden genom att kontakta [supportteamet för Deskradar-klienten](mailto:support@deskradar.com). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Deskradar-programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

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

    g. Klicka på **Spara**.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Base64)** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. På den **konfigurera Deskradar** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-deskradar-sso"></a>Konfigurera Deskradar SSO

1. Om du vill automatisera konfigurationen inom Deskradar, måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![Mina appar-tillägg](common/install-myappssecure-extension.png)

1. När du lägger till tillägg till webbläsaren, klickar på **installationsprogrammet Deskradar** omdirigerar dig till programmet Deskradar. Ange administratörsautentiseringsuppgifter för att logga in på Deskradar därifrån. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Installationskonfiguration](common/setup-sso.png)

1. Om du vill konfigurera Deskradar manuellt, öppna ett nytt webbläsarfönster och logga till Deskradar företagets webbplatsen som administratör och utför följande steg:

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

I det här avsnittet skapar du en användare i Azure-portalen kallas B.Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Deskradar.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I programlistan väljer du **Deskradar**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-deskradar-test-user"></a>Skapa Deskradar-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Deskradar. Kontakta [supportteamet för Deskradar-klienten](mailto:support@deskradar.com) för att lägga till användare i Deskradar-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Deskradar i åtkomstpanelen, bör det vara loggas in automatiskt till Deskradar som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)