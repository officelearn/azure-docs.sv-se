---
title: 'Självstudier: Azure Active Directory-integrering med ServiceNow | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ba516aa2c3d2decaa4962f1ccd0394ebe9a4a62
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706116"
---
# <a name="tutorial-integrate-servicenow-with-azure-active-directory"></a>Självstudier: Integrera ServiceNow med Azure Active Directory

I de här självstudierna lär du dig att integrera ServiceNow med Azure Active Directory (AD Azure). När du integrerar ServiceNow med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till ServiceNow.
* Ge dina användare att automatiskt inloggad till ServiceNow med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* ServiceNow enkel inloggning (SSO) aktiverat prenumeration.
* För ServiceNow, en instans eller klientorganisation för ServiceNow, Calgary-versionen eller senare
* För ServiceNow Express, en instans av ServiceNow Express, Helsingfors-versionen eller senare
* ServiceNow-klientorganisationen måste ha [Multiple Provider Single Sign On Plugin](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) (Plugin-program för enkel inloggning med flera providrar) aktiverat. Det går att göra detta genom att [skicka en tjänstbegäran](https://hi.service-now.com).
* Om du vill ha automatisk konfiguration aktiverar du plugin-programmet för flera providrar för ServiceNow.
* Om du vill installera programmet ServiceNow klassisk (mobilt) måste du gå till lämplig store och Sök efter programmet för ServiceNow klassisk och klicka på ladda ned.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för ServiceNow **SP** initierad SSO och stöder [ **automatisk** användaretablering](servicenow-provisioning-tutorial.md).

Programmet för ServiceNow klassisk (mobilt) kan nu konfigureras med Azure AD för att aktivera enkel inloggning och den stöder både **Android** och **IOS** användare. I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö.

## <a name="adding-servicenow-from-the-gallery"></a>Lägga till ServiceNow från galleriet

För att konfigurera integreringen av ServiceNow till Azure AD behöver du lägga till ServiceNow från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **ServiceNow** i sökrutan.
1. Välj **ServiceNow** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med ServiceNow med en testanvändare kallas **B.Simon**. För enkel inloggning ska fungera, måste du upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i ServiceNow.

Om du vill konfigurera och testa Azure AD enkel inloggning med ServiceNow, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera ServiceNow](#configure-servicenow)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Konfigurera Azure AD SSO för ServiceNow snabba](#configure-azure-ad-sso-for-servicenow-express)**  – om du vill ge användarna använda den här funktionen.
4. **[Konfigurera ServiceNow Express SSO](#configure-servicenow-express-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
5. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med B.Simon.
6. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera B.Simon att använda Azure AD enkel inloggning.
7. **[Skapa testanvändare för ServiceNow](#create-servicenow-test-user)**  har en motsvarighet för B.Simon i ServiceNow som är länkad till en Azure AD-representation av användaren.
8. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.
9. **[Testa enkel inloggning för ServiceNow klassisk (mobilt)](#test-sso-for-servicenow-classic-mobile)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **ServiceNow** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<instance-name>.service-now.com/navpage.do`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du måste uppdatera dessa värden med faktiska inloggnings-URL och identifierare som beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Base64)** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

   a. Klicka på kopieringsknappen för att kopiera **URL för appfederationsmetadata** och klistra in den i Anteckningar, eftersom denna URL för appfederationsmetadata används senare i självstudien.

    b. Klicka på **Ladda ned** för att ladda ned **Certificate(Base64)** och spara sedan certifikatfilen på datorn.

1. På den **konfigurera ServiceNow** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-servicenow"></a>Konfigurera ServiceNow

1. Logga in på ditt ServiceNow-program som administratör.

2. Aktivera plugin-programmet **Integration - Multiple Provider Single Sign-On Installer** (Integrering – installationsprogram för enkel inloggning med flera providrar) genom att följa nästa steg:

    a. I navigeringsfönstret till vänster söker du efter avsnittet **System Definition** (Systemdefinition) i sökfältet och klickar sedan på **Plugins** (Plugin-program).

    ![Aktivera plugin-program](./media/servicenow-tutorial/tutorial_servicenow_03.png "Aktivera plugin-program")

    b. Sök efter **Integration - Multiple Provider Single Sign-On Installer** (Integrering – installationsprogram för enkel inloggning med flera providrar).

     ![Aktivera plugin-program](./media/servicenow-tutorial/tutorial_servicenow_04.png "Aktivera plugin-program")

    c. Markera plugin-programmet. Högerklicka och välj **Activate/Upgrade** (Aktivera/uppgradera).

     ![Aktivera plugin-program](./media/servicenow-tutorial/tutorial_activate.png "Aktivera plugin-program")

    d. Klicka på knappen **Activate** (Aktivera).

     ![Aktivera plugin-program](./media/servicenow-tutorial/tutorial_activate1.png "Aktivera plugin-program")

3. I navigeringsfönstret till vänster söker du efter avsnittet **Multi-Provider SSO** (Enkel inloggning med flera providrar) i sökfältet och klickar sedan på **Properties** (Egenskaper).

    ![Konfigurera app-URL](./media/servicenow-tutorial/tutorial_servicenow_06.png "Konfigurera app-URL")

4. I dialogrutan **Multiple Provider SSO Properties** (Egenskaper för enkel inloggning med flera providrar) utför du följande steg:

    ![Konfigurera app-URL](./media/servicenow-tutorial/ic7694981.png "Konfigurera app-URL")

    * För **Enable multiple provider SSO** (Aktivera enkel inloggning med flera providrar) väljer du **Yes** (Ja).
  
    * För **Enable Auto Importing of users from all identity providers into the user table** (Aktivera automatisk import av användare från alla identitetsprovidrar till användartabellen) väljer du **Ja**.

    * För **Enable debug logging for the multiple provider SSO integration** (Aktivera felsökningsloggning för integreringen av enkel inloggning med flera providrar) väljer du **Ja**.

    * I textrutan **The field on the user table that...** (Fältet i den användartabell där...) skriver du **user_name**.
  
    * Klicka på **Spara**.

5. Du kan konfigurera **ServiceNow** på två sätt – automatiskt och manuellt.

6. Om du vill konfigurera **ServiceNow** automatiskt följer du stegen nedan:

    * Gå tillbaka till sidan för enkel inloggning för **ServiceNow** i Azure-portalen.

    * En tjänst för konfiguration med ett enda klick tillhandahålls för ServiceNow, det vill säga att Azure AD automatiskt konfigurerar ServiceNow för SAML-baserad autentisering. Om du vill aktivera den här tjänsten går du till avsnittet **ServiceNow Configuration** (ServiceNow-konfiguration) och klickar på **Configure ServiceNow** (Konfigurera ServiceNow) för att öppna fönstret för inloggningskonfiguration.

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * Anger ServiceNow-instansnamnet, administratörsanvändarnamnet och administratörslösenordet i formuläret **Configure sign-on** (Konfigurera inloggning) och klicka på **Configure Now** (Konfigurera nu). Observera att det administratörsanvändarnamn som angetts måste ha rollen **security_admin** tilldelad i ServiceNow för detta ska fungera. Annars för att manuellt konfigurera ServiceNow för att använda Azure AD som en SAML-identitetsprovider, klickar du på **manuellt konfigurera enkel inloggning** och kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från avsnittet Snabbreferens.

        ![Konfigurera app-URL](./media/servicenow-tutorial/configure.png "Konfigurera app-URL")

    * Logga in på ditt ServiceNow-program som administratör.

    * I den automatiska konfigurationen konfigureras de nödvändiga inställningarna på **ServiceNow**-sidan, men **X.509-certifikatet** aktiveras inte som standard. Du måste mappa det manuellt till din identitetsprovider i ServiceNow. Följ nedanstående steg för detta:

    * I navigeringsfönstret till vänster söker du efter avsnittet **Multi-Provider SSO** (Enkel inloggning med flera providrar) i sökfältet och klickar sedan på **Identity Providers** (Identitetsprovidrar).

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurera enkel inloggning")

    * Klicka på den automatiskt genererade identitetsprovidern

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_08.png "Konfigurera enkel inloggning")

    *  I avsnittet **Identity Provider** (Identitetsprovider) utför du följande steg:

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/automatic_config.png "Konfigurera enkel inloggning")

        * I den **namn** textrutan anger du ett namn för din konfiguration (till exempel **Microsoft Azure federerad enkel inloggning**).

        * Ta bort det ifyllda värdet **Identity Provider's SingleLogoutRequest** (SingleLogoutRequest för identitetsprovider) från textrutan.

        * Kopiera **ServiceNow Homepage** värde, klistra in den i den **inloggnings-URL** -textrutan i **ServiceNow SAML grundkonfiguration** avsnittet på Azure-portalen.

            > [!NOTE]
            > Startsidan för ServiceNow-instansen är en sammanlänkning av din **URL för ServiceNow-klientorganisation** och **/navpage.do** (till exempel: `https://fabrikam.service-now.com/navpage.do`).

        * Kopiera **entitets-ID / utfärdare** värde, klistra in det i **identifierare** -textrutan i **ServiceNow SAML grundkonfiguration** avsnittet på Azure-portalen.

        * Kontrollera att **NameID Policy** (NameID-princip) är inställd på värdet `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. 

    * Rulla ned till avsnittet **X.509 Certificate** (X.509-certifikat) och välj **Redigera**.

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_09.png "Konfigurera enkel inloggning")

    * Markera certifikatet och klicka på ikonen med en högerpil för att lägga till certifikatet

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_11.png "Konfigurera enkel inloggning")

    * Klicka på **Spara**.

    * Klicka på **Testanslutning** i det övre högra hörnet på sidan.

        ![Aktivera plugin-program](./media/servicenow-tutorial/tutorial_activate2.png "Aktivera plugin-program")

    * När du klickar på **Testanslutning** visas ett popup-fönster där du behöver ange autentiseringsuppgifter, och nedanstående sida med resultat visas. Felet **SSO Logout Test Results** (Testresultat för utloggning av enkel inloggning) förväntas. Ignorera felet och klicka på knappen **Aktivera**.

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/servicenowactivate.png "Konfigurera enkel inloggning")
  
7. Om du vill konfigurera **ServiceNow** manuellt följer du stegen nedan:

    * Logga in på ditt ServiceNow-program som administratör.

    * I navigeringsfönster på vänster sida klickar du på **Identity Providers** (Identitetsprovidrar).

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurera enkel inloggning")

    * I dialogrutan **Identity Providers** (Identitetsprovidrar) klickar du på **New** (Ny).

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694977.png "Konfigurera enkel inloggning")

    * I dialogrutan **Identity Providers** (Identitetsprovidrar) klickar du på **SAML**.

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694978.png "Konfigurera enkel inloggning")

    * I popup-fönstret **Import Identity Provider Metadata** (Importera metadata för identitetsprovider) utför du följande steg:

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/idp.png "Konfigurera enkel inloggning")

        * Ange den **URL för appfederationsmetadata** som du har kopierat från Azure-portalen.

        * Klicka på **Importera**.

    * Den läser metadata-URL för IdP och fyller i information i alla fält.

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694982.png "Konfigurera enkel inloggning")

        * I den **namn** textrutan anger du ett namn för din konfiguration (till exempel **Microsoft Azure federerad enkel inloggning**).

        * Ta bort det ifyllda värdet **Identity Provider's SingleLogoutRequest** (SingleLogoutRequest för identitetsprovider) från textrutan.

        * Kopiera **ServiceNow Homepage** värde, klistra in den i den **inloggnings-URL** -textrutan i **ServiceNow SAML grundkonfiguration** avsnittet på Azure-portalen.

            > [!NOTE]
            > Startsidan för ServiceNow-instansen är en sammanlänkning av din **URL för ServiceNow-klientorganisation** och **/navpage.do** (till exempel: `https://fabrikam.service-now.com/navpage.do`).

        * Kopiera **entitets-ID / utfärdare** värde, klistra in det i **identifierare** -textrutan i **ServiceNow SAML grundkonfiguration** avsnittet på Azure-portalen.

        * Kontrollera att **NameID Policy** (NameID-princip) är inställd på värdet `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`.

        * Klicka på **Avancerat**. I textrutan **User Field** (Användarfält) skriver du **email** (e-post) eller **user_name** (användarnamn) beroende på vilket fält som används för att unikt identifiera användare i din ServiceNow-distribution.

            > [!NOTE]
            > Du kan konfigurera Azure AD för att sända antingen Azure AD-användar-ID (user principal name, användarens huvudnamn) eller e-postadressen som den unika identifieraren i SAML-token genom att gå till avsnittet **ServiceNow > Attributes > Single Sign-On** (ServiceNow > Attribut > Enkel inloggning) i Azure-portalen och mappa det önskade fältet till attributet **nameidentifier**. Det värde som lagras för det valda attributet i Azure AD (till exempel användarens huvudnamn) måste matcha det värde som lagras i ServiceNow för det angivna fältet (till exempel user_name)

        * Klicka på **Testanslutning** i det övre högra hörnet på sidan.

        * När du klickar på **Testanslutning** visas ett popup-fönster där du behöver ange autentiseringsuppgifter, och nedanstående sida med resultat visas. Felet **SSO Logout Test Results** (Testresultat för utloggning av enkel inloggning) förväntas. Ignorera felet och klicka på knappen **Aktivera**.

          ![Konfigurera enkel inloggning](./media/servicenow-tutorial/servicenowactivate.png "Konfigurera enkel inloggning")

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Konfigurera Azure AD SSO för ServiceNow

1. I den [Azure-portalen](https://portal.azure.com/)på den **ServiceNow** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. På den **väljer du en metod för enkel inloggning** dialogrutan Välj **SAML/WS-Fed** läge för att aktivera enkel inloggning.

    ![Enkel inloggning för det valda läget](common/select-saml-option.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<instance-name>.service-now.com/navpage.do`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du måste uppdatera dessa värden med faktiska inloggnings-URL och identifierare som beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. En tjänst för konfiguration med ett enda klick tillhandahålls för ServiceNow, det vill säga att Azure AD automatiskt konfigurerar ServiceNow för SAML-baserad autentisering. Aktivera den här tjänsten genom att gå till avsnittet **Set up ServiceNow** (Konfigurera ServiceNow) och klicka på **Visa stegvisa instruktioner** för att öppna fönstret för inloggningskonfiguration.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Anger ServiceNow-instansnamnet, administratörsanvändarnamnet och administratörslösenordet i formuläret **Configure sign-on** (Konfigurera inloggning) och klicka på **Configure Now** (Konfigurera nu). Observera att det administratörsanvändarnamn som angetts måste ha rollen **security_admin** tilldelad i ServiceNow för detta ska fungera. Annars för att manuellt konfigurera ServiceNow för att använda Azure AD som en SAML-identitetsprovider, klickar du på **manuellt konfigurera enkel inloggning** och kopiera den **utloggnings-URL, Azure AD-identifierare och inloggnings-URL** från den Snabb referensavsnittet.

    ![Konfigurera app-URL](./media/servicenow-tutorial/configure.png "Konfigurera app-URL")

### <a name="configure-servicenow-express-sso"></a>Konfigurera ServiceNow Express SSO

1. Logga in på ditt ServiceNow Express-program som administratör.

2. I navigeringsfönster på vänster sida klickar du på **Enkel inloggning**.

    ![Konfigurera app-URL](./media/servicenow-tutorial/ic7694980ex.png "Konfigurera app-URL")

3. I dialogrutan **Enkel inloggning** klickar du på konfigurationsikonen i det övre högra hörnet och anger följande egenskaper:

    ![Konfigurera app-URL](./media/servicenow-tutorial/ic7694981ex.png "Konfigurera app-URL")

    a. Växla **Enable multiple provider SSO** (Aktivera enkel inloggning med flera providrar) till höger.

    b. Växla **Enable debug logging for the multiple provider SSO integration** (Aktivera felsökningsloggning för integreringen av enkel inloggning med flera providrar) till höger.

    c. I textrutan **The field on the user table that...** (Fältet i den användartabell där...) skriver du **user_name**.

4. I dialogrutan **Enkel inloggning** klickar du på **Add New Certificate** (Lägg till nytt certifikat).

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694973ex.png "Konfigurera enkel inloggning")

5. I dialogrutan **X.509 Certificates** (X.509-certifikat) utför du följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694975.png "Konfigurera enkel inloggning")

    a. I textrutan **Name** (Namn) skriver du ett namn för organisationen (till exempel: **TestSAML2.0**).

    b. Välj **Active** (Aktiv).

    c. För **Format** väljer du **PEM**.

    d. För **Type** (Typ) väljer du **Trust Store Cert** (Lita på lagrat certifikat).

    e. Öppna ditt Base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopierar innehållet i Urklipp och klistra sedan in den i textrutan **PEM Certificate** (PEM-certifikat).

    f. Klicka på **Update** (Uppdatera)

6. I dialogrutan **Enkel inloggning** klickar du på **Add New IdP** (Lägg till ny IdP).

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694976ex.png "Konfigurera enkel inloggning")

7. I dialogrutan **Add New Identity Provider** Lägg till ny identitetsprovider) går du till **Configure Identity Provider** (Konfigurera identitetsprovider) och utför följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694982ex.png "Konfigurera enkel inloggning")

    a. I textrutan **Name** (Namn) skriver du ett namn för organisationen (till exempel: **SAML 2.0**).

    b. I fältet **Identity Provider URL** (URL för identitetsprovider) klistrar du in värdet för det **identitetsprovider-ID** som du har kopierat från Azure-portalen.

    c. I fältet **Identity Provider's AuthnRequest** (AuthnRequest för identitetsprovider) klistrar du in värdet för den **URL för autentiseringsbegäran** som du har kopierat från Azure-portalen.

    d. I fältet **Identity Provider's SingleLogoutReques** (SingleLogoutReques för identitetsprovider) klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen

    e. För **Identity Provider Certificate** (Certifikat för identitetsprovider) väljer du det certifikat som du skapade i föregående steg.

8. Klicka på **Avancerade inställningar**. I **Additional Identity Provider Properties** (Ytterligare egenskaper för identitetsprovider) utför du följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694983ex.png "Konfigurera enkel inloggning")

    a. I textrutan **Protocol Binding for the IDP's SingleLogoutRequest** (Protokollbindning för IdP:ns SingleLogoutRequest) skriver du **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. I textrutan **NameID Policy** (NameID-princip) skriver du **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    c. I metoden **AuthnContextClassRef** skriver du `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Avmarkera **Create an AuthnContextClass** (Skapa en AuthnContextClass).

9. Under **Additional Service Provider Properties** (Ytterligare egenskaper för tjänstleverantör) utför du följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694984ex.png "Konfigurera enkel inloggning")

    a. I textrutan **ServiceNow Homepage** (Startsida för ServiceNow) skriver du URL:en för startsidan för din ServiceNow-instans.

    > [!NOTE]
    > Startsidan för ServiceNow-instansen är en sammanlänkning av din **URL för ServiceNow-klientorganisation** och **/navpage.do** (till exempel: `https://fabrikam.service-now.com/navpage.do`).

    b. I textrutan **Entity ID / Issuer** (ID/utfärdare för entitet) skriver du URL:en för din ServiceNow-klientorganisation.

    c. I textrutan **Audience URI** (URI för målgrupp) skriver du URL:en för din ServiceNow-klientorganisation.

    d. I textrutan **Clock Skew** (Klockförskjutning) skriver du **60**.

    e. I textrutan **User Field** (Användarfält) skriver du **email** (e-post) eller **user_name** (användarnamn) beroende på vilket fält som används för att unikt identifiera användare i din ServiceNow-distribution.

    > [!NOTE]
    > Du kan konfigurera Azure AD för att sända antingen Azure AD-användar-ID (user principal name, användarens huvudnamn) eller e-postadressen som den unika identifieraren i SAML-token genom att gå till avsnittet **ServiceNow > Attributes > Single Sign-On** (ServiceNow > Attribut > Enkel inloggning) i Azure-portalen och mappa det önskade fältet till attributet **nameidentifier**. Det värde som lagras för det valda attributet i Azure AD (till exempel användarens huvudnamn) måste matcha det värde som lagras i ServiceNow för det angivna fältet (till exempel user_name)

    f. Klicka på **Spara**.

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

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning om du beviljar åtkomst till ServiceNow.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I programlistan väljer du **ServiceNow**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-servicenow-test-user"></a>Skapa ServiceNow-testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i ServiceNow. ServiceNow stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](servicenow-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

> [!NOTE]
> Om du behöver skapa en användare manuellt kontaktar du [supportteamet för ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen ServiceNow i åtkomstpanelen, bör det vara loggas in automatiskt till ServiceNow som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Testa enkel inloggning för ServiceNow klassisk (mobil)

1. Öppna din **ServiceNow klassisk (mobilt)** program och utför följande steg:

    a. Klicka på den **lägga till** symbol under skärmen.

    ![Logga in](./media/servicenow-tutorial/test03.png)

    b. Skriv namnet på ServiceNow-instans och klicka på **Fortsätt**.

    ![Logga in](./media/servicenow-tutorial/test04.png)

    c. På den **logga in** skärmen, utför följande steg:

    ![Logga in](./media/servicenow-tutorial/test01.png)

    *  Typ **användarnamn** som B.simon@contoso.com.

    *  Klicka på **Använd extern inloggning** och du kommer att omdirigeras till Azure AD-sidan för inloggning.
    
    *  Ange dina autentiseringsuppgifter och om det inte finns någon tredje parts-autentisering eller andra säkerhetsfunktion som aktiverad och användaren måste svara därefter och programmet **startsida** visas enligt nedan:

        ![Startsidan](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera användarförsörjning](servicenow-provisioning-tutorial.md)