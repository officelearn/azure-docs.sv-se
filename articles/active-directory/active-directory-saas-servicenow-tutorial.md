---
title: "Självstudier: Azure Active Directory-integrering med ServiceNow | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ServiceNow."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 7b25e2184296182687d49b798e5e5a9a2d623c6e
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Självstudier: Azure Active Directory-integrering med ServiceNow

I kursen får lära du att integrera ServiceNow med Azure Active Directory (AD Azure).

Integrera ServiceNow med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ServiceNow.
- Du kan aktivera användarna att automatiskt hämta loggat in på ServiceNow (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med ServiceNow, behöver du följande:

- En Azure AD-prenumeration
- För ServiceNow, instans eller innehavare för ServiceNow Calgary version eller högre
- För ServiceNow Express, en instans av ServiceNow Express, Helsingfors version eller högre
- ServiceNow-klient måste ha den [flera enkel inloggning på Plugin-providern](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) aktiverat. Detta kan göras med [att skicka en serviceförfrågan](https://hi.service-now.com).

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till ServiceNow från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-servicenow-from-the-gallery"></a>Att lägga till ServiceNow från galleriet
Du måste lägga till ServiceNow från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av ServiceNow i Azure AD.

**Utför följande steg för att lägga till ServiceNow från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **ServiceNow**väljer **ServiceNow** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![ServiceNow i resultatlistan](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med ServiceNow baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i ServiceNow motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i ServiceNow upprättas.

I ServiceNow, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med ServiceNow, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning för ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)**  - om du vill att användarna kan använda den här funktionen.
2. **[Konfigurera Azure AD enkel inloggning för ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)**  - om du vill att användarna kan använda den här funktionen.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Skapa en testanvändare ServiceNow](#create-a-servicenow-test-user)**  – du har en motsvarighet för Britta Simon i ServiceNow som är kopplad till Azure AD-representation av användaren.
5. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
6. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Konfigurera Azure AD-Single Sign-On för ServiceNow

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt ServiceNow-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ServiceNow:**

1. I Azure-portalen på den **ServiceNow** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. På den **ServiceNow domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och ServiceNow-domän med enkel inloggning information](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<instance-name>.service-now.com/navpage.do`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<instance-name>.service-now.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Du behöver uppdatera dessa värden från faktiska inloggnings-URL och identifierare som beskrivs senare i självstudierna.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-servicenow-tutorial/tutorial_general_400.png)

6. Att generera den **Metadata** url, utför följande steg:

    a. Klicka på **App registreringar**.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/appregistrations.png)

    b. Klicka på **slutpunkter** att öppna **slutpunkter** dialogrutan.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/endpointicon.png)
    
    c. Klicka på kopieringsknappen för att kopiera **FEDERATION METADATADOKUMENTET** url och klistra in den i anteckningar.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/endpoint.png)

    d. Gå till **ServiceNow** egenskaper och kopiera den **program-ID** med **kopiera** knappen och klistra in den i anteckningar.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/appid.png)

    e. Generera den **URL för tjänstmetadata** med hjälp av följande mönster: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`.  Kopiera det genererade värdet i anteckningar som dessa metadata URL kommer att användas senare under kursen.

7. En Klicka på Konfigurera service har angetts för ServiceNow som är att Azure AD automatiskt konfigurera ServiceNow för SAML-baserad autentisering. Om du vill aktivera den här tjänsten går du till **ServiceNow Configuration** klickar du på **konfigurera ServiceNow** öppna Konfigurera inloggning fönster.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_configure.png)

8. Ange ditt ServiceNow-instansnamn, administratörsanvändarnamnet och admin-lösenordet i den **konfigurera inloggning** formuläret och klicka på **konfigurera nu**. Observera att administratörsanvändarnamnet tillhandahålls måste ha den **security_admin** roll i ServiceNow för detta ska fungera. Annars för att manuellt konfigurera ServiceNow för att använda Azure AD som en SAML-identitetsprovider, klickar du på **manuellt konfigurera enkel inloggning** och kopiera den **Sign-Out URL, SAML enhets-ID och SAML inloggning tjänst-URL för enkel** från avsnittet Snabbreferens.

    ![Konfigurera app-URL](./media/active-directory-saas-servicenow-tutorial/configure.png "konfigurera app-URL")

9. Logga in på ditt ServiceNow-program som administratör.

10. Aktivera den **integrering – flera enkel inloggning installationsprogrammet för providern** plugin-programmet genom att följa nästa steg:

    a. Sök i navigeringsfönstret till vänster **System Definition** avsnittet från sökfältet och klicka sedan på **plugin-program**.

    ![Aktivera plugin-programmet](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "aktivera plugin-program")

     b. Sök efter **integrering – flera enkel inloggning installationsprogrammet för providern**.

     ![Aktivera plugin-programmet](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "aktivera plugin-program")

    c. Välj plugin-programmet. Högerklicka och välj **aktivera eller uppgradering**.

    d. Klicka på den **aktivera** knappen.

11. Sök i navigeringsfönstret till vänster **flera providern SSO** avsnittet från sökfältet och klicka sedan på **egenskaper**.

    ![Konfigurera app-URL](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "konfigurera app-URL")

12. På den **flera egenskaper för SSO** dialogrutan, utför följande steg:

    ![Konfigurera app-URL](./media/active-directory-saas-servicenow-tutorial/ic7694981.png "konfigurera app-URL")

    a. Som **aktivera flera providern SSO**väljer **Ja**.

    b. Som **aktivera automatisk importerar användare från alla identitetsleverantörer i användartabellen**väljer **Ja**.

    c. Som **Aktivera felsökningsloggning för flera providern SSO integration**väljer **Ja**.

    d. I **fältet på användaren tabell som...**  textruta typen **user_name**.

    e. Klicka på **Spara**.

13. Sök i navigeringsfönstret till vänster **flera providern SSO** avsnittet från sökfältet och klicka sedan på **x509 certifikat**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Konfigurera enkel inloggning")

14. På den **X.509-certifikat** dialogrutan klickar du på **ny**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/ic7694974.png "Konfigurera enkel inloggning")

15. På den **X.509-certifikat** dialogrutan, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/ic7694975.png "Konfigurera enkel inloggning")

    a. I den **namn** textruta, ange ett namn för konfigurationen (till exempel: **TestSAML2.0**).

    b. Välj **Active**.

    c. Som **Format**väljer **PEM**.

    d. Som **typen**väljer **förtroende Store Cert**.

    e. Öppna din Base64-kodat certifikat hämtas från Azure i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **PEM certifikat** textruta.

     f. Klicka på **skicka**.

16. I navigeringsfönstret till vänster klickar du på **identitetsleverantörer**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Konfigurera enkel inloggning")

17. På den **identitetsleverantörer** dialogrutan klickar du på **ny**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/ic7694977.png "Konfigurera enkel inloggning")

18. På den **identitetsleverantörer** dialogrutan klickar du på **SAML2 Update1?**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/ic7694978.png "Konfigurera enkel inloggning")

19. I dialogrutan Egenskaper för SAML2 Update1 utför du följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/idp.png "Konfigurera enkel inloggning")

    a. Välj **URL** alternativet i **Importera identitet providern Metadata** dialogruta.

    b. Ange den **URL för tjänstmetadata** genereras från Azure-portalen.

    c. Klicka på **Importera**.

20. Den läser IdP metadata-URL och fyller i informationen för fält.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/ic7694982.png "Konfigurera enkel inloggning")

    a. I den **namn** textruta, ange ett namn för konfigurationen (till exempel **SAML 2.0**).

    b. I den **Info** textruta typen **e-post** eller **user_name**, beroende på vilket fält som används för att unikt identifiera användarna i din ServiceNow-distribution.

    > [!NOTE]
    > Du kan konfigurera Azure AD för att generera Azure AD användar-ID (användarens huvudnamn) eller e-postadressen som den unika identifieraren i SAML-token genom att gå till den **ServiceNow > attribut > enkel inloggning** på Azure portal och mappa det önskade fältet som ska den **nameidentifier** attribut. Värdet för det markerade attributet i Azure AD (till exempel användarens huvudnamn) måste överensstämma med värdet som lagras i ServiceNow för det angivna fältet (till exempel användarnamn)

    c. Kopiera **ServiceNow Homepage** värde, klistra in den i den **inloggnings-URL** TextBox-kontroll i **ServiceNow domän och URL: er** avsnitt på Azure-portalen.

    > [!NOTE]
    > Startsidan för ServiceNow-instans är en sammansättning av din **ServieNow klient URL** och **/navpage.do** (till exempel:`https://fabrikam.service-now.com/navpage.do`).

    d. Kopiera **enhets-ID / utfärdaren** värde, klistra in den i **identifierare** TextBox-kontroll i **ServiceNow domän och URL: er** avsnitt på Azure-portalen.

     e. Under **x509 certifikat**, visar en lista över certifikat som du har skapat i föregående steg.

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Konfigurera Azure AD-Single Sign-On för ServiceNow Express

1. I Azure-portalen på den **ServiceNow** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. På den **ServiceNow domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_url.png)

    a. I den **inloggnings-URL** textruta Skriv det värde som använder följande mönster:`https://<instance-name>.service-now.com/navpage.do`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<instance-name>.service-now.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [ServiceNow klienten supportteamet](https://www.servicenow.com/support/contact-support.html) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/tutorial_general_400.png)

6. En Klicka på Konfigurera service har angetts för ServiceNow som är att Azure AD automatiskt konfigurera ServiceNow för SAML-baserad autentisering. Om du vill aktivera den här tjänsten går du till **ServiceNow Configuration** klickar du på **konfigurera ServiceNow** öppna Konfigurera inloggning fönster.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_configure.png)

7. Ange ditt ServiceNow-instansnamn, administratörsanvändarnamnet och admin-lösenordet i den **konfigurera inloggning** formuläret och klicka på **konfigurera nu**. Observera att administratörsanvändarnamnet tillhandahålls måste ha den **security_admin** roll i ServiceNow för detta ska fungera. Annars för att manuellt konfigurera ServiceNow för att använda Azure AD som en SAML-identitetsprovider, klickar du på **manuellt konfigurera enkel inloggning** och kopiera den **Sign-Out URL, SAML enhets-ID och SAML inloggning tjänst-URL för enkel** från avsnittet Snabbreferens.

    ![Konfigurera app-URL](./media/active-directory-saas-servicenow-tutorial/configure.png "konfigurera app-URL")

8. Logga in på ditt ServiceNow Express-program som administratör.

9. I navigeringsfönstret till vänster klickar du på **enkel inloggning**.

    ![Konfigurera app-URL](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "konfigurera app-URL")

10. På den **enkel inloggning** dialogrutan, klicka på ikonen konfigurationen i det övre högra hörnet och ange följande egenskaper:

    ![Konfigurera app-URL](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "konfigurera app-URL")

    a. Växla **aktivera flera providern SSO** till höger.
    
    b. Växla **Aktivera felsökningsloggning för flera providern SSO integration** till höger.
    
    c. I **fältet på användaren tabell som...**  textruta typen **user_name**.

11. På den **enkel inloggning** dialogrutan klickar du på **Lägg till nytt certifikat**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Konfigurera enkel inloggning")

12. På den **X.509-certifikat** dialogrutan, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/ic7694975.png "Konfigurera enkel inloggning")

    a. I den **namn** textruta, ange ett namn för konfigurationen (till exempel: **TestSAML2.0**).

    b. Välj **Active**.

    c. Som **Format**väljer **PEM**.

    d. Som **typen**väljer **förtroende Store Cert**.

    e. Öppna din Base64-kodat certifikat hämtas från Azure-portalen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **PEM certifikat** textruta.

    f. Klicka på **uppdatering**

13. På den **enkel inloggning** dialogrutan klickar du på **lägga till nya IdP**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Konfigurera enkel inloggning")

14. På den **lägga till nya identitetsleverantör** dialogrutan under **konfigurera identitetsleverantören**, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Konfigurera enkel inloggning")

    a. I den **namn** textruta, ange ett namn för konfigurationen (till exempel: **SAML 2.0**).

    b. I den **identitet providern URL** och klistra in värdet för fältet **identitet Provider-ID** som du har kopierat från Azure-portalen.
    
    c. I den **identitetsleverantörens AuthnRequest** och klistra in värdet för fältet **autentiserings-URL för begäran** som du har kopierat från Azure-portalen.

    d. I den **identitetsleverantörens SingleLogoutRequest** och klistra in värdet för fältet **tjänst-URL för enkel Sign-Out** som du har kopierat från Azure-portalen

    e. Som **providern identitetscertifikat**, Välj det certifikat som du har skapat i föregående steg.

15. Klicka på **avancerade inställningar**, och under **ytterligare identitetsegenskaper providern**, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Konfigurera enkel inloggning")

    a. I den **protokollet bindningen för den IDP SingleLogoutRequest** textruta typen **urn: oasis: namn: tc: SAML:2.0:bindings:HTTP-omdirigera**.

    b. I den **NameID princip** textruta typen **urn: oasis: namn: tc: SAML:1.1:nameid-format: Okänt**.

    c. I den **AuthnContextClassRef metoden**, typen `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Avmarkera **skapa en AuthnContextClass**.

16. Under **ytterligare egenskaper**, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Konfigurera enkel inloggning")

    a. I den **ServiceNow Homepage** textruta ange Webbadressen till startsidan för ServiceNow-instans.

    > [!NOTE]
    > Startsidan för ServiceNow-instans är en sammansättning av din **ServieNow klient URL** och **/navpage.do** (till exempel: `https://fabrikam.service-now.com/navpage.do`).

    b. I den **enhets-ID / utfärdaren** textruta anger du URL för din ServiceNow-klient.

    c. I den **Målgrupps-URI** textruta anger du URL för din ServiceNow-klient.

    d. I **klockan skeva** textruta typen **60**.

    e. I den **Info** textruta typen **e-post** eller **user_name**, beroende på vilket fält som används för att unikt identifiera användarna i din ServiceNow-distribution.

    > [!NOTE]
    > Du kan konfigurera Azure AD för att generera Azure AD användar-ID (användarens huvudnamn) eller e-postadressen som den unika identifieraren i SAML-token genom att gå till den **ServiceNow > attribut > enkel inloggning** på Azure portal och mappa det önskade fältet som ska den **nameidentifier** attribut. Värdet för det markerade attributet i Azure AD (till exempel användarens huvudnamn) måste överensstämma med värdet som lagras i ServiceNow för det angivna fältet (till exempel användarnamn)

    f. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-servicenow-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-servicenow-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-servicenow-test-user"></a>Skapa en testanvändare ServiceNow

I det här avsnittet skapar du en användare som kallas Britta Simon i ServiceNow. Om du inte vet hur du lägger till en användare i ditt ServiceNow eller snabba ServiceNow-konto, kontakta [supportteamet för ServiceNow-klient](https://www.servicenow.com/support/contact-support.html)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ServiceNow.

![Tilldela rollen][200] 

**Om du vill tilldela ServiceNow Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **ServiceNow**.

    ![Länken ServiceNow i listan med program](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen ServiceNow på åtkomstpanelen du bör få automatiskt loggat in på ditt ServiceNow-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png

