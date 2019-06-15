---
title: 'Självstudier: Azure Active Directory-integrering med Replicon | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27615b0c76b5c23bbc79788431b0e909b8bf22a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092771"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Självstudier: Integrera Replicon med Azure Active Directory

I de här självstudierna lär du dig att integrera Replicon med Azure Active Directory (AD Azure). När du integrerar Replicon med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Replicon.
* Ge dina användare att automatiskt inloggad till Replicon med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/).
* Aktiverat prenumeration replicon enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för replicon **SP** -initierad SSO.

## <a name="adding-replicon-from-the-gallery"></a>Att lägga till Replicon från galleriet

För att konfigurera integrering av Replicon i Azure AD, som du behöver lägga till Replicon från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Replicon** i sökrutan.
1. Välj **Replicon** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Replicon med en testanvändare kallas **B.Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Replicon för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med Replicon, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Replicon SSO](#configure-replicon-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera B.Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare Replicon](#create-replicon-test-user)**  – du har en motsvarighet för B.Simon i Replicon som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Replicon** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** ange värdena för följande fält:

    1. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. I rutan **Identifierare** skriver du en URL med följande mönster: `https://global.replicon.com/!/saml2/<client name>`

    1. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Replicon klienten supportteamet](https://www.replicon.com/customerzone/contact-support) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Klicka på ikonen Redigera/penna för **SAML-signeringscertifikat** att redigera inställningarna.

    ![Signeringsalgoritm](common/signing-algorithm.png)

    1. Välj **inloggning SAML-försäkran** som den **signering alternativet**.

    1. Välj **SHA-256** som den **Signeringsalgoritm**.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **XML-Metadata för Federation** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Konfigurera Replicon SSO

1. Logga in på webbplatsen Replicon företag som en administratör i ett annat webbläsarfönster.

2. Utför följande steg för att konfigurera SAML 2.0:

    ![Aktivera SAML-autentisering](./media/replicon-tutorial/ic777805.png "aktivera SAML-autentisering")

    a. Att visa den **EnableSAML Authentication2** dialogrutan lägger du till följande till din URL efter företagets nyckeln: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Nedan visas schemat för den fullständiga URL: en: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Klicka på den **+** att expandera den **v20Configuration** avsnittet.

   c. Klicka på den **+** att expandera den **metaDataConfiguration** avsnittet.

   d. Välj **SHA256** för xmlSignatureAlgorithm

   e. Klicka på **Välj fil**, för att välja din identitet providern XML-fil och klicka på **skicka**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas B.Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Replicon.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **Replicon**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-replicon-test-user"></a>Skapa Replicon testanvändare

Målet med det här avsnittet är att skapa en användare som kallas B.Simon i Replicon.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på webbplatsen Replicon företag som en administratör i ett webbläsarfönster.

2. Gå till **Administration \> användare**.

    ![Användare](./media/replicon-tutorial/ic777806.png "Användare")

3. Klicka på **+ Lägg till användare**.

    ![Lägg till användare](./media/replicon-tutorial/ic777807.png "Lägg till användare")

4. I den **användarprofil** avsnittet, utför följande steg:

    ![Användarprofil](./media/replicon-tutorial/ic777808.png "användarprofil")

    a. I den **inloggningsnamn** textrutan Ange Azure AD-e-postadress för Azure AD-användare som du vill etablera som `B.Simon@contoso.com`.

    > [!NOTE]
    > Inloggningsnamn måste matcha användarens e-postadress i Azure AD

    b. Som **autentiseringstyp**väljer **SSO**.

    c. Ange ID för autentisering till samma värde som inloggningsnamn (Azure AD e-postadressen för användaren)

    d. I den **avdelning** textrutan skriver användarens avdelning.

    e. Som **typ av anställd**väljer **administratör**.

    f. Klicka på **spara användarprofil**.

> [!NOTE]
> Du kan använda alla andra Replicon användare konto verktyg för att skapa eller API: er som tillhandahålls av Replicon att etablera användarkonton i Azure AD.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Replicon i åtkomstpanelen, bör det vara loggas in automatiskt till Replicon som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)