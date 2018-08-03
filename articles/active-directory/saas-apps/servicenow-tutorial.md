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
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 5d5c4d5e26fa21488dd637805a4c22bd3ed18a7f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421091"
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
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till ServiceNow från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-servicenow-from-the-gallery"></a>Att lägga till ServiceNow från galleriet
För att konfigurera integrering av ServiceNow i Azure AD, som du behöver lägga till ServiceNow från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ServiceNow från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **ServiceNow**väljer **ServiceNow** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![ServiceNow i resultatlistan](./media/servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ServiceNow baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i ServiceNow är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i ServiceNow upprättas.

I ServiceNow, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med ServiceNow, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning för ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)**  – om du vill ge användarna använda den här funktionen.
1. **[Konfigurera Azure AD enkel inloggning för ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare för ServiceNow](#create-a-servicenow-test-user)**  – du har en motsvarighet för Britta Simon i ServiceNow som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Konfigurera Azure AD enkel inloggning för ServiceNow

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt ServiceNow-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ServiceNow:**

1. I Azure-portalen på den **ServiceNow** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/servicenow-tutorial/tutorial_servicenow_samlbase.png)

1. På den **ServiceNow domän och URL: er** avsnittet, utför följande steg:

    ![ServiceNow-domän och URL: er med enkel inloggning för information](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<instance-name>.service-now.com/navpage.do`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<instance-name>.service-now.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Du måste uppdatera dessa värden från faktiska inloggnings-URL och identifierare som beskrivs senare i självstudien.

1. På den **SAML-signeringscertifikat** avsnittet, utför följande steg: 

    ![Länk för hämtning av certifikat](./media/servicenow-tutorial/tutorial_servicenow_certificate.png)

    a. Klicka på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar, som den här Appfederationsmetadata används senare under kursen.

    b. Klicka på **Certificate(Base64)** och spara certifikatfilen på datorn.

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/servicenow-tutorial/tutorial_general_400.png)

1. Logga in på ditt ServiceNow-program som administratör.

1. Aktivera den **Integration - flera enkel inloggning för installationsprogrammet för providern** plugin-programmet genom att följa nästa steg:

    a. Sök i navigeringsfönstret till vänster **System Definition** avsnittet från sökfältet och klicka sedan på **plugin-program**.

    ![Aktivera plugin-programmet](./media/servicenow-tutorial/tutorial_servicenow_03.png "aktivera plugin-programmet")

     b. Sök efter **Integration - flera enkel inloggning för installationsprogrammet för providern**.

     ![Aktivera plugin-programmet](./media/servicenow-tutorial/tutorial_servicenow_04.png "aktivera plugin-programmet")

    c. Välj plugin-programmet. Högerklicka och välj **aktivera/uppgraderingen**.

    d. Klicka på den **aktivera** knappen.

1. Det finns två sätt som **ServiceNow** kan vara konfigurerade automatiskt och manuellt.

1. För att konfigurera **ServiceNow** automatiskt i stegen nedan

    a. Gå tillbaka till den **ServiceNow** enkel inloggning i Azure-portalen.

    b. Ett enda klick konfigurera tjänsten har angetts för ServiceNow det vill säga att Azure AD automatiskt konfigurera ServiceNow för SAML-baserad autentisering. Aktivera den här tjänsten genom att gå till **ServiceNow Configuration** klickar du på **konfigurera ServiceNow** öppna Konfigurera inloggnings-fönstret.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    c. Anger namn på ServiceNow-instans, administratörsanvändarnamn och administratörslösenord i den **konfigurera inloggning** formuläret och klicka på **konfigurera nu**. Observera att administratörsanvändarnamnet som angetts måste ha den **security_admin** roll som tilldelats i ServiceNow för detta ska fungera. Annars för att manuellt konfigurera ServiceNow för att använda Azure AD som en SAML-identitetsprovider, klickar du på **manuellt konfigurera enkel inloggning** och kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från avsnittet Snabbreferens.

    ![Konfigurera app-URL](./media/servicenow-tutorial/configure.png "konfigurera app-URL")

    d. Logga in på ditt ServiceNow-program som administratör.

    e. I den automatiska konfigurationen konfigureras de nödvändiga inställningarna på den **ServiceNow** sida men **X.509-certifikat** är inte aktiverad som standard. Du måste mappa den manuellt till din identitetsprovider i ServiceNow. Följ de nedanstående steg för samma:
    
    * I navigeringsfönstret till vänster klickar du på **Identitetsprovidrar** Under **Multi-provider SSO**.

      ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurera enkel inloggning")

    * Klicka på den automatiskt genererade identitetsprovidern

      ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_08.png "Konfigurera enkel inloggning")

    * Rulla ned till den **X.509-certifikat** avsnittet. Välj **Redigera**.

      ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_09.png "Konfigurera enkel inloggning")
    
    * Välj på certifikatet och klicka på högerpilen ikonen om du vill lägga till certifikatet

      ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_11.png "Konfigurera enkel inloggning")

    * Klicka på **Spara**.

    * Klicka på **aktivera** i det övre högra hörnet på sidan.

1. För att konfigurera **ServiceNow** manuellt följer du de stegen nedan

1. Logga in på ditt ServiceNow-program som administratör.

1. Sök i navigeringsfönstret till vänster **Multi-provider SSO** avsnittet från sökfältet och klicka sedan på **egenskaper**.

    ![Konfigurera app-URL](./media/servicenow-tutorial/tutorial_servicenow_06.png "konfigurera app-URL")

1. På den **flera egenskaper för enkel inloggning** dialogrutan utför följande steg:

    ![Konfigurera app-URL](./media/servicenow-tutorial/ic7694981.png "konfigurera app-URL")

    a. Som **aktivera flera provider SSO**väljer **Ja**.

    b. Som **aktivera automatisk import av användare från alla Identitetsproviders i användartabellen**väljer **Ja**.

    c. Som **Aktivera felsökningsloggning för flera providern SSO integration**väljer **Ja**.

    d. I **fältet på användaren tabell som...**  textrutan typ **user_name**.

    e. Klicka på **Spara**.

1. Sök i navigeringsfönstret till vänster **Multi-provider SSO** avsnittet från sökfältet och klicka sedan på **x509 certifikat**.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_05.png "Konfigurera enkel inloggning")

1. På den **X.509-certifikat** dialogrutan klickar du på **New**.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694974.png "Konfigurera enkel inloggning")

1. På den **X.509-certifikat** dialogrutan utför följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694975.png "Konfigurera enkel inloggning")

    a. I den **namn** textrutan anger du ett namn för din konfiguration (till exempel: **TestSAML2.0**).

    b. Välj **Active**.

    c. Som **Format**väljer **PEM**.

    d. Som **typ**väljer **lita på Store Cert**.

    e. Öppna din Base64-kodat certifikat som hämtats från Azure i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **PEM certifikat** textrutan.

     f. Klicka på **skicka**.

1. I navigeringsfönstret till vänster klickar du på **Identitetsprovidrar**.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurera enkel inloggning")

1. På den **Identitetsprovidrar** dialogrutan klickar du på **New**.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694977.png "Konfigurera enkel inloggning")

1. På den **Identitetsprovidrar** dialogrutan klickar du på **SAML2 Update1?**.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694978.png "Konfigurera enkel inloggning")

1. I dialogrutan Egenskaper för SAML2 Update1 utför du följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/idp.png "Konfigurera enkel inloggning")

    a. Välj **URL** alternativet i **Importera identitet providern Metadata** dialogruta.

    b. Ange den **Appfederationsmetadata** som du har kopierat från Azure-portalen.

    c. Klicka på **Importera**.

1. Den läser metadata-URL för IDP: N och fylls informationen för fält.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694982.png "Konfigurera enkel inloggning")

    a. I den **namn** textrutan anger du ett namn för din konfiguration (till exempel **SAML 2.0**).
    
    b. Kopiera **ServiceNow Homepage** värde, klistra in den i den **inloggnings-URL** -textrutan i **ServiceNow domän och URL: er** avsnittet på Azure-portalen.

    > [!NOTE]
    > Startsida för ServiceNow-instans är en sammansättning av din **ServieNow klient-URL** och **/navpage.do** (till exempel:`https://fabrikam.service-now.com/navpage.do`).

    c. Kopiera **entitets-ID / utfärdare** värde, klistra in det i **identifierare** -textrutan i **ServiceNow domän och URL: er** avsnittet på Azure-portalen.

    d. Klicka på **avancerade**. I den **Info** textrutan typ **e-post** eller **user_name**, beroende på vilket fält som används för att unikt identifiera användarna i din ServiceNow-distribution.

    > [!NOTE]
    > Du kan konfigurera Azure AD för att skapa Azure AD användar-ID (användarens huvudnamn) eller e-postadressen som den unika identifieraren i SAML-token genom att gå till den **ServiceNow > attribut > enkel inloggning** på Azure portal och mappa det önskade fältet till den **nameidentifier** attribut. Värdet för det markerade attributet i Azure AD (t.ex, användarens huvudnamn) måste matcha det värde som lagras i ServiceNow för det angivna fältet (till exempel användarnamn)

    e. Under **x509 certifikat**, visar en lista över certifikat som du har skapat i föregående steg.

    > [!NOTE]
    > ServiceNow tillåter inte aktivering av IDP: N utan att klicka på knappen Testa anslutning, om du vill åsidosätta samma följer du de stegen nedan.

1. Klicka på ikonen menyn från din nya identitetsprovider som du har skapat som en del av konfigurationen och väljer du den **kopiera sys_id**

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694992.png "Konfigurera enkel inloggning")

1. I det övre vänstra sökrutan söker du efter **sys_properties.list** och tryck på RETUR.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694993.png "Konfigurera enkel inloggning")

1. Klicka på **Ny**.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694994.png "Konfigurera enkel inloggning")

1. I den **Systemegenskapen** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694995.png "Konfigurera enkel inloggning")

    a. Ange `glide.authenticate.sso.redirect.idp` värde i textrutan namn.

    b. I den **värdet** textrutan klistra in kopiera sys_id värde som du har kopierat i föregående steg.

    c. Välj **privata**.

    d. Klicka på **skicka**.

1. Klicka på **Ny**.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694994.png "Konfigurera enkel inloggning")

1. I den **Systemegenskapen** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694996.png "Konfigurera enkel inloggning")

    a. Ange `glide.authenticate.multisso.test.connection.mandatory` värde i textrutan namn.

    b. I den **värdet** textrutan Ange **FALSKT**.

    c. Klicka på **skicka**.

1. Efter detta steget ovan nu kommer du att kunna aktivera din nya identitetsprovider och din SSO ska fungera

> [!NOTE]
> . Observera att du behöver testa den nya Idp-konfigurationen i ett nytt incognito-fönster

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Konfigurera Azure AD enkel inloggning för ServiceNow

1. I Azure-portalen på den **ServiceNow** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_samlbase.png)

1. På den **ServiceNow domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a. I den **inloggnings-URL** textrutan skriver du värdet med följande mönster: `https://<instance-name>.service-now.com/navpage.do`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska inloggnings-URL och identifierare. Kontakta [ServiceNow klienten supportteamet](https://www.servicenow.com/support/contact-support.html) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_certificates.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_general_400.png)

1. Ett enda klick konfigurera tjänsten har angetts för ServiceNow det vill säga att Azure AD automatiskt konfigurera ServiceNow för SAML-baserad autentisering. Aktivera den här tjänsten genom att gå till **ServiceNow Configuration** klickar du på **konfigurera ServiceNow** öppna Konfigurera inloggnings-fönstret.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

1. Anger namn på ServiceNow-instans, administratörsanvändarnamn och administratörslösenord i den **konfigurera inloggning** formuläret och klicka på **konfigurera nu**. Observera att administratörsanvändarnamnet som angetts måste ha den **security_admin** roll som tilldelats i ServiceNow för detta ska fungera. Annars för att manuellt konfigurera ServiceNow för att använda Azure AD som en SAML-identitetsprovider, klickar du på **manuellt konfigurera enkel inloggning** och kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från avsnittet Snabbreferens.

    ![Konfigurera app-URL](./media/servicenow-tutorial/configure.png "konfigurera app-URL")

1. Logga in på ditt ServiceNow Express-program som administratör.

1. I navigeringsfönstret till vänster klickar du på **enkel inloggning**.

    ![Konfigurera app-URL](./media/servicenow-tutorial/ic7694980ex.png "konfigurera app-URL")

1. På den **enkel inloggning** dialogrutan, klicka på konfigurationsikonen i det övre högra hörnet och ange följande egenskaper:

    ![Konfigurera app-URL](./media/servicenow-tutorial/ic7694981ex.png "konfigurera app-URL")

    a. Visa/dölj **aktivera flera provider SSO** till höger.
    
    b. Visa/dölj **Aktivera felsökningsloggning för flera providern SSO integration** till höger.
    
    c. I **fältet på användaren tabell som...**  textrutan typ **user_name**.

1. På den **enkel inloggning** dialogrutan klickar du på **Lägg till nytt certifikat**.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694973ex.png "Konfigurera enkel inloggning")

1. På den **X.509-certifikat** dialogrutan utför följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694975.png "Konfigurera enkel inloggning")

    a. I den **namn** textrutan anger du ett namn för din konfiguration (till exempel: **TestSAML2.0**).

    b. Välj **Active**.

    c. Som **Format**väljer **PEM**.

    d. Som **typ**väljer **lita på Store Cert**.

    e. Öppna din Base64-kodat certifikat som hämtats från Azure-portalen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **PEM certifikat** textrutan.

    f. Klicka på **Update**

1. På den **enkel inloggning** dialogrutan klickar du på **lägga till nya IDP: N**.

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694976ex.png "Konfigurera enkel inloggning")

1. På den **lägga till nya identitetsprovider** dialogrutan under **konfigurera identitetsprovider**, utför följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694982ex.png "Konfigurera enkel inloggning")

    a. I den **namn** textrutan anger du ett namn för din konfiguration (till exempel: **SAML 2.0**).

    b. I den **-URL för identitetsprovider** fältet, klistra in värdet för **identitet Provider-ID**, som du har kopierat från Azure-portalen.
    
    c. I den **Identitetsproviderns AuthnRequest** fältet, klistra in värdet för **autentiserings-URL för begära**, som du har kopierat från Azure-portalen.

    d. I den **Identitetsproviderns SingleLogoutRequest** fältet, klistra in värdet för **tjänst-URL för enkel utloggning**, som du har kopierat från Azure-portalen

    e. Som **providern identitetscertifikat**, väljer du det certifikat som du har skapat i föregående steg.

1. Klicka på **avancerade inställningar**, och under **ytterligare identitetsegenskaper providern**, utför följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694983ex.png "Konfigurera enkel inloggning")

    a. I den **protokollet bindning för IDP: ns SingleLogoutRequest** textrutan typ **urn: oasis: namn: tc: SAML:2.0:bindings:HTTP-omdirigera**.

    b. I den **NameID princip** textrutan typ **urn: oasis: namn: tc: SAML:1.1:nameid-format: Ospecificerat**.

    c. I den **AuthnContextClassRef metoden**, typen `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Avmarkera **skapa en AuthnContextClass**.

1. Under **ytterligare egenskaper för providern**, utför följande steg:

    ![Konfigurera enkel inloggning](./media/servicenow-tutorial/ic7694984ex.png "Konfigurera enkel inloggning")

    a. I den **ServiceNow Homepage** textrutan Ange Webbadressen till din startsida för ServiceNow-instans.

    > [!NOTE]
    > Startsida för ServiceNow-instans är en sammansättning av din **ServieNow klient-URL** och **/navpage.do** (till exempel: `https://fabrikam.service-now.com/navpage.do`).

    b. I den **entitets-ID / utfärdare** textrutan anger du URL för din ServiceNow-klient.

    c. I den **Målgrupps-URI** textrutan anger du URL för din ServiceNow-klient.

    d. I **klockan förskjuta** textrutan typ **60**.

    e. I den **Info** textrutan typ **e-post** eller **user_name**, beroende på vilket fält som används för att unikt identifiera användarna i din ServiceNow-distribution.

    > [!NOTE]
    > Du kan konfigurera Azure AD för att skapa Azure AD användar-ID (användarens huvudnamn) eller e-postadressen som den unika identifieraren i SAML-token genom att gå till den **ServiceNow > attribut > enkel inloggning** på Azure portal och mappa det önskade fältet till den **nameidentifier** attribut. Värdet för det markerade attributet i Azure AD (t.ex, användarens huvudnamn) måste matcha det värde som lagras i ServiceNow för det angivna fältet (till exempel användarnamn)

    f. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/servicenow-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/servicenow-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/servicenow-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/servicenow-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-servicenow-test-user"></a>Skapa en testanvändare för ServiceNow

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i ServiceNow. ServiceNow stöder automatisk användaretablering, vilket är som standard aktiverat. Du hittar mer information om [här](servicenow-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du behöva kontakta [ServiceNow klienten support-teamet](https://www.servicenow.com/support/contact-support.html)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till ServiceNow.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon ServiceNow, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **ServiceNow**.

    ![ServiceNow-länk i listan med program](./media/servicenow-tutorial/tutorial_servicenow_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
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
