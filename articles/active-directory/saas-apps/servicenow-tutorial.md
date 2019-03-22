---
title: 'Självstudier: Azure Active Directory-integrering med ServiceNow | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0efc69aa7fb081303df6fc6327253fd5b8336999
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57898609"
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Självstudier: Azure Active Directory-integrering med ServiceNow

I den här självstudien lär du dig att integrera ServiceNow med Azure Active Directory (AD Azure).
Genom att integrera ServiceNow med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till ServiceNow.
* Du kan göra så att dina användare automatiskt loggas in på ServiceNow (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med ServiceNow behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* ServiceNow-prenumeration med enkel inloggning aktiverat
* För ServiceNow, en instans eller klientorganisation för ServiceNow, Calgary-versionen eller senare
* För ServiceNow Express, en instans av ServiceNow Express, Helsingfors-versionen eller senare
* ServiceNow-klientorganisationen måste ha [Multiple Provider Single Sign On Plugin](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) (Plugin-program för enkel inloggning med flera providrar) aktiverat. Det går att göra detta genom att [skicka en tjänstbegäran](https://hi.service-now.com).
* Om du vill ha automatisk konfiguration aktiverar du plugin-programmet för flera providrar för ServiceNow.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* ServiceNow stöder **IDP**-initierad enkel inloggning

* ServiceNow stöder [**automatisk** användaretablering](servicenow-provisioning-tutorial.md)

## <a name="adding-servicenow-from-the-gallery"></a>Lägga till ServiceNow från galleriet

För att konfigurera integreringen av ServiceNow till Azure AD behöver du lägga till ServiceNow från galleriet till listan över hanterade SaaS-appar.

**Lägg till ServiceNow från galleriet genom att utföra följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **ServiceNow**, väljer **ServiceNow** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![ServiceNow i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med ServiceNow baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i ServiceNow upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med ServiceNow behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD för ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för ServiceNow](#configure-servicenow-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Konfigurera enkel inloggning med Azure AD för ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)** – så att användarna kan använda den här funktionen.
4. **[Konfigurera enkel inloggning för ServiceNow Express](#configure-servicenow-express-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
5. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
6. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
7. **[Skapa ServiceNow-testanvändare](#create-servicenow-test-user)** – för att ha en motsvarighet för Britta Simon i ServiceNow som är länkad till en Azure AD-representation av användaren.
8. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Konfigurera enkel inloggning med Azure AD för ServiceNow

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med ServiceNow:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **ServiceNow**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![ServiceNow-domän och information om URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<instance-name>.service-now.com/navpage.do`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du behöver uppdatera de här värdena från faktisk inloggnings-URL och identifierare. Detta förklaras senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

    a. Klicka på kopieringsknappen för att kopiera **URL för appfederationsmetadata** och klistra in den i Anteckningar, eftersom denna URL för appfederationsmetadata används senare i självstudien.

    b. Klicka på **Ladda ned** för att ladda ned **Certificate(Base64)** och spara sedan certifikatfilen på datorn.

6. I avsnittet **Konfigurera ServiceNow** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-servicenow-single-sign-on"></a>Konfigurera enkel inloggning för ServiceNow

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

    * Anger ServiceNow-instansnamnet, administratörsanvändarnamnet och administratörslösenordet i formuläret **Configure sign-on** (Konfigurera inloggning) och klicka på **Configure Now** (Konfigurera nu). Observera att det administratörsanvändarnamn som angetts måste ha rollen **security_admin** tilldelad i ServiceNow för detta ska fungera. I annat fall konfigurerar du manuellt ServiceNow för att använda Azure AD som SAML-identitetsprovider genom att klicka på **Konfigurera enkel inloggning manuellt** och kopiera **utloggnings-URL, SAML-entitets-ID och URL för tjänsten för enkel inloggning med SAML** från avsnittet Quick Reference (Snabbreferens).

        ![Konfigurera app-URL](./media/servicenow-tutorial/configure.png "Konfigurera app-URL")

    * Logga in på ditt ServiceNow-program som administratör.

    * I den automatiska konfigurationen konfigureras de nödvändiga inställningarna på **ServiceNow**-sidan, men **X.509-certifikatet** aktiveras inte som standard. Du måste mappa det manuellt till din identitetsprovider i ServiceNow. Följ nedanstående steg för detta:

    * I navigeringsfönstret till vänster söker du efter avsnittet **Multi-Provider SSO** (Enkel inloggning med flera providrar) i sökfältet och klickar sedan på **Identity Providers** (Identitetsprovidrar).

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurera enkel inloggning")

    * Klicka på den automatiskt genererade identitetsprovidern

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_08.png "Konfigurera enkel inloggning")

    *  I avsnittet **Identity Provider** (Identitetsprovider) utför du följande steg:

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/automatic_config.png "Konfigurera enkel inloggning")

        * I textrutan **Name** (Namn) anger du ett namn för konfigurationen (till exempel **Federerad enkel inloggning för Microsoft Azure**).

        * Ta bort det ifyllda värdet **Identity Provider's SingleLogoutRequest** (SingleLogoutRequest för identitetsprovider) från textrutan.

        * Kopiera värdet **ServiceNow Homepage** (Startsida för ServiceNow), klistra in det i textrutan **Inloggnings-URL** i avsnittet **ServiceNow Domain and URLs** (ServiceNow-domän och URL:er) i Azure-portalen.

            > [!NOTE]
            > Startsidan för ServiceNow-instansen är en sammanlänkning av din **URL för ServiceNow-klientorganisation** och **/navpage.do** (till exempel: `https://fabrikam.service-now.com/navpage.do`).

        * Kopiera värdet **Entity ID / Issuer** (ID/utfärdare för entitet), klistra in det i textrutan **Identifier** (Identifierare) i avsnittet **ServiceNow Domain and URLs** (ServiceNow-domän och URL:er) i Azure-portalen.

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

        * I textrutan **Name** (Namn) anger du ett namn för konfigurationen (till exempel **Federerad enkel inloggning för Microsoft Azure**).

        * Ta bort det ifyllda värdet **Identity Provider's SingleLogoutRequest** (SingleLogoutRequest för identitetsprovider) från textrutan.

        * Kopiera värdet **ServiceNow Homepage** (Startsida för ServiceNow), klistra in det i textrutan **Inloggnings-URL** i avsnittet **ServiceNow Domain and URLs** (ServiceNow-domän och URL:er) i Azure-portalen.

            > [!NOTE]
            > Startsidan för ServiceNow-instansen är en sammanlänkning av din **URL för ServiceNow-klientorganisation** och **/navpage.do** (till exempel: `https://fabrikam.service-now.com/navpage.do`).

        * Kopiera värdet **Entity ID / Issuer** (ID/utfärdare för entitet), klistra in det i textrutan **Identifier** (Identifierare) i avsnittet **ServiceNow Domain and URLs** (ServiceNow-domän och URL:er) i Azure-portalen.

        * Kontrollera att **NameID Policy** (NameID-princip) är inställd på värdet `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`.

        * Klicka på **Avancerat**. I textrutan **User Field** (Användarfält) skriver du **email** (e-post) eller **user_name** (användarnamn) beroende på vilket fält som används för att unikt identifiera användare i din ServiceNow-distribution.

            > [!NOTE]
            > Du kan konfigurera Azure AD för att sända antingen Azure AD-användar-ID (user principal name, användarens huvudnamn) eller e-postadressen som den unika identifieraren i SAML-token genom att gå till avsnittet **ServiceNow > Attributes > Single Sign-On** (ServiceNow > Attribut > Enkel inloggning) i Azure-portalen och mappa det önskade fältet till attributet **nameidentifier**. Det värde som lagras för det valda attributet i Azure AD (till exempel användarens huvudnamn) måste matcha det värde som lagras i ServiceNow för det angivna fältet (till exempel user_name)

        * Klicka på **Testanslutning** i det övre högra hörnet på sidan.

        * När du klickar på **Testanslutning** visas ett popup-fönster där du behöver ange autentiseringsuppgifter, och nedanstående sida med resultat visas. Felet **SSO Logout Test Results** (Testresultat för utloggning av enkel inloggning) förväntas. Ignorera felet och klicka på knappen **Aktivera**.

          ![Konfigurera enkel inloggning](./media/servicenow-tutorial/servicenowactivate.png "Konfigurera enkel inloggning")

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Konfigurera enkel inloggning med Azure AD för ServiceNow Express

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **ServiceNow**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![ServiceNow-domän och information om URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<instance-name>.service-now.com/navpage.do`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du behöver uppdatera de här värdena från faktisk inloggnings-URL och identifierare. Detta förklaras senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. En tjänst för konfiguration med ett enda klick tillhandahålls för ServiceNow, det vill säga att Azure AD automatiskt konfigurerar ServiceNow för SAML-baserad autentisering. Aktivera den här tjänsten genom att gå till avsnittet **Set up ServiceNow** (Konfigurera ServiceNow) och klicka på **Visa stegvisa instruktioner** för att öppna fönstret för inloggningskonfiguration.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Anger ServiceNow-instansnamnet, administratörsanvändarnamnet och administratörslösenordet i formuläret **Configure sign-on** (Konfigurera inloggning) och klicka på **Configure Now** (Konfigurera nu). Observera att det administratörsanvändarnamn som angetts måste ha rollen **security_admin** tilldelad i ServiceNow för detta ska fungera. I annat fall konfigurerar du manuellt ServiceNow för att använda Azure AD som SAML-identitetsprovider genom att klicka på **Konfigurera enkel inloggning manuellt** och kopiera **utloggnings-URL, SAML-entitets-ID och URL för tjänsten för enkel inloggning med SAML** från avsnittet Quick Reference (Snabbreferens).

    ![Konfigurera app-URL](./media/servicenow-tutorial/configure.png "Konfigurera app-URL")

### <a name="configure-servicenow-express-single-sign-on"></a>Konfigurera enkel inloggning för ServiceNow Express

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

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till ServiceNow.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **ServiceNow**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **ServiceNow**.

    ![ServiceNow-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-servicenow-test-user"></a>Skapa ServiceNow-testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i ServiceNow. ServiceNow stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](servicenow-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

> [!NOTE]
> Om du behöver skapa en användare manuellt kontaktar du [supportteamet för ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på ServiceNow-panelen i åtkomstpanelen bör du automatiskt loggas in på ServiceNow som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera användarförsörjning](servicenow-provisioning-tutorial.md)
