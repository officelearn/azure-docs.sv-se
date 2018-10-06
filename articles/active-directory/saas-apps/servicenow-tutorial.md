---
title: 'Självstudier: Azure Active Directory-integration med ServiceNow | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: jeedes
ms.openlocfilehash: 470805b2bb77e367887767b95e0f1e04d79c8f9d
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830743"
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Självstudier: Azure Active Directory-integration med ServiceNow

I den här självstudien får du lära dig hur du integrerar ServiceNow med Azure Active Directory (AD Azure).

Integrera ServiceNow med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ServiceNow.
- Du kan aktivera användarna att automatiskt få loggat in på ServiceNow (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ServiceNow, behöver du följande objekt:

- En Azure AD-prenumeration
- För ServiceNow, en instans eller klient för ServiceNow Calgary versionen eller senare
- För ServiceNow Express, en instans av ServiceNow uttryckliga, Helsingfors versionen eller senare
- ServiceNow-klient måste ha den [flera enkel inloggning på plugin-providern](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) aktiverat. Detta kan göras genom [att skicka en serviceförfrågan](https://hi.service-now.com).
- Aktivera plugin-programmet för multi-provider för ServiceNow för automatisk konfiguration.

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till ServiceNow från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-servicenow-from-the-gallery"></a>Att lägga till ServiceNow från galleriet

För att konfigurera integrering av ServiceNow i Azure AD, som du behöver lägga till ServiceNow från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ServiceNow från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **ServiceNow**väljer **ServiceNow** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![ServiceNow i resultatlistan](./media/servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ServiceNow baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i ServiceNow är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i ServiceNow upprättas.

I ServiceNow, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med ServiceNow, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning för ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Azure AD enkel inloggning för ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)**  – om du vill ge användarna använda den här funktionen.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Skapa en testanvändare för ServiceNow](#create-a-servicenow-test-user)**  – du har en motsvarighet för Britta Simon i ServiceNow som är länkad till en Azure AD-representation av användaren.
5. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Konfigurera Azure AD enkel inloggning för ServiceNow

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt ServiceNow-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ServiceNow:**

1. I Azure-portalen på den **ServiceNow** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. Klicka på **ändra enkel inloggningsläge** på skärmen för att välja den **SAML** läge.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_general_300.png)

3. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_general_301.png)

4. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_general_302.png)

5. På den **SAML grundkonfiguration** avsnittet, utför följande steg:

    ![ServiceNow-domän och URL: er med enkel inloggning för information](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<instance-name>.service-now.com/navpage.do`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du måste uppdatera dessa värden från faktiska inloggnings-URL och identifierare som beskrivs senare i självstudien.

6. På den **SAML-signeringscertifikat** avsnittet, utför följande steg:

    ![Länk för hämtning av certifikat](./media/servicenow-tutorial/tutorial_servicenow_certificate.png)

    a. Klicka på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar, som den här Appfederationsmetadata används senare under kursen.

    b. Klicka på **hämta** att ladda ned **Certificate(Base64)** och spara certifikatfilen på datorn.

7. Logga in på ditt ServiceNow-program som administratör.

8. Aktivera den **Integration - flera enkel inloggning för installationsprogrammet för providern** plugin-programmet genom att följa nästa steg:

    a. Sök i navigeringsfönstret till vänster **System Definition** avsnittet från sökfältet och klicka sedan på **plugin-program**.

    ![Aktivera plugin-programmet](./media/servicenow-tutorial/tutorial_servicenow_03.png "aktivera plugin-programmet")

     b. Sök efter **Integration - flera enkel inloggning för installationsprogrammet för providern**.

     ![Aktivera plugin-programmet](./media/servicenow-tutorial/tutorial_servicenow_04.png "aktivera plugin-programmet")

    c. Välj plugin-programmet. Högerklicka och välj **aktivera/uppgraderingen**.

     ![Aktivera plugin-programmet](./media/servicenow-tutorial/tutorial_activate.png "aktivera plugin-programmet")

    d. Klicka på den **aktivera** knappen.

     ![Aktivera plugin-programmet](./media/servicenow-tutorial/tutorial_activate1.png "aktivera plugin-programmet")

9. Sök i navigeringsfönstret till vänster **Multi-provider SSO** avsnittet från sökfältet och klicka sedan på **egenskaper**.

    ![Konfigurera app-URL](./media/servicenow-tutorial/tutorial_servicenow_06.png "konfigurera app-URL")

10. På den **flera egenskaper för enkel inloggning** dialogrutan utför följande steg:

    ![Konfigurera app-URL](./media/servicenow-tutorial/ic7694981.png "konfigurera app-URL")

    * Som **aktivera flera provider SSO**väljer **Ja**.
  
    * Som **aktivera automatisk import av användare från alla Identitetsproviders i användartabellen**väljer **Ja**.

    * Som **Aktivera felsökningsloggning för flera providern SSO integration**väljer **Ja**.

    * I **fältet på användaren tabell som...**  textrutan typ **user_name**.
  
    * Klicka på **Spara**.

11. Det finns två sätt som **ServiceNow** kan konfigureras - automatiskt och manuellt.

12. För att konfigurera **ServiceNow** automatiskt, följer du de stegen nedan:

    * Gå tillbaka till den **ServiceNow** enkel inloggning i Azure-portalen.

    * Ett enda klick konfigurera tjänsten har angetts för ServiceNow det vill säga att Azure AD automatiskt konfigurera ServiceNow för SAML-baserad autentisering. Aktivera den här tjänsten genom att gå till **ServiceNow Configuration** klickar du på **konfigurera ServiceNow** öppna Konfigurera inloggnings-fönstret.

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * Anger namn på ServiceNow-instans, administratörsanvändarnamn och administratörslösenord i den **konfigurera inloggning** formuläret och klicka på **konfigurera nu**. Observera att administratörsanvändarnamnet som angetts måste ha den **security_admin** roll som tilldelats i ServiceNow för detta ska fungera. Annars för att manuellt konfigurera ServiceNow för att använda Azure AD som en SAML-identitetsprovider, klickar du på **manuellt konfigurera enkel inloggning** och kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från avsnittet Snabbreferens.

        ![Konfigurera app-URL](./media/servicenow-tutorial/configure.png "konfigurera app-URL")

    * Logga in på ditt ServiceNow-program som administratör.

    * I den automatiska konfigurationen konfigureras de nödvändiga inställningarna på den **ServiceNow** sida men **X.509-certifikat** är inte aktiverad som standard. Du måste mappa den manuellt till din identitetsprovider i ServiceNow. Följ de nedanstående steg för samma:

    * Sök i navigeringsfönstret till vänster **Multi-provider SSO** avsnittet från sökfältet och klicka sedan på **Identitetsprovidrar**.

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurera enkel inloggning")

    * Klicka på den automatiskt genererade identitetsprovider

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_08.png "Konfigurera enkel inloggning")

    *  På den **identitetsprovider** avsnittet, utför följande steg:

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/automatic_config.png "Konfigurera enkel inloggning")

        * I den **namn** textrutan anger du ett namn för din konfiguration (till exempel **Microsoft Azure federerad enkel inloggning**).

        * Ta bort den ifyllda **Identitetsproviderns SingleLogoutRequest** värdet från textrutan.

        * Kopiera **ServiceNow Homepage** värde, klistra in den i den **inloggnings-URL** -textrutan i **ServiceNow domän och URL: er** avsnittet på Azure-portalen.

            > [!NOTE]
            > Startsida för ServiceNow-instans är en sammansättning av din **ServieNow klient-URL** och **/navpage.do** (till exempel:`https://fabrikam.service-now.com/navpage.do`).

        * Kopiera **entitets-ID / utfärdare** värde, klistra in det i **identifierare** -textrutan i **ServiceNow domän och URL: er** avsnittet på Azure-portalen.

        * Kontrollera att **NameID princip** är inställd på `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` värde. 

    * Rulla ned till den **X.509-certifikat** väljer **redigera**.

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_09.png "Konfigurera enkel inloggning")

    * Välj på certifikatet och klicka på högerpilen ikonen om du vill lägga till certifikatet

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_11.png "Konfigurera enkel inloggning")

    * Klicka på **Spara**.

    * Klicka på **Testanslutningen** i det övre högra hörnet på sidan.

        ![Aktivera plugin-programmet](./media/servicenow-tutorial/tutorial_activate2.png "aktivera plugin-programmet")

    * När du klickar på den **Testanslutningen**, du får popup-fönstret där du måste ange autentiseringsuppgifter och nedan sida med resultat visas. Den **testresultaten för SSO-utloggning** fel förväntas ignorera felet och klicka på **aktivera** knappen.

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/servicenowactivate.png "Konfigurera enkel inloggning")
  
13. För att konfigurera **ServiceNow** manuellt i stegen nedan:

    * Logga in på ditt ServiceNow-program som administratör.

    * I navigeringsfönstret till vänster klickar du på **Identitetsprovidrar**.

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurera enkel inloggning")

    * På den **Identitetsprovidrar** dialogrutan klickar du på **New**.

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694977.png "Konfigurera enkel inloggning")

    * På den **Identitetsprovidrar** dialogrutan klickar du på **SAML**.

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694978.png "Konfigurera enkel inloggning")

    * På den **Importera identitet providern Metadata** popup-fönstret utför följande steg:

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/idp.png "Konfigurera enkel inloggning")

        * Ange den **Appfederationsmetadata** som du har kopierat från Azure-portalen.

        * Klicka på **Importera**.

    * Den läser metadata-URL för IDP: N och fylls informationen för fält.

        ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694982.png "Konfigurera enkel inloggning")

        * I den **namn** textrutan anger du ett namn för din konfiguration (till exempel **Microsoft Azure federerad enkel inloggning**).

        * Ta bort den ifyllda **Identitetsproviderns SingleLogoutRequest** värdet från textrutan.

        * Kopiera **ServiceNow Homepage** värde, klistra in den i den **inloggnings-URL** -textrutan i **ServiceNow domän och URL: er** avsnittet på Azure-portalen.

            > [!NOTE]
            > Startsida för ServiceNow-instans är en sammansättning av din **klient-URL för ServiceNow** och **/navpage.do** (till exempel:`https://fabrikam.service-now.com/navpage.do`).

        * Kopiera **entitets-ID / utfärdare** värde, klistra in det i **identifierare** -textrutan i **ServiceNow domän och URL: er** avsnittet på Azure-portalen.

        * Kontrollera att **NameID princip** är inställd på `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` värde.

        * Klicka på **avancerade**. I den **Info** textrutan typ **e-post** eller **user_name**, beroende på vilket fält som används för att unikt identifiera användarna i din ServiceNow-distribution.

            > [!NOTE]
            > Du kan konfigurera Azure AD för att skapa Azure AD användar-ID (användarens huvudnamn) eller e-postadressen som den unika identifieraren i SAML-token genom att gå till den **ServiceNow > attribut > enkel inloggning** på Azure portal och mappa det önskade fältet till den **nameidentifier** attribut. Värdet för det markerade attributet i Azure AD (t.ex, användarens huvudnamn) måste matcha det värde som lagras i ServiceNow för det angivna fältet (till exempel användarnamn)

        * Klicka på **Testanslutningen** i det övre högra hörnet på sidan.

        * När du klickar på den **Testanslutningen**, du får popup-fönstret där du måste ange autentiseringsuppgifter och nedan sida med resultat visas. Den **testresultaten för SSO-utloggning** fel förväntas ignorera felet och klicka på **aktivera** knappen.

          ![Konfigurera enkel inloggning](./media/servicenow-tutorial/servicenowactivate.png "Konfigurera enkel inloggning")

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Konfigurera Azure AD enkel inloggning för ServiceNow

1. I Azure-portalen på den **ServiceNow** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. Klicka på **ändra enkel inloggningsläge** på skärmen för att välja den **SAML** läge.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_general_300.png)

3. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_general_301.png)

4. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_general_302.png)

5. På den **SAML grundkonfiguration** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. I den **inloggnings-URL** textrutan skriver du värdet med följande mönster: `https://<instance-name>.service-now.com/navpage.do`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska inloggnings-URL och identifierare. Kontakta [ServiceNow klienten supportteamet](https://www.servicenow.com/support/contact-support.html) att hämta dessa värden.

6. På den **SAML-signeringscertifikat** avsnittet, klicka på **hämta** att ladda ned **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_certificates.png)

7. Ett enda klick konfigurera tjänsten har angetts för ServiceNow det vill säga att Azure AD automatiskt konfigurera ServiceNow för SAML-baserad autentisering. Aktivera den här tjänsten genom att gå till **konfigurera ServiceNow** klickar du på **visa stegvisa instruktioner** öppna Konfigurera inloggnings-fönstret.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

8. Anger namn på ServiceNow-instans, administratörsanvändarnamn och administratörslösenord i den **konfigurera inloggning** formuläret och klicka på **konfigurera nu**. Observera att administratörsanvändarnamnet som angetts måste ha den **security_admin** roll som tilldelats i ServiceNow för detta ska fungera. Annars för att manuellt konfigurera ServiceNow för att använda Azure AD som en SAML-identitetsprovider, klickar du på **manuellt konfigurera enkel inloggning** och kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från avsnittet Snabbreferens.

    ![Konfigurera app-URL](./media/servicenow-tutorial/configure.png "konfigurera app-URL")

9. Logga in på ditt ServiceNow Express-program som administratör.

10. I navigeringsfönstret till vänster klickar du på **enkel inloggning**.

    ![Konfigurera app-URL](./media/servicenow-tutorial/ic7694980ex.png "konfigurera app-URL")

11. På den **enkel inloggning** dialogrutan, klicka på konfigurationsikonen i det övre högra hörnet och ange följande egenskaper:

    ![Konfigurera app-URL](./media/servicenow-tutorial/ic7694981ex.png "konfigurera app-URL")

    a. Visa/dölj **aktivera flera provider SSO** till höger.

    b. Visa/dölj **Aktivera felsökningsloggning för flera providern SSO integration** till höger.

    c. I **fältet på användaren tabell som...**  textrutan typ **user_name**.

12. På den **enkel inloggning** dialogrutan klickar du på **Lägg till nytt certifikat**.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694973ex.png "Konfigurera enkel inloggning")

13. På den **X.509-certifikat** dialogrutan utför följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694975.png "Konfigurera enkel inloggning")

    a. I den **namn** textrutan anger du ett namn för din konfiguration (till exempel: **TestSAML2.0**).

    b. Välj **Active**.

    c. Som **Format**väljer **PEM**.

    d. Som **typ**väljer **lita på Store Cert**.

    e. Öppna din Base64-kodat certifikat som hämtats från Azure-portalen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **PEM certifikat** textrutan.

    f. Klicka på **Update**

14. På den **enkel inloggning** dialogrutan klickar du på **lägga till nya IDP: N**.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694976ex.png "Konfigurera enkel inloggning")

15. På den **lägga till nya identitetsprovider** dialogrutan under **konfigurera identitetsprovider**, utför följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694982ex.png "Konfigurera enkel inloggning")

    a. I den **namn** textrutan anger du ett namn för din konfiguration (till exempel: **SAML 2.0**).

    b. I den **-URL för identitetsprovider** fältet, klistra in värdet för **identitet Provider-ID**, som du har kopierat från Azure-portalen.

    c. I den **Identitetsproviderns AuthnRequest** fältet, klistra in värdet för **autentiserings-URL för begära**, som du har kopierat från Azure-portalen.

    d. I den **Identitetsproviderns SingleLogoutRequest** fältet, klistra in värdet för **tjänst-URL för enkel utloggning**, som du har kopierat från Azure-portalen

    e. Som **providern identitetscertifikat**, väljer du det certifikat som du har skapat i föregående steg.

16. Klicka på **avancerade inställningar**, och under **ytterligare identitetsegenskaper providern**, utför följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694983ex.png "Konfigurera enkel inloggning")

    a. I den **protokollet bindning för IDP: ns SingleLogoutRequest** textrutan typ **urn: oasis: namn: tc: SAML:2.0:bindings:HTTP-omdirigera**.

    b. I den **NameID princip** textrutan typ **urn: oasis: namn: tc: SAML:1.1:nameid-format: Ospecificerat**.

    c. I den **AuthnContextClassRef metoden**, typen `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Avmarkera **skapa en AuthnContextClass**.

17. Under **ytterligare egenskaper för providern**, utför följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694984ex.png "Konfigurera enkel inloggning")

    a. I den **ServiceNow Homepage** textrutan Ange Webbadressen till din startsida för ServiceNow-instans.

    > [!NOTE]
    > Startsida för ServiceNow-instans är en sammansättning av din **klient-URL för ServiceNow** och **/navpage.do** (till exempel: `https://fabrikam.service-now.com/navpage.do`).

    b. I den **entitets-ID / utfärdare** textrutan anger du URL för din ServiceNow-klient.

    c. I den **Målgrupps-URI** textrutan anger du URL för din ServiceNow-klient.

    d. I **klockan förskjuta** textrutan typ **60**.

    e. I den **Info** textrutan typ **e-post** eller **user_name**, beroende på vilket fält som används för att unikt identifiera användarna i din ServiceNow-distribution.

    > [!NOTE]
    > Du kan konfigurera Azure AD för att skapa Azure AD användar-ID (användarens huvudnamn) eller e-postadressen som den unika identifieraren i SAML-token genom att gå till den **ServiceNow > attribut > enkel inloggning** på Azure portal och mappa det önskade fältet till den **nameidentifier** attribut. Värdet för det markerade attributet i Azure AD (t.ex, användarens huvudnamn) måste matcha det värde som lagras i ServiceNow för det angivna fältet (till exempel användarnamn)

    f. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](./media/servicenow-tutorial/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](./media/servicenow-tutorial/create_aaduser_02.png)

    a. I den **namn** ange **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="create-a-servicenow-test-user"></a>Skapa en testanvändare för ServiceNow

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i ServiceNow. ServiceNow stöder automatisk användaretablering, vilket är som standard aktiverat. Du hittar mer information om [här](servicenow-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du behöva kontakta [ServiceNow klienten support-teamet](https://www.servicenow.com/support/contact-support.html)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till ServiceNow.

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **ServiceNow**.

    ![ServiceNow-länk i listan med program](./media/servicenow-tutorial/tutorial_servicenow_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på ServiceNow-panelen i åtkomstpanelen du bör få automatiskt loggat in på ditt ServiceNow-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](servicenow-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/servicenow-tutorial/tutorial_general_01.png
[2]: ./media/servicenow-tutorial/tutorial_general_02.png
[3]: ./media/servicenow-tutorial/tutorial_general_03.png
[4]: ./media/servicenow-tutorial/tutorial_general_04.png

[100]: ./media/servicenow-tutorial/tutorial_general_100.png

[200]: ./media/servicenow-tutorial/tutorial_general_200.png
[201]: ./media/servicenow-tutorial/tutorial_general_201.png
[202]: ./media/servicenow-tutorial/tutorial_general_202.png
[203]: ./media/servicenow-tutorial/tutorial_general_203.png
