---
title: 'Självstudier: Azure Active Directory-integrering med SAP-molnet för kund | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP moln för kunden.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: 9628090e3573c0e6874f860a991d14a225d8aa02
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35964092"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Självstudier: Azure Active Directory-integrering med SAP-molnet för kunden

I kursen får lära du att integrera SAP-molnet för kunden med Azure Active Directory (AD Azure).

Integrera SAP-molnet för kunden med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAP moln för kunden
- Du kan aktivera användarna att automatiskt hämta loggat in på molnet SAP för kund (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med SAP-molnet för kunden, behöver du följande:

- En Azure AD-prenumeration
- Ett SAP-moln för kunden enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad här: [utvärderingsversion erbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägger till SAP-molnet för kunden från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Lägger till SAP-molnet för kunden från galleriet
Om du vill konfigurera molnet för SAP-integrering för kund till Azure AD som du behöver lägga till SAP-molnet för kunden från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till SAP-molnet för kunden från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **SAP-molnet för kunden**.

    ![Skapa en testanvändare i Azure AD](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_search.png)

5. Välj i resultatpanelen **SAP-molnet för kunden**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med SAP-molnet för kund baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i SAP-molnet för kund till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SAP-molnet för kunden upprättas.

I SAP moln för kunden, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAP-molnet för kunden, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa ett SAP-moln för kunden testanvändare](#creating-a-sap-cloud-for-customer-test-user)**  – du har en motsvarighet för Britta Simon i SAP-molnet för kund som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din SAP-molnet för kund-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SAP-molnet för kunden:**

1. I Azure-portalen på den **SAP-molnet för kunden** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_samlbase.png)

3. På den **SAP molnet kunden domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<server name>.crm.ondemand.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<server name>.crm.ondemand.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [SAP-molnet för kunden klienten supportteamet](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) att hämta dessa värden. 

4. På den **användarattribut** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_attribute.png)

    a. I **användar-ID** väljer den **ExtractMailPrefix()** funktion.

    b. Från den **e** väljer användarattribut som du vill använda för din implementering.
    Till exempel om du vill använda EmployeeID som unikt identifierare och du har lagrat attributvärdet i ExtensionAttribute2, välj sedan user.extensionattribute2.  

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_general_400.png)

7. På den **SAP-molnet för konfiguration av Customer** klickar du på **SAP konfigurera molnet för kunden** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_configure.png) 

8. Utför följande steg för att få SSO konfigurerats:
   
    a. Logga in på molnet SAP för kundens portal med administratörsrättigheter.
   
    b. Navigera till den **program och vanliga användare hanteringsaktivitet** och klicka på den **identitetsleverantör** fliken.
   
    c. Klicka på **nya identitetsleverantör** och välj metadata XML-fil som du har hämtat från Azure-portalen. Genom att importera metadata filöverföringar systemet automatiskt krävs Signaturcertifikat och certifikat för kryptering.
   
    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    d. Azure Active Directory kräver element Assertion konsumenten tjänst-URL i SAML-begäran, så du väljer den **inkludera Assertion konsumenten tjänsten URL** kryssrutan.
   
    e. Klicka på **aktivera enkel inloggning**.
   
    f. Spara ändringarna.
   
    g. Klicka på den **Mina System** fliken.
   
    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. I **Azure AD URL: en inloggning** textruta klistra in **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.
   
    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    i. Ange om medarbetaren kan välja mellan att logga in med användar-ID och lösenord eller enkel inloggning genom att välja den **manuell identitet providern markeringen**.
   
    j. I den **SSO URL** avsnitt, ange den URL som ska användas av dina anställda för att logga in på systemet. 
    I den **URL skickas till medarbetare** lista kan du välja mellan följande alternativ:
   
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

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/sap-customer-cloud-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/sap-customer-cloud-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/sap-customer-cloud-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/sap-customer-cloud-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-sap-cloud-for-customer-test-user"></a>Skapa ett SAP-moln för kunden testanvändare

I det här avsnittet kan du skapa en användare som kallas Britta Simon i SAP-molnet för kunden. Se tillsammans med [SAP-molnet för kundtjänst](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) att lägga till användare i SAP-molnet för kund-plattformen. 

> [!NOTE]
> Kontrollera att NameID värdet ska vara identiskt med fältet för användarnamn i SAP-molnet för kund-plattformen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP-molnet för kunden.

![Tilldela användare][200] 

**Om du vill tilldela moln SAP Britta Simon för kunden, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SAP-molnet för kunden**.

    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på SAP-molnet för kund-panelen på åtkomstpanelen du ska hämta automatiskt loggat in på din SAP-molnet för kund-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sap-customer-cloud-tutorial/tutorial_general_01.png
[2]: ./media/sap-customer-cloud-tutorial/tutorial_general_02.png
[3]: ./media/sap-customer-cloud-tutorial/tutorial_general_03.png
[4]: ./media/sap-customer-cloud-tutorial/tutorial_general_04.png

[100]: ./media/sap-customer-cloud-tutorial/tutorial_general_100.png

[200]: ./media/sap-customer-cloud-tutorial/tutorial_general_200.png
[201]: ./media/sap-customer-cloud-tutorial/tutorial_general_201.png
[202]: ./media/sap-customer-cloud-tutorial/tutorial_general_202.png
[203]: ./media/sap-customer-cloud-tutorial/tutorial_general_203.png

