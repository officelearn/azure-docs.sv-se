---
title: 'Självstudier: Azure Active Directory-integrering med SAP Business ByDesign | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Business ByDesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: 23a0f202cabf39036a3a405894c814655e3f3cd9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352226"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Självstudier: Azure Active Directory-integration med SAP Business ByDesign

Lär dig hur du integrerar SAP Business ByDesign med Azure Active Directory (AD Azure) i den här självstudiekursen.

Integrera SAP Business ByDesign med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAP Business ByDesign.
- Du kan aktivera användarna att automatiskt hämta loggat in på SAP Business ByDesign (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAP Business ByDesign behöver du följande:

- En Azure AD-prenumeration
- En SAP Business ByDesign enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SAP Business ByDesign från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Att lägga till SAP Business ByDesign från galleriet
Du måste lägga till SAP Business ByDesign från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SAP Business ByDesign i Azure AD.

**Utför följande steg för att lägga till SAP Business ByDesign från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SAP Business ByDesign**väljer **SAP Business ByDesign** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![SAP Business ByDesign i resultatlistan](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med SAP Business ByDesign baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i SAP Business ByDesign motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SAP Business ByDesign upprättas.

I SAP Business ByDesign tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAP Business ByDesign, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en SAP Business ByDesign testanvändare](#create-an-sap-business-bydesign-test-user)**  – du har en motsvarighet för Britta Simon i SAP Business ByDesign som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för SAP Business ByDesign.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SAP Business ByDesign:**

1. I Azure-portalen på den **SAP Business ByDesign** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_samlbase.png)

3. På den **SAP Business ByDesign domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och SAP Business ByDesign domän med enkel inloggning information](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<servername>.sapbydesign.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<servername>.sapbydesign.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [SAP Business ByDesign Client supportteamet](https://www.sap.com/products/cloud-analytics.support.html) att hämta dessa värden.

4. På den **användarattribut** avsnittet, utför följande steg:

    ![SAP Business ByDesign attributet avsnitt](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_attribute.png)
    
    a. I **användar-ID** väljer den **ExtractMailPrefix()** funktion.
    
    b. Från den **e** väljer användarattribut som du vill använda för din implementering. Till exempel om du vill använda EmployeeID som unikt identifierare och du har lagrat attributvärdet i ExtensionAttribute2, välj sedan user.extensionattribute2.     

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_400.png)

7. På den **SAP Business ByDesign Configuration** klickar du på **konfigurera SAP Business ByDesign** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![SAP Business ByDesign konfiguration](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_configure.png) 

8. Utför följande steg för att få SSO konfigurerats för ditt program:
   
    a. Logga in på din SAP Business ByDesign portal med administratörsrättigheter.
   
    b. Gå till **program och vanliga användare hanteringsaktivitet** och klicka på den **identitetsleverantör** fliken.
   
    c. Klicka på **nya identitetsleverantör** och välj metadata XML-filen som du har hämtat från Azure-portalen. Genom att importera metadata filöverföringar systemet automatiskt krävs Signaturcertifikat och certifikat för kryptering.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d. Att inkludera den **Assertion konsument-tjänstens URL** i SAML-begäran Välj **inkludera Assertion konsumenten tjänsten URL**.
   
    e. Klicka på **aktivera enkel inloggning**.
   
    f. Spara ändringarna.
   
    g. Klicka på den **Mina System** fliken.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. Klistra in **SAML enkel inloggning Tjänstwebbadress**, som du har kopierat från Azure portal till den **Azure AD URL: en inloggning** textruta.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    i. Ange om medarbetaren kan välja mellan att logga in med användar-ID och lösenord eller enkel inloggning genom att välja **manuell identitet providern markeringen**.
   
    j. I den **SSO URL** avsnitt, ange den URL som ska användas av anställda att logga in på systemet. 
    I den URL skickas till medarbetare listrutan, kan du välja mellan följande alternativ:
   
    **URL: en icke-SSO**
   
    Systemet skickar bara normala URL-Adressen till anställde. Medarbetaren kan inte logga in med enkel inloggning, och måste använda lösenordet eller certifikatet i stället.
   
    **URL FÖR ENKEL INLOGGNING** 
   
    SSO URL skickas till medarbetaren. Medarbetaren kan logga in med enkel inloggning. Autentiseringsbegäran dirigeras via IdP.
   
    **Automatiskt val**
   
    Om enkel inloggning inte är aktiv, skickar systemets normala URL: en till medarbetaren. Om enkel inloggning är aktiv, kontrollerar systemet om medarbetaren har ett lösenord. Om det finns ett lösenord skickas både SSO Webbadressen och icke-SSO till medarbetaren. Om medarbetaren har inget lösenord, skickas endast URL för enkel inloggning till medarbetaren.
   
    k. Spara ändringarna.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-sap-business-bydesign-test-user"></a>Skapa en SAP Business ByDesign testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i SAP Business ByDesign. Se tillsammans med [SAP Business ByDesign Client supportteamet](https://www.sap.com/products/cloud-analytics.support.html) att lägga till användare i SAP Business ByDesign-plattformen. 

> [!NOTE]
> Kontrollera att NameID värdet ska vara identiskt med fältet för användarnamn i SAP Business ByDesign-plattformen.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP Business ByDesign.

![Tilldela rollen][200] 

**Om du vill tilldela SAP Business ByDesign Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SAP Business ByDesign**.

    ![Länken SAP Business ByDesign i listan med program](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen SAP Business ByDesign på åtkomstpanelen du bör få automatiskt loggat in på ditt SAP Business ByDesign program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png

