---
title: 'Självstudier: Azure Active Directory-integrering med SAP HANA | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP HANA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: c466e811d868403c59d6615882422996442d792a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045835"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Självstudier: Azure Active Directory-integrering med SAP HANA

I den här självstudien får du lära dig hur du integrerar SAP HANA med Azure Active Directory (AD Azure).

När du integrerar SAP HANA med Azure AD, får du följande förmåner:

- Du kan styra i Azure AD som har åtkomst till SAP HANA.
- Du kan aktivera användarna att automatiskt loggas i SAP HANA med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Mer information om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAP HANA, behöver du följande objekt:

- En Azure AD-prenumeration
- En SAP HANA-prenumeration som är enkel inloggning (SSO) aktiverat
- En HANA-instans som körs på alla offentliga IaaS, lokala, virtuella Azure-datorer eller SAP stora instanser i Azure
- XSA Administration webbgränssnitt, samt HANA Studio installerat på HANA-instans

> [!NOTE]
> Vi rekommenderar inte att använda en produktionsmiljö av SAP HANA för att testa stegen i den här självstudien. Testa integrationen först i utvecklings- eller mellanlagringsmiljön för programmet och sedan använda produktionsmiljön.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- [Hämta en månads kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/) av Azure AD om du inte redan har en Azure AD-utvärderingsmiljö.

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SAP HANA från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-sap-hana-from-the-gallery"></a>Lägg till SAP HANA från galleriet
Lägga till SAP HANA från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SAP HANA i Azure AD.

**Om du vill lägga till SAP HANA från galleriet, gör du följande:**

1. I den [Azure-portalen](https://portal.azure.com), i rutan till vänster väljer du den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Om du vill lägga till det nya programmet, Välj den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SAP HANA**. Välj sedan **SAP HANA** från panelen resultat. Välj slutligen den **Lägg till** för att lägga till programmet. 

    ![Det nya programmet](./media/saphana-tutorial/tutorial_saphana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SAP HANA baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vilken användaren motsvarighet i SAP HANA som en användare i Azure AD. Med andra ord måste du skapa en länk mellan en Azure AD-användare och relaterade användaren i SAP HANA.

I SAP HANA, ger den **användarnamn** värdet samma värde för den **användarnamn** i Azure AD. Det här steget upprättas länken mellan de två användarna.

Slutför följande byggblock för att konfigurera och testa Azure AD enkel inloggning med SAP HANA:

1. [Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on) vill tillåta att användarna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare för SAP HANA](#creating-a-sap-hana-test-user) har en motsvarighet för Britta Simon i SAP HANA som är länkad till en Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#testing-single-sign-on) att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt SAP HANA-program.

**Om du vill konfigurera Azure AD enkel inloggning med SAP HANA, gör du följande:**

1. I Azure-portalen på den **SAP HANA** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. I den **enkel inloggning** dialogrutan **SAML-baserad inloggning**väljer **läge**.
 
    ![Enkel inloggning för dialogrutan](./media/saphana-tutorial/tutorial_saphana_samlbase.png)

3. I den **SAP HANA-domän och URL: er** avsnittet, gör följande:

    ![Domän och URL: er med enkel inloggning för information](./media/saphana-tutorial/tutorial_saphana_url.png)

    a. I den **identifierare** skriver du följande: `HA100` 

    b. I den **svars-URL** skriver du en URL med följande mönster: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med det faktiska ID: T och svars-URL. Kontakta den [supportteamet för SAP HANA-klienten](https://cloudplatform.sap.com/contact.html) att hämta dessa värden. 

4. I den **SAML-signeringscertifikat** väljer **XML-Metadata för**. Spara metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Om certifikatet inte är aktiv, sedan aktivera den genom att välja den **gör nytt certifikat aktivt** kryssrutan i Azure AD. 

5. För SAP HANA-program som förväntar SAML-intyg i ett visst format. Följande skärmbild visar ett exempel på det här formatet. 

    Här vi mappat den **användaridentifierare** med den **ExtractMailPrefix()** funktion av **user.mail**. Detta ger prefixvärdet av användarens e-post, som är unikt användar-ID. Detta användar-ID skickas till SAP HANA-program i varje lyckade svar.

    ![Konfigurera enkel inloggning](./media/saphana-tutorial/attribute.png)

6. I den **användarattribut** delen av den **enkel inloggning** dialogrutan rutan, gör följande:

    a. I den **användaridentifierare** listrutan, väljer **ExtractMailPrefix**.
    
    b. I den **e** listrutan, väljer **user.mail**.

7. Välj knappen **Spara**.

    ![Konfigurera den enkel inloggning spara knappen](./media/saphana-tutorial/tutorial_general_400.png)
    
8. Om du vill konfigurera enkel inloggning på SAP HANA-sida, logga in på din **HANA XSA webbkonsolen** genom att gå till respektive HTTPS-slutpunkten.

    > [!NOTE]
    > I standardkonfigurationen omdirigerar URL: en begäran till en inloggningsskärm, som kräver autentiseringsuppgifter för en autentiserad användare för SAP HANA-databas. Den användare som loggar in måste ha behörighet att utföra administrationsuppgifter i SAML.

9. Gå till i webbgränssnittet XSA **SAML-identitetsprovider**. Därifrån väljer den **+** knappen längst ned på skärmen för att visa den **lägga till information om identitet** fönstret. Gör sedan följande:

    ![Lägg till identitetsprovider](./media/saphana-tutorial/sap1.png)

    a. I den **lägga till information om identitet** fönstret klistra in innehållet i den XML-Metadata (som du laddade ned från Azure portal) i den **Metadata** box.

    ![Lägg till identitetsprovider-inställningar](./media/saphana-tutorial/sap2.png)

    b. Om innehållet i XML-dokumentet är giltiga, parsning processen extraherar den information som krävs för den **ämne, entitets-ID och utfärdar** fält i den **allmänna data** Skrivbordsstorlek. Det också extraherar den information som krävs för URL-fält i den **mål** skärmen område, till exempel den **bas-URL och SingleSignOn URL: en (*)** fält.

    ![Lägg till identitetsprovider-inställningar](./media/saphana-tutorial/sap3.png)

    c. I den **namn** för den **allmänna Data** Skrivbordsstorlek, ange ett namn för den nya SAML SSO-identitetsprovidern.

    > [!NOTE]
    > Namnet på SAML IDP: N är obligatoriskt och måste vara unikt. Den visas i listan över tillgängliga SAML IDP: er som visas när du väljer SAML som autentiseringsmetod för SAP HANA XS programmen kan använda. Du kan exempelvis göra detta den **autentisering** skärmen område i administrationsverktyget för XS artefakten.

10. Välj **spara** att spara information om SAML-identitetsprovider och lägga till nya SAML IDP: N i listan över kända SAML IDP: er.

    ![Knappen Spara](./media/saphana-tutorial/sap4.png)

11. I HANA Studio system egenskaperna för den **Configuration** fliken, filterinställningar av **saml**. Justera sedan den **assertion_timeout** från **10sek** till **120 sek**.

    ![assertion_timeout inställningen](./media/saphana-tutorial/sap7.png)

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) när du ställer in appen! När du lägger till den här appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och komma åt den inbäddade dokumentation genom den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen embedded-dokumentation i den [embedded-dokumentation för Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Om du vill skapa en testanvändare i Azure AD, gör du följande:**

1. I den **Azure-portalen**, i rutan till vänster väljer du den **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](./media/saphana-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper**. Välj sedan **alla användare**.
    
    ![”Användare och grupper” och ”alla användare”-länkar](./media/saphana-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan **Lägg till** överst i dialogrutan.
 
    ![Knappen Lägg till](./media/saphana-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan rutan, gör följande:
 
    ![Dialogrutan användare](./media/saphana-tutorial/create_aaduser_04.png) 

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord**, och sedan skriva ned lösenordet.

    d. Välj **Skapa**.
 
### <a name="create-a-sap-hana-test-user"></a>Skapa en testanvändare för SAP HANA

Om du vill aktivera Azure AD-användare att logga in till SAP HANA, måste du etablera dem i SAP HANA.
SAP HANA stöder just-in-time-etablering, som är av aktiverat som standard.

Om du vill skapa en användare manuellt gör du följande:

>[!NOTE]
>Du kan ändra extern autentisering som användaren använder. De kan autentisera med ett externt system, till exempel Kerberos. Detaljerad information om externa identiteter, kontakta din [domänadministratören](https://cloudplatform.sap.com/contact.html).

1. Öppna den [SAP HANA-Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) som administratör, och sedan aktivera DB-användaren för SAML SSO.

    ![Skapa användare](./media/saphana-tutorial/sap5.png)

2. Markera kryssrutan osynliga till vänster om **SAML**, och välj sedan den **konfigurera** länk.

3. Välj **Lägg till** att lägga till SAML-IDP: N.  Välj lämplig SAML IDP: N och välj sedan **OK**.

4. Lägg till den **externa identitet** (i det här fallet BrittaSimon) eller välj **alla**. Välj sedan **OK**.

    >[!Note]
    >Om den **alla** inte är markerad och klicka sedan användarnamnet i HANA måste exakt matcha namnet på användaren i UPN-namnet innan domänsuffix. (Till exempel BrittaSimon@contoso.com blir BrittaSimon i HANA.)

5. I testsyfte kan du tilldela alla **XS** roller för användaren.

    ![Tilldela roller](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Du bör ge behörigheter som är lämpliga för ditt användningsfall endast.

6. Spara användaren.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till SAP HANA.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon till SAP HANA, utför du följande steg:**

1. Öppna program i Azure-portalen. Gå till vyn directory och gå till **företagsprogram**. Välj **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **SAP HANA**.

    ![Tilldela användare](./media/saphana-tutorial/tutorial_saphana_app.png) 

3. I menyn till vänster väljer **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

4. Välj den **Lägg till** knappen. I den **Lägg till tilldelning** dialogrutan **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** dialogrutan **Britta Simon** i den **användare** lista.

6. Klicka på den **Välj** knappen i den **användare och grupper** dialogrutan.

7. Välj den **tilldela** knappen i den **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen SAP HANA i åtkomstpanelen, bör du automatiskt loggas i SAP HANA-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/saphana-tutorial/tutorial_general_01.png
[2]: ./media/saphana-tutorial/tutorial_general_02.png
[3]: ./media/saphana-tutorial/tutorial_general_03.png
[4]: ./media/saphana-tutorial/tutorial_general_04.png

[100]: ./media/saphana-tutorial/tutorial_general_100.png

[200]: ./media/saphana-tutorial/tutorial_general_200.png
[201]: ./media/saphana-tutorial/tutorial_general_201.png
[202]: ./media/saphana-tutorial/tutorial_general_202.png
[203]: ./media/saphana-tutorial/tutorial_general_203.png

