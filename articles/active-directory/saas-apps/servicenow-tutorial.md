---
title: 'Självstudier: Azure Active Directory enkel inloggning (SSO) med ServiceNow | Microsoft Docs'
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
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 431d20c4c5ae5355d456ca3453b832e590cbb199
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558949"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Självstudier: Azure Active Directory enkel inloggning (SSO) med ServiceNow

I den här självstudien får du lära dig hur du integrerar ServiceNow med Azure Active Directory (Azure AD). När du integrerar ServiceNow med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ServiceNow.
* Gör det möjligt för användarna att logga in automatiskt till ServiceNow med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* ServiceNow för enkel inloggning (SSO) aktive rad.
* För ServiceNow, en instans eller klientorganisation för ServiceNow, Calgary-versionen eller senare
* För ServiceNow Express, en instans av ServiceNow Express, Helsingfors-versionen eller senare
* ServiceNow-klientorganisationen måste ha [Multiple Provider Single Sign On Plugin](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) (Plugin-program för enkel inloggning med flera providrar) aktiverat. Det går att göra detta genom att [skicka en tjänstbegäran](https://hi.service-now.com).
* Om du vill ha automatisk konfiguration aktiverar du plugin-programmet för flera providrar för ServiceNow.
* För att installera det klassiska ServiceNow-programmet (mobil) måste du gå till rätt Arkiv och söka efter det klassiska ServiceNow-programmet och klicka på Hämta.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. ServiceNow stöder **SP** -initierad SSO och har stöd för [ **Automatisk** användar etablering](servicenow-provisioning-tutorial.md).

ServiceNow-Klassiskt (mobil) program kan nu konfigureras med Azure AD för att aktivera SSO och det stöder både **Android** -och **iOS** -användare. I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

## <a name="adding-servicenow-from-the-gallery"></a>Lägga till ServiceNow från galleriet

För att konfigurera integreringen av ServiceNow till Azure AD behöver du lägga till ServiceNow från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **ServiceNow** i sökrutan.
1. Välj **ServiceNow** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Konfigurera och testa enkel inloggning med Azure AD för ServiceNow

Konfigurera och testa Azure AD SSO med ServiceNow med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i ServiceNow.

Om du vill konfigurera och testa Azure AD SSO med ServiceNow, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD](#create-an-azure-ad-test-user)** -testanvändare för att testa enkel inloggning i Azure AD med B. Simon.
    1. **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -testanvändaren att aktivera B. Simon för att använda enkel inloggning i Azure AD.
    1. **[Konfigurera Azure AD SSO för ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera ServiceNow](#configure-servicenow)** för att konfigurera SSO-inställningarna på program sidan.
    1. **[Skapa ServiceNow test User](#create-servicenow-test-user)** för att ha en motsvarighet till B. Simon i ServiceNow som är länkad till Azure AD-representation av användare.
    1. **[Konfigurera ServiceNow Express SSO](#configure-servicenow-express-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.    
3. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.
4. **[Testa SSO för ServiceNow Classic (mobil)](#test-sso-for-servicenow-classic-mobile)** för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **ServiceNow** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<instance-name>.service-now.com/navpage.do`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du måste uppdatera dessa värden med den faktiska inloggnings-URL: en och identifieraren som beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

   a. Klicka på kopieringsknappen för att kopiera **URL för appfederationsmetadata** och klistra in den i Anteckningar, eftersom denna URL för appfederationsmetadata används senare i självstudien.

    b. Klicka på **Ladda ned** för att ladda ned **Certificate(Base64)** och spara sedan certifikatfilen på datorn.

1. I avsnittet **Konfigurera ServiceNow** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ServiceNow.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **ServiceNow**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Konfigurera Azure AD SSO för ServiceNow Express

1. Välj **enkel inloggning**på sidan **ServiceNow** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialog rutan **Välj metod för enkel inloggning** väljer du **SAML/WS-utfodras** läge för att aktivera enkel inloggning.

    ![Välj läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **Redigera** ikon för att öppna dialog rutan **grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<instance-name>.service-now.com/navpage.do`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du måste uppdatera dessa värden med den faktiska inloggnings-URL: en och identifieraren som beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på **Ladda ned** för att ladda ned **certifikatet (base64)** från de angivna alternativen enligt ditt krav och sparar det på din dator.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. En tjänst för konfiguration med ett enda klick tillhandahålls för ServiceNow, det vill säga att Azure AD automatiskt konfigurerar ServiceNow för SAML-baserad autentisering. Aktivera den här tjänsten genom att gå till avsnittet **Set up ServiceNow** (Konfigurera ServiceNow) och klicka på **Visa stegvisa instruktioner** för att öppna fönstret för inloggningskonfiguration.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Anger ServiceNow-instansnamnet, administratörsanvändarnamnet och administratörslösenordet i formuläret **Configure sign-on** (Konfigurera inloggning) och klicka på **Configure Now** (Konfigurera nu). Kontrol lera att det angivna administratörs användar namnet måste ha **security_admin** -rollen tilldelad ServiceNow för att detta ska fungera. Om du vill konfigurera ServiceNow manuellt för att använda Azure AD som en SAML-identitetsprovider, klickar du på **Konfigurera enkel inloggning manuellt** och kopierar URL: **en för utloggning, Azure AD-identifierare och inloggnings webb adress** från avsnittet snabb referens.

    ![Konfigurera app-URL](./media/servicenow-tutorial/configure.png "Konfigurera app-URL")

## <a name="configure-servicenow"></a>Konfigurera ServiceNow

1. Logga in på ditt ServiceNow-program som administratör.

2. Aktivera plugin **-programmet för integration – flera providers med enkel inloggning** genom att följa stegen nedan:

    a. I navigeringsfönstret till vänster söker du efter avsnittet **System Definition** (Systemdefinition) i sökfältet och klickar sedan på **Plugins** (Plugin-program).

    ![Aktivera plugin-program](./media/servicenow-tutorial/tutorial_servicenow_03.png "Aktivera plugin-program")

    b. Sök efter **integration – flera providers-installationsprogram för enkel inloggning**.

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

    * Anger ServiceNow-instansnamnet, administratörsanvändarnamnet och administratörslösenordet i formuläret **Configure sign-on** (Konfigurera inloggning) och klicka på **Configure Now** (Konfigurera nu). Kontrol lera att det angivna administratörs användar namnet måste ha **security_admin** -rollen tilldelad ServiceNow för att detta ska fungera. Annars, om du vill konfigurera ServiceNow manuellt för att använda Azure AD som en SAML-identitetsprovider, klickar du på **Konfigurera enkel inloggning manuellt** och kopierar den utloggnings- **URL, SAML Entity ID och SAML Single Sign-on-tjänst-URL: en** från avsnittet snabb referens.

        ![Konfigurera app-URL](./media/servicenow-tutorial/configure.png "Konfigurera app-URL")

    * Logga in på ditt ServiceNow-program som administratör.

    * I den automatiska konfigurationen konfigureras de nödvändiga inställningarna på **ServiceNow**-sidan, men **X.509-certifikatet** aktiveras inte som standard. Du måste mappa det manuellt till din identitetsprovider i ServiceNow. Följ nedanstående steg för detta:

    * I navigeringsfönstret till vänster söker du efter avsnittet **Multi-Provider SSO** (Enkel inloggning med flera providrar) i sökfältet och klickar sedan på **Identity Providers** (Identitetsprovidrar).

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurera enkel inloggning")

    * Klicka på den automatiskt genererade identitetsprovidern

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_08.png "Konfigurera enkel inloggning")

    *  I avsnittet **Identity Provider** (Identitetsprovider) utför du följande steg:

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/automatic_config.png "Konfigurera enkel inloggning")

        * I text rutan **namn** anger du ett namn för din konfiguration (till exempel **Microsoft Azure federerad enkel inloggning**).

        * Ta bort det ifyllda värdet **Identity Provider's SingleLogoutRequest** (SingleLogoutRequest för identitetsprovider) från textrutan.

        * Kopiera **Start sidans** värde för ServiceNow, klistra in det i text rutan **inloggnings-URL** i avsnittet **ServiceNow Basic SAML Configuration** på Azure Portal.

            > [!NOTE]
            > Startsidan för ServiceNow-instansen är en sammanlänkning av din **URL för ServiceNow-klientorganisation** och **/navpage.do** (till exempel: `https://fabrikam.service-now.com/navpage.do`).

        * Kopiera **entitets-ID/Issuer-** värde, klistra in det i text rutan **identifierare** i avsnittet **ServiceNow Basic SAML Configuration** på Azure Portal.

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

        * I text rutan **namn** anger du ett namn för din konfiguration (till exempel **Microsoft Azure federerad enkel inloggning**).

        * Ta bort det ifyllda värdet **Identity Provider's SingleLogoutRequest** (SingleLogoutRequest för identitetsprovider) från textrutan.

        * Kopiera **Start sidans** värde för ServiceNow, klistra in det i text rutan **inloggnings-URL** i avsnittet **ServiceNow Basic SAML Configuration** på Azure Portal.

            > [!NOTE]
            > Startsidan för ServiceNow-instansen är en sammanlänkning av din **URL för ServiceNow-klientorganisation** och **/navpage.do** (till exempel: `https://fabrikam.service-now.com/navpage.do`).

        * Kopiera **entitets-ID/Issuer-** värde, klistra in det i text rutan **identifierare** i avsnittet **ServiceNow Basic SAML Configuration** på Azure Portal.

        * Kontrollera att **NameID Policy** (NameID-princip) är inställd på värdet `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`.

        * Klicka på **Avancerat**. I textrutan **User Field** (Användarfält) skriver du **email** (e-post) eller **user_name** (användarnamn) beroende på vilket fält som används för att unikt identifiera användare i din ServiceNow-distribution.

            > [!NOTE]
            > Du kan konfigurera Azure AD för att generera antingen Azure AD-användarnamnet (User Principal Name) eller e-postadressen som unik identifierare i SAML-token genom att gå till avsnittet **ServiceNow > attribut > enkel inloggning** i Azure Portal och mappa det önskade fältet till attributet **NameIdentifier** . Det värde som lagras för det valda attributet i Azure AD (till exempel användarens huvudnamn) måste matcha det värde som lagras i ServiceNow för det angivna fältet (till exempel user_name)

        * Klicka på **Testanslutning** i det övre högra hörnet på sidan.

        * När du klickar på **Testanslutning** visas ett popup-fönster där du behöver ange autentiseringsuppgifter, och nedanstående sida med resultat visas. Felet **SSO Logout Test Results** (Testresultat för utloggning av enkel inloggning) förväntas. Ignorera felet och klicka på knappen **Aktivera**.

          ![Konfigurera enkel inloggning](./media/servicenow-tutorial/servicenowactivate.png "Konfigurera enkel inloggning")

### <a name="create-servicenow-test-user"></a>Skapa ServiceNow-testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i ServiceNow. ServiceNow stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](servicenow-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

> [!NOTE]
> Om du behöver skapa en användare manuellt kontaktar du [supportteamet för ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="configure-servicenow-express-sso"></a>Konfigurera ServiceNow Express SSO

1. Logga in på ditt ServiceNow Express-program som administratör.

2. Klicka på **enkel inloggning**i navigerings fönstret till vänster.

    ![Konfigurera app-URL](./media/servicenow-tutorial/ic7694980ex.png "Konfigurera app-URL")

3. I dialog rutan **enkel inloggning** klickar du på konfigurations ikonen längst upp till höger och anger följande egenskaper:

    ![Konfigurera app-URL](./media/servicenow-tutorial/ic7694981ex.png "Konfigurera app-URL")

    a. Växla **Enable multiple provider SSO** (Aktivera enkel inloggning med flera providrar) till höger.

    b. Växla **Enable debug logging for the multiple provider SSO integration** (Aktivera felsökningsloggning för integreringen av enkel inloggning med flera providrar) till höger.

    c. I textrutan **The field on the user table that...** (Fältet i den användartabell där...) skriver du **user_name**.

4. I dialog rutan **enkel inloggning** klickar du på **Lägg till nytt certifikat**.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694973ex.png "Konfigurera enkel inloggning")

5. I dialogrutan **X.509 Certificates** (X.509-certifikat) utför du följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694975.png "Konfigurera enkel inloggning")

    a. I textrutan **Name** (Namn) skriver du ett namn för organisationen (till exempel: **TestSAML2.0**).

    b. Välj **Active** (Aktiv).

    c. För **Format** väljer du **PEM**.

    d. För **Type** (Typ) väljer du **Trust Store Cert** (Lita på lagrat certifikat).

    e. Öppna ditt Base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopierar innehållet i Urklipp och klistra sedan in den i textrutan **PEM Certificate** (PEM-certifikat).

    f. Klicka på **Update** (Uppdatera)

6. I dialog rutan **enkel inloggning** klickar du på **Lägg till ny IDP**.

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
    > Du kan konfigurera Azure AD för att generera antingen Azure AD-användarnamnet (User Principal Name) eller e-postadressen som unik identifierare i SAML-token genom att gå till avsnittet **ServiceNow > attribut > enkel inloggning** i Azure Portal och mappa det önskade fältet till attributet **NameIdentifier** . Det värde som lagras för det valda attributet i Azure AD (till exempel användarens huvudnamn) måste matcha det värde som lagras i ServiceNow för det angivna fältet (till exempel user_name)

    f. Klicka på **Spara**.

## <a name="test-sso"></a>Testa SSO

När du väljer panelen ServiceNow på åtkomst panelen, bör du loggas in automatiskt på den ServiceNow som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Testa SSO för ServiceNow Classic (mobil)

1. Öppna ditt **klassiska ServiceNow-program (mobil)** och utför följande steg:

    a. Klicka på **Lägg till** symbol under skärmen.

    ![Inloggningen](./media/servicenow-tutorial/test03.png)

    b. Skriv in namnet på ServiceNow-instansen och klicka på **Fortsätt**.

    ![Inloggningen](./media/servicenow-tutorial/test04.png)

    c. Utför följande steg på inloggnings skärmen:

    ![Inloggningen](./media/servicenow-tutorial/test01.png)

    *  Skriv **användar namn** som B.simon@contoso.com.

    *  Klicka på **Använd extern inloggning** så kommer du att omdirigeras till Azure AD-sidan för inloggning.
    
    *  Ange dina autentiseringsuppgifter och om det finns någon tredjeparts autentisering eller någon annan säkerhetsfunktion som är aktive rad kommer användaren att behöva svara på detta och programmets **Start sida** visas enligt nedan:

        ![Start Sidan](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera användarförsörjning](servicenow-provisioning-tutorial.md)

- [Prova ServiceNow med Azure AD](https://aad.portal.azure.com)