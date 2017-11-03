---
title: "Självstudier: Azure Active Directory-integrering med SAP HANA | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP HANA."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: a7e73f6ee763d1005ad85935cf2d8f6b24ecf116
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Självstudier: Azure Active Directory-integrering med SAP HANA

Lär dig hur du integrerar SAP HANA med Azure Active Directory (AD Azure) i den här självstudiekursen.

Integrera SAP HANA med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAP HANA
- Du kan aktivera användarna att automatiskt hämta loggat in på SAP HANA (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med SAP HANA behöver du följande:

- En Azure AD-prenumeration
- En SAP HANA enkel inloggning aktiverad prenumeration
- Körs HANA instansen antingen på alla offentliga IaaS lokalt, virtuella Azure-datorer eller stora SAP-instanser i Azure
- XSA Administration webbgränssnitt samt HANA Studio installerat på HANA-instans.

> [!NOTE]
> Om du vill testa stegen i den här självstudiekursen rekommenderar vi inte med hjälp av en produktionsmiljö för SAP HANA. Testa integreringen först i utvecklings- eller mellanlagringsmiljön för programmet och sedan använda produktionsmiljön.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SAP HANA från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sap-hana-from-the-gallery"></a>Att lägga till SAP HANA från galleriet
Du måste lägga till SAP HANA från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SAP HANA i Azure AD.

**Utför följande steg för att lägga till SAP HANA från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SAP HANA**väljer **SAP HANA** resultatet-panelen klickar **Lägg till** för att lägga till programmet. 

    ![Det nya programmet](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med SAP HANA baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i SAP HANA motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SAP HANA upprättas.

I SAP HANA tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAP HANA, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare för SAP HANA](#creating-a-sap-hana-test-user)**  – du har en motsvarighet för Britta Simon i SAP HANA som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för SAP HANA.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SAP HANA:**

1. I Azure-portalen på den **SAP HANA** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_samlbase.png)

3. På den **SAP HANA domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och domän med enkel inloggning information](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_url.png)

    a. I den **identifierare** textruta typ som:`HA100` 

    b. I den **Reply URL** textruta Skriv en URL med följande mönster:`https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare och Reply-URL. Kontakta [SAP HANA klienten supportteamet](https://cloudplatform.sap.com/contact.html) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Om certifikatet inte är aktiv sedan aktivera den genom att klicka på kryssrutan ”Aktivera nytt certifikat” i Azure AD. 

5. SAP HANA program förväntar SAML-intyg i ett specifikt format. Följande skärmbild visar ett exempel för det här. Vi har här mappat den **användar-ID** med **ExtractMailPrefix()** funktion **user.mail**. Detta ger prefixvärdet av e-post för den användare som är den unika användar-ID. Detta skickas till SAP HANA-programmet i varje lyckat svar.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-saphana-tutorial/attribute.png)

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan:

    a. I den **användar-ID** listrutan, Välj **ExtractMailPrefix**.
    
    b. I den **e** listrutan, Välj **user.mail**.

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-saphana-tutorial/tutorial_general_400.png)
    
8. Konfigurera enkel inloggning på **SAP HANA** sida, logga in på ditt **HANA XSA webbkonsolen** genom att bläddra till respektive HTTPS-slutpunkten.

    > [!Note]
    > I standardkonfigurationen omdirigerar URL: en begäran till en inloggningsskärm som kräver autentiseringsuppgifter för autentiserad SAP HANA-databasanvändare för att slutföra inloggningen. Användare som loggar in måste ha behörighet som krävs för att utföra administrationsuppgifter i SAML.

9. I webbgränssnitt XSA navigerar du till **SAML-identitetsprovider** och därifrån klickar du på den **”+”** -knappen längst ned på skärmen för att visa fönstret Lägg till information om identitet och utför följande steg:

    ![Lägg till identitetsleverantören.](./media/active-directory-saas-saphana-tutorial/sap1.png)

    a. I den **lägga till information om identitet** rutan klistra in innehållet i det XML-Metadata, som du har hämtat från Azure-portalen i den **Metadata** textruta.

    ![Lägg till inställningar för identitetsleverantören.](./media/active-directory-saas-saphana-tutorial/sap2.png)

    b. Om innehållet i XML-dokumentet är giltiga tolkning processen extraherar informationen som krävs för att infoga i den **ämne, enhets-ID och utfärdaren** fält i datamängden allmänna skärmen området och URL-fält i skärmen målområdet, till exempel  **Base Webbadressen och SingleSignOn (*)**.

    ![Lägg till inställningar för identitetsleverantören.](./media/active-directory-saas-saphana-tutorial/sap3.png)

    c. Ange ett namn för den nya SAML SSO identitetsleverantören i rutan namn i Skrivbordsstorlek allmänna Data.

    > [!Note]
    > Namnet på SAML IDP är obligatoriskt och måste vara unikt. den visas i listan över tillgängliga SAML-IDPs som visas, om du väljer SAML som autentiseringsmetod för SAP HANA XS program ska använda, till exempel i Skrivbordsstorlek autentisering av XS artefakt Administrationsverktyg.

10. Spara information om nya SAML-identitetsprovider. Välj **spara** spara information om SAML-identitetsprovider och lägga till nya SAML IDP i listan över kända SAML IDPs.

    ![Knappen Spara](./media/active-directory-saas-saphana-tutorial/sap4.png)

11. I HANA Studio system-egenskaperna för den **Configuration** fliken, bara filtrera inställningar av **saml** och justera de **assertion_timeout** från **10 sek** till **120 sek**.

    ![assertion_timeout inställning](./media/active-directory-saas-saphana-tutorial/sap7.png)

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](./media/active-directory-saas-saphana-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-saphana-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Knappen Lägg till](./media/active-directory-saas-saphana-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan användare](./media/active-directory-saas-saphana-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-sap-hana-test-user"></a>Skapa en SAP HANA testanvändare

Om du vill aktivera Azure AD-användare att logga in till SAP HANA etableras de till SAP HANA.
SAP HANA stöder just-in-time-etablering, vilket är aktiverat som standard.

Om du behöver skapa en användare manuellt utför du följande steg:

>[!Note]
>Du kan ändra extern autentisering som används av användaren.
Externa användare autentiseras med hjälp av ett externt system, till exempel Kerberos-system. Detaljerad information om externa identiteter, kontakta din [domänadministratören](https://cloudplatform.sap.com/contact.html).

1. Öppna den [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) som administratör och aktivera DB-användaren för SAML SSO.

    ![Skapa användare](./media/active-directory-saas-saphana-tutorial/sap5.png)

2. Skalstreck osynliga kryssrutan till vänster om **SAML** och konfigurera länken.

3. Klicka på **Lägg till** SAML IDP och sedan **OK** att välja lämpliga SAML IDP.

4. Lägg till den **externa identitet** (t.ex. Här BrittaSimon) eller välj **”alla”** och på **OK**.

    >[!Note]
    >Om ”alla” kryssrutan inte är markerad och sedan användarnamnet i HANA måste exakt matcha namnet på användaren i UPN innan domänsuffixet (d.v.s. BrittaSimon@contoso.com skulle bli BrittaSimon i HANA).

5. I testsyfte kan du tilldela alla **”XS”** roller till användaren.

    ![tilldela roller](./media/active-directory-saas-saphana-tutorial/sap6.png)

    > [!TIP]
    > Du bör ge de behörigheterna som är lämpliga för din användningsfall endast.

6. Spara användaren.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP HANA.

![Tilldela rollen][200] 

**Om du vill tilldela SAP HANA Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SAP HANA**.

    ![Tilldela användare](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen SAP HANA på åtkomstpanelen du bör få automatiskt loggat in på ditt program för SAP HANA.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_203.png

