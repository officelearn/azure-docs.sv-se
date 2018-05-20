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
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 626dfe07c13c90686157dea710715b333a378136
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Självstudier: Azure Active Directory-integrering med SAP HANA

Lär dig hur du integrerar SAP HANA med Azure Active Directory (AD Azure) i den här självstudiekursen.

När du integrerar SAP HANA med Azure AD kan du få följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAP HANA.
- Du kan aktivera användarna att automatiskt hämta inloggad SAP HANA med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats--Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAP HANA behöver du följande:

- En Azure AD-prenumeration
- En SAP HANA-prenumeration som är enkel inloggning (SSO) aktiverad
- En HANA-instans som körs på alla offentliga IaaS lokalt, Azure VM eller SAP stora instanser i Azure
- XSA Administration webbgränssnitt samt HANA Studio installerat på HANA-instans

> [!NOTE]
> Vi rekommenderar inte med hjälp av en produktionsmiljö för SAP HANA för att testa stegen i den här kursen. Testa integreringen först i utvecklings- eller mellanlagringsmiljön för programmet och sedan använda produktionsmiljön.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte din produktionsmiljö om det är nödvändigt.
- [Skaffa en kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/) Azure AD, om du inte redan har en utvärderingsversion Azure AD-miljö.

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SAP HANA från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-sap-hana-from-the-gallery"></a>Lägg till SAP HANA från galleriet
För att konfigurera integrering av SAP HANA i Azure AD, lägga till SAP HANA från galleriet i listan över hanterade SaaS-appar.

**Om du vill lägga till SAP HANA från galleriet, gör du följande:**

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till det nya programmet, Välj den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SAP HANA**. Välj sedan **SAP HANA** från panelen resultat. Välj slutligen den **Lägg till** för att lägga till programmet. 

    ![Det nya programmet](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med SAP HANA baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD att veta vilka motsvarande användaren i SAP HANA är att en användare i Azure AD. Med andra ord måste du skapa en länk mellan en Azure AD-användare och relaterade användaren i SAP HANA.

I SAP HANA ger den **användarnamn** värdet samma värde i **användarnamn** i Azure AD. Det här steget upprättas länken mellan de två användarna.

Slutför följande byggblock för att konfigurera och testa Azure AD enkel inloggning med SAP HANA:

1. [Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on) att användarna ska kunna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare för SAP HANA](#creating-a-sap-hana-test-user) har en motsvarighet för Britta Simon SAP HANA som är kopplad till Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#testing-single-sign-on) att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för SAP HANA.

**För att konfigurera Azure AD enkel inloggning med SAP HANA, gör du följande:**

1. I Azure-portalen på den **SAP HANA** programmet integration anger **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. I den **enkel inloggning** dialogrutan under **SAML-baserade inloggning**väljer **läge**.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_samlbase.png)

3. I den **SAP HANA domän och URL: er** avsnittet, gör du följande:

    ![URL: er och domän med enkel inloggning information](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_url.png)

    a. I den **identifierare** skriver du följande: `HA100` 

    b. I den **Reply URL** Skriv en URL med följande mönster: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och reply URL. Kontakta den [supportteamet för SAP HANA klienten](https://cloudplatform.sap.com/contact.html) att hämta dessa värden. 

4. I den **SAML-signeringscertifikat** väljer **XML-Metadata för**. Spara metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Om certifikatet inte är aktiva, sedan aktivera den genom att välja den **aktivera nya certifikatet** kryssrutan i Azure AD. 

5. SAP HANA-programmet förväntar SAML-intyg i ett specifikt format. Följande skärmbild visar ett exempel på det här formatet. 

    Här vi har kopplat den **användar-ID** med den **ExtractMailPrefix()** funktion **user.mail**. Detta ger prefixvärdet för användarens e-post, som är unikt användar-ID. Detta användar-ID skickas till SAP HANA-programmet i varje lyckat svar.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-saphana-tutorial/attribute.png)

6. I den **användarattribut** avsnitt i den **enkel inloggning** dialogrutan rutan, gör du följande:

    a. I den **användar-ID** listrutan, Välj **ExtractMailPrefix**.
    
    b. I den **e** listrutan, Välj **user.mail**.

7. Välj knappen **Spara**.

    ![Konfigurera den enkel inloggning spara knappen](./media/active-directory-saas-saphana-tutorial/tutorial_general_400.png)
    
8. Om du vill konfigurera enkel inloggning för SAP HANA-sida, logga in på ditt **HANA XSA webbkonsolen** genom att gå till respektive HTTPS-slutpunkten.

    > [!NOTE]
    > I standardkonfigurationen omdirigerar URL: en begäran till en inloggningssida, som kräver autentiseringsuppgifter för en autentiserad användare för SAP HANA-databas. Den användare som loggar in måste ha behörighet att utföra administrationsuppgifter i SAML.

9. Gå till i webbgränssnitt XSA **SAML-identitetsprovider**. Därifrån väljer den **+** knappen längst ned på skärmen för att visa den **lägga till information om identitet** fönstret. Sedan gör du följande:

    ![Lägg till identitetsleverantören.](./media/active-directory-saas-saphana-tutorial/sap1.png)

    a. I den **lägga till information om identitet** rutan klistra in innehållet i det XML-Metadata (som du hämtade från Azure portal) i den **Metadata** rutan.

    ![Lägg till inställningar för identitetsleverantören.](./media/active-directory-saas-saphana-tutorial/sap2.png)

    b. Om innehållet i XML-dokumentet är giltiga tolkning processen extraherar den information som krävs för den **ämne, enhets-ID och utfärdaren** fält i den **allmänna data** Skrivbordsstorlek. Extraherar även information som krävs för URL-fälten i den **mål** Skrivbordsstorlek, till exempel den **Base Webbadressen och SingleSignOn (*)** fält.

    ![Lägg till inställningar för identitetsleverantören.](./media/active-directory-saas-saphana-tutorial/sap3.png)

    c. I den **namn** för den **allmänna Data** Skrivbordsstorlek, ange ett namn för den nya SAML SSO-identitetsleverantören.

    > [!NOTE]
    > Namnet på SAML IDP är obligatoriskt och måste vara unika. Den visas i listan över tillgängliga SAML-IDPs som visas när du väljer SAML som autentiseringsmetod för SAP HANA XS program kan använda. Exempelvis kan du göra detta i den **autentisering** Skrivbordsstorlek av XS artefakt Administrationsverktyg.

10. Välj **spara** att spara information om SAML-identitetsprovider och lägga till nya SAML IDP i listan över kända SAML IDPs.

    ![Knappen Spara](./media/active-directory-saas-saphana-tutorial/sap4.png)

11. I HANA Studio system-egenskaperna för den **Configuration** fliken, för att filtrera inställningar av **saml**. Justera sedan den **assertion_timeout** från **10sek** till **120 sek**.

    ![assertion_timeout inställning](./media/active-directory-saas-saphana-tutorial/sap7.png)

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) när du ställer in appen! När du lägger till den här appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen i den [inbäddade dokumentation för Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Om du vill skapa en testanvändare i Azure AD, gör du följande:**

1. I den **Azure-portalen**, i den vänstra rutan, Välj den **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](./media/active-directory-saas-saphana-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper**. Välj sedan **alla användare**.
    
    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-saphana-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan **Lägg till** överst i dialogrutan.
 
    ![Knappen Lägg till](./media/active-directory-saas-saphana-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan rutan, gör du följande:
 
    ![Dialogrutan användare](./media/active-directory-saas-saphana-tutorial/create_aaduser_04.png) 

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet**, och sedan skriva ned lösenordet.

    d. Välj **Skapa**.
 
### <a name="create-a-sap-hana-test-user"></a>Skapa en SAP HANA testanvändare

Om du vill aktivera Azure AD-användare att logga in på SAP HANA måste du etablera dem i SAP HANA.
SAP HANA stöder just-in-time-allokering som av aktiveras som standard.

Om du behöver skapa en användare manuellt gör du följande:

>[!NOTE]
>Du kan ändra extern autentisering som användaren använder. De kan autentisera med ett externt system, exempelvis Kerberos. Detaljerad information om externa identiteter, kontakta din [domänadministratören](https://cloudplatform.sap.com/contact.html).

1. Öppna den [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) som administratör och sedan aktivera DB-användaren för SAML SSO.

    ![Skapa användare](./media/active-directory-saas-saphana-tutorial/sap5.png)

2. Markera kryssrutan osynliga till vänster om **SAML**, och välj sedan den **konfigurera** länk.

3. Välj **Lägg till** att lägga till SAML-IDP.  Välj lämpliga SAML IDP och välj sedan **OK**.

4. Lägg till den **externa identitet** (i det här fallet BrittaSimon) eller välj **alla**. Välj sedan **OK**.

    >[!Note]
    >Om den **alla** inte är markerad och klicka sedan användarnamnet i HANA måste exakt matcha namnet på användaren i UPN innan domänsuffix. (Till exempel BrittaSimon@contoso.com blir BrittaSimon i HANA.)

5. I testsyfte kan du tilldela alla **XS** roller till användaren.

    ![tilldela roller](./media/active-directory-saas-saphana-tutorial/sap6.png)

    > [!TIP]
    > Du bör ge behörigheter som är lämpliga för din användningsområden endast.

6. Spara användaren.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP HANA.

![Tilldela rollen][200] 

**Om du vill tilldela SAP HANA Britta Simon utför du följande steg:**

1. Öppna program i Azure-portalen. Gå till vyn katalog och gå till **företagsprogram**. Välj **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SAP HANA**.

    ![Tilldela användare](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_app.png) 

3. Välj på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

4. Välj den **Lägg till** knappen. I den **Lägg uppdrag** dialogrutan **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** dialogrutan **Britta Simon** i den **användare** lista.

6. Klicka på den **Välj** knappen i den **användare och grupper** dialogrutan.

7. Välj den **tilldela** knappen i den **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

När du väljer SAP HANA-panelen i åtkomstpanelen ska hämta loggas du automatiskt till SAP HANA-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



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

