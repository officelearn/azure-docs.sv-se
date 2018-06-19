---
title: 'Självstudier: Azure Active Directory-integrering med RedBrick Health | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RedBrick hälsa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26290c65-9aa3-42ab-8ba5-901b14dc8e73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jeedes
ms.openlocfilehash: 00ab7d894c50a18cb27b3420b51fee38fcf8d58f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35977556"
---
# <a name="tutorial-azure-active-directory-integration-with-redbrick-health"></a>Självstudier: Azure Active Directory-integrering med RedBrick hälsa

I kursen får lära du att integrera RedBrick hälsa med Azure Active Directory (AD Azure).

Integrera RedBrick hälsa med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till RedBrick hälsa.
- Du kan aktivera användarna att automatiskt hämta loggat in på RedBrick hälsa (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med RedBrick Health, behöver du följande:

- En Azure AD-prenumeration
- En RedBrick hälsa enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till RedBrick hälsa från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-redbrick-health-from-the-gallery"></a>Att lägga till RedBrick hälsa från galleriet
Du måste lägga till RedBrick hälsa från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av RedBrick hälsa i Azure AD.

**Utför följande steg för att lägga till RedBrick hälsa från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **RedBrick hälsa**väljer **RedBrick hälsa** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![RedBrick hälsa i resultatlistan](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med RedBrick hälsotillstånd baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i RedBrick hälsa till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i RedBrick hälsa upprättas.

I RedBrick hälsa, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med RedBrick Health, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare RedBrick hälsa](#create-a-redbrick-health-test-user)**  – har en motsvarighet för Britta Simon RedBrick hälsa som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet RedBrick hälsa.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med RedBrick Health:**

1. I Azure-portalen på den **RedBrick hälsa** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_samlbase.png)

3. På den **RedBrick hälsa domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och redBrick hälsa domän med enkel inloggning information](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_url.png)

    a. I den **identifierare** textruta, ange ett URL-Adressen: `http://www.redbrickhealth.com`
    
    b. I den **Reply URL** textruta, ange ett URL-Adressen: `https://sso-intg.redbrickhealth.com/sp/ACS.saml2`
    
    För produktionsmiljö: `https://sso.redbrickhealth.com/sp/ACS.saml2`

    c. Klicka på **visa avancerade inställningar för URL: en**.
    
    ![URL: er och redBrick hälsa domän med enkel inloggning information](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_url1.png)

    d. I den **Relay tillstånd** textruta Skriv en URL med följande mönster: `https://api-sso2.redbricktest.com/identity/sso/nbound?target=https://vanity9-sso2.redbrickdev.com/portal&connection=<companyname>conn1`
    
    > [!NOTE] 
    > Relay tillstånd värdet är inte verkliga. Uppdatera det här värdet med det aktuella Relay-läget. Kontakta [RedBrick hälsa supportteamet](https://home.redbrickhealth.com/contact/) att hämta det här värdet.

4. Programmet RedBrick hälsa förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. Dessa anspråk kundspecifika och beror på dina behov. Följande valfria anspråk är exempel som som du kan konfigurera för ditt program. Du kan hantera värden för attributen från den **användarattribut** avsnitt på sidan för integrering av programmet.

    ![Konfigurera enkel inloggning](./media/redbrickhealth-tutorial/attribute.png)

5. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:

    | Attributnamn | Attributvärde |
    | ---------------| ----------------|
    | huvudnamn | ********** |
    | klient-ID | ********** |
    | loggpost-id | ********** |
    
    > [!NOTE]
    > Dessa värden är endast referens syfte. Du måste definiera attribut enligt organisationens krav. Kontakta [RedBrick hälsa supportteamet](https://home.redbrickhealth.com/contact/) att få mer information om nödvändiga anspråk.
    
    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.
    
    ![Konfigurera enkel inloggning](./media/redbrickhealth-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurera enkel inloggning](./media/redbrickhealth-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Lämna den **Namespace** tomt.
    
    e. Klicka på **OK**.

6. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/redbrickhealth-tutorial/tutorial_general_400.png)

8. På den **RedBrick Hälsokonfiguration** klickar du på **konfigurera RedBrick hälsa** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID** från den **Snabbreferens avsnitt.**

    ![RedBrick Health-konfiguration](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_configure.png) 

9. Konfigurera enkel inloggning på **RedBrick hälsa** sida, måste du skicka den hämtade **Certificate(Base64)** och **SAML enhets-ID** till [RedBrick hälsa supportteam](https://home.redbrickhealth.com/contact/). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/redbrickhealth-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/redbrickhealth-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/redbrickhealth-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/redbrickhealth-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-a-redbrick-health-test-user"></a>Skapa en testanvändare RedBrick hälsa

I det här avsnittet kan du skapa en användare som kallas Britta Simon i RedBrick hälsa. Arbeta med [RedBrick hälsa supportteamet](https://home.redbrickhealth.com/contact/) att lägga till användare i RedBrick hälsa-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till RedBrick hälsa.

![Tilldela rollen][200] 

**Om du vill tilldela RedBrick hälsa Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **RedBrick hälsa**.

    ![Länken RedBrick hälsa i listan med program](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen RedBrick hälsa på åtkomstpanelen du ska hämta automatiskt loggat in i tillämpningsprogrammet RedBrick hälsa.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/redbrickhealth-tutorial/tutorial_general_01.png
[2]: ./media/redbrickhealth-tutorial/tutorial_general_02.png
[3]: ./media/redbrickhealth-tutorial/tutorial_general_03.png
[4]: ./media/redbrickhealth-tutorial/tutorial_general_04.png

[100]: ./media/redbrickhealth-tutorial/tutorial_general_100.png

[200]: ./media/redbrickhealth-tutorial/tutorial_general_200.png
[201]: ./media/redbrickhealth-tutorial/tutorial_general_201.png
[202]: ./media/redbrickhealth-tutorial/tutorial_general_202.png
[203]: ./media/redbrickhealth-tutorial/tutorial_general_203.png

