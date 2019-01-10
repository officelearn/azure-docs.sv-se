---
title: 'Självstudier: Azure Active Directory-integrering med SAP NetWeaver | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 607d05818966e62407795640d223f1aed2f59bbb
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156756"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Självstudier: Azure Active Directory-integrering med SAP NetWeaver

I den här självstudien får du lära dig hur du integrerar SAP NetWeaver med Azure Active Directory (AD Azure).

Integrera SAP NetWeaver med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAP NetWeaver.
- Du kan aktivera användarna att automatiskt få loggat in på SAP NetWeaver (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAP NetWeaver, behöver du följande objekt:

- En Azure AD-prenumeration
- SAP NetWeaver enkel inloggning aktiverat prenumeration
- SAP NetWeaver V7.20 krävs minst

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SAP NetWeaver från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sap-netweaver-from-the-gallery"></a>Att lägga till SAP NetWeaver från galleriet

För att konfigurera integrering av SAP NetWeaver i Azure AD, som du behöver lägga till SAP NetWeaver från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SAP NetWeaver från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SAP NetWeaver**väljer **SAP NetWeaver** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![SAP NetWeaver i resultatlistan](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SAP NetWeaver-baserade på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i SAP NetWeaver till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i SAP NetWeaver upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAP NetWeaver, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa SAP NetWeaver testanvändare](#creating-sapnetweaver-test-user)**  – du har en motsvarighet för Britta Simon i SAP NetWeaver som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt SAP NetWeaver-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SAP NetWeaver:**

1. Öppna ett nytt webbläsarfönster och logga i din SAP NetWeaver företagets webbplats som administratör

2. Se till att **http** och **https** tjänster är aktiva och lämpliga portar tilldelas i **SMICM** T-kod.

3. Logga in på klienten för företag för SAP-System (T01), där enkel inloggning krävs och aktivera HTTP-säkerhet session Management.

    a. Gå till transaktionskod **SICF_SESSIONS**. Den visar alla relevanta profil parametrar med aktuella värden. De se ut så här:-
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Justera ovan parametrar enligt organisationens krav, ovan parametrar anges här som indikation endast.

    b. Om det behövs justera parametrarna i instans/standardprofilen för SAP-system och starta om SAP-system.

    c. Dubbelklicka på relevanta klienten att aktivera HTTP-security-sessionen.

    ![Länk för nedladdning av certifikatet](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Aktivera nedan SICF tjänster:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. Gå till transaktionskod **SAML2** i företag klient av SAP-system [T01/122]. Ett användargränssnitt öppnas i en webbläsare. I det här exemplet antas vi 122 som SAP business-klient.

    ![Länk för nedladdning av certifikatet](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. Ange ditt användarnamn och lösenord för att ange i användargränssnittet och klicka på **redigera**.

    ![Länk för nedladdning av certifikatet](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. Ersätt **providernamn** från T01122 till `http://T01122` och klicka på **spara**.

    > [!NOTE]
    > Som standard providernamn medföljer som <sid> <client> formatet, men Azure AD förväntar sig namnet i formatet <protocol>://<name>rekommendationer att underhålla providernamn som https://<sid> <client> så att flera SAP NetWeaver ABAP motorer för att konfigurera i Azure AD.

    ![Länk för nedladdning av certifikatet](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **Generera Service Provider Metadata**: – när vi är klar med att konfigurera den **lokala leverantör** och **betrodda Providers** inställningar på användargränssnittet för SAML 2.0, nästa steg är att Generera tjänstleverantörens metadata-filen (som innehåller alla inställningar, när det gäller autentisering och andra konfigurationer i SAP). Vi behöver ladda upp den i Azure AD när den här filen har genererats.

    ![Länk för nedladdning av certifikatet](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Gå till **fliken lokala Provider**.

    b. Klicka på **Metadata**.

    c. Spara den genererade **Metadata-XML-filen** på datorn och ladda upp den i **SAML grundkonfiguration** avsnittet för automatisk polulate den **identifierare** och  **Svars-URL** värdena i Azure-portalen.

8. I Azure-portalen på den **SAP NetWeaver** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

9. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial_general_301.png)

10. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

11. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. Klicka på **ladda upp metadatafilen** att ladda upp den **tjänstleverantör metadatafil** som du fått tidigare.

    ![Ladda upp metadatafil](common/editmetadataupload.png)

    b. Klicka på **mappen logotyp** att välja metadatafilen och klicka på **överför**.

    ![Ladda upp metadatafil](common/uploadmetadata.png)

    c. När metadatafilen har laddats upp den **identifierare** och **svars-URL** värden får automatiskt ifylld i **SAML grundkonfiguration** avsnittet textrutan som visas nedan :

    ![SAP NetWeaver domän och URL: er med enkel inloggning för information](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_url.png)

    d. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<your company instance of SAP NetWeaver>`

12. SAP NetWeaver-programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![Attributet avsnittet](./media/sapnetweaver-tutorial/edit_attribute.png)

13. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    a. Klicka på **redigera** ikonen för att öppna den **hantera användaranspråk** dialogrutan.
    
    ![Attributet avsnittet](./media/sapnetweaver-tutorial/nameidattribute.png)

    b. På den **hantera användaranspråk** fliken, utför följande steg:

    ![Attributet avsnittet](./media/sapnetweaver-tutorial/nameidattribute1.png)

    * Välj **omvandling**.
  
    * Från den **omvandling** väljer `ExtractMailPrefix()`.
  
    * Från den **Parameter 1** väljer `user.userprincipalname`.

    * Klicka på **Spara**.

14. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned **Federation Metadata XML** och spara för metadatafilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_certificate.png)

15. På den **konfigurera SAP NetWeaver** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggnings-URL

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    ![SAP NetWeaver-konfigurationen](common/configuresection.png)

16. Logga in till SAP-system och gå till transaktionskod SAML2. Nytt webbläsarfönster öppnas med skärm för konfiguration av SAML.

17. För att konfigurera slutpunkter för betrodda provider (Azure AD) går du till **betrodda Providers** fliken.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

18. Tryck på **Lägg till** och välj **ladda upp metadatafilen** på snabbmenyn.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

19. Ladda upp metadatafilen som du har hämtat från Azure-portalen.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

20. Ange aliasnamnet på nästa skärm. Till exempel aadsts och tryck på **nästa** att fortsätta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

21. Se till att din **Sammandragsalgoritmen** ska vara **SHA-256** och kräver inte några ändringar och tryck på **nästa**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

22. På **slutpunkter för enkel inloggning**, använda **HTTP POST** och klicka på **nästa** att fortsätta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

23. På **enkel utloggning slutpunkter** Välj **HTTPRedirect** och klicka på **nästa** att fortsätta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

24. På **artefakt slutpunkter**, tryck på **nästa** att fortsätta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

25. På **autentiseringskrav**, klickar du på **Slutför**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

26. Gå till fliken **Trusted Provider** > **identitetsfederation** (från längst ned på skärmen). Klicka på **Redigera**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

27. Klicka på **Lägg till** under den **identitetsfederation** fliken (det nedre fönstret).

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

28. I popup-fönstret Välj **Ospecificerad** från den **stöds NameID-format** och klicka på OK.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

29. Observera att **användar-ID: T källa** och **användarläge id-mappning** värden bestämmer länken mellan SAP-användare och Azure AD-anspråk.  

    ####<a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scenario: SAP-användare till Azure AD-mappning.

    a. Skärmbild med NameID information från SAP.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Skärmbild som nämner begärda anspråk från Azure AD.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/claimsaad1.png)

    ####<a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scenario: Välj SAP användar-id utifrån konfigurerade e-postadress i SU01. I det här fallet konfigureras e-post-id i su01 för varje användare som kräver enkel inloggning.

    a.  Skärmbild med NameID information från SAP.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Skärmbild som nämner begärda anspråk från Azure AD.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/claimsaad2.png)

30. Klicka på **spara** och klicka sedan på **aktivera** att aktivera identitetsprovider.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/configuration1.png)

31. Klicka på **OK** när du uppmanas till detta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/configuration2.png)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_01.png)

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_02.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="creating-sap-netweaver-test-user"></a>Skapa SAP NetWeaver testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i SAP NetWeaver. Fungerar din internt experten SAP-team eller arbeta med din organisation SAP-partner och lägga till användare i SAP NetWeaver-plattformen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP NetWeaver.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **SAP NetWeaver**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

1. När identitetsprovidern Azure AD har aktiverats, försök att öppna nedanstående URL att kontrollera SSO (det kommer ingen uppmaning för användarnamn och lösenord)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (eller) använda URL: en nedan

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Ersätt sapurl med själva SAP-värdnamnet.

2. URL: en ovan bör ta att under nämnda skärmen. Om du kan nå upp till den nedan kan Azure AD SSO-installationen är klar.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/testingsso.png)

3. Om användarnamn och lösenord Ange PIN-kod,. diagnostisera problemet genom att aktivera spårning med hjälp av nedanstående URL

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
