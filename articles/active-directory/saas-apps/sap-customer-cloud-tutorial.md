---
title: 'Självstudier: Azure Active Directory-integration med SAP-moln för kund | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP-moln för kunden.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: 8855a82c1490c916e040f61c07e1116d9125e7e6
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045870"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Självstudier: Azure Active Directory-integration med SAP-moln för kund

Lär dig hur du integrerar SAP-moln för kunder med Azure Active Directory (AD Azure) i den här självstudien.

Integrera SAP-moln för kunder med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAP-moln för kund
- Du kan aktivera användarna att automatiskt få loggat in på SAP-moln för kund (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAP-moln för kunden, behöver du följande objekt:

- En Azure AD-prenumeration
- Ett SAP-moln för kunden enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du kan få en månads utvärdering här: [– prova](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SAP-moln för kund från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Att lägga till SAP-moln för kund från galleriet
Om du vill konfigurera integreringen av SAP-moln för kunden i Azure AD, som du behöver lägga till SAP-moln för kund från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till SAP-moln för kund från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **SAP-moln för kund**.

    ![Skapa en Azure AD-användare för testning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_search.png)

5. I resultatpanelen väljer **SAP-moln för kund**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SAP-moln för kund baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i SAP-moln för kund är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i SAP-moln för kund upprättas.

I SAP-moln för kunden, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAP-moln för kunden, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa ett SAP-moln för kunden testanvändare](#creating-a-sap-cloud-for-customer-test-user)**  – du har en motsvarighet för Britta Simon i SAP-moln för kund som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din SAP-moln för kundprogram.

**Om du vill konfigurera Azure AD enkel inloggning med SAP-moln för kunden, utför du följande steg:**

1. I Azure-portalen på den **SAP-moln för kund** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_samlbase.png)

3. På den **SAP Cloud kunden domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<server name>.crm.ondemand.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<server name>.crm.ondemand.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [SAP-moln för kunden klienten supportteamet](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) att hämta dessa värden. 

4. På den **användarattribut** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_attribute.png)

    a. I **användaridentifierare** väljer den **ExtractMailPrefix()** funktion.

    b. Från den **e** väljer du det användarattribut som du vill använda för din implementering.
    Till exempel om du vill använda EmployeeID som unikt användar-ID och du har lagrat attributvärdet i ExtensionAttribute2, välj sedan user.extensionattribute2.  

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_general_400.png)

7. På den **SAP-moln för konfiguration av Customer** klickar du på **konfigurera SAP-moln för kund** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_configure.png) 

8. Utför följande steg för att få SSO konfigurerats:
   
    a. Logga in på SAP-moln för kundportalen med administratörsbehörighet.
   
    b. Navigera till den **program och vanliga Användarhanteringsuppgift** och klicka på den **identitetsprovider** fliken.
   
    c. Klicka på **nya identitetsprovider** och välj metadata-XML-fil som du har hämtat från Azure-portalen. Genom att importera metadata, överför systemet automatiskt de nödvändiga Signaturcertifikat och krypteringscertifikat.
   
    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    d. Azure Active Directory kräver URL för Konsumenttjänst för försäkran-element i SAML-begäran, så Välj den **inkluderar Assertion URL för Konsumenttjänst** kryssrutan.
   
    e. Klicka på **aktivera enkel inloggning**.
   
    f. Spara ändringarna.
   
    g. Klicka på den **Mina System** fliken.
   
    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. I **Azure AD URL: en inloggning** textrutan klistra in **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.
   
    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    i. Ange om medarbetaren kan manuellt välja mellan att logga in med användar-ID och lösenord eller SSO genom att välja den **manuell identitet providern val av**.
   
    j. I den **SSO URL** avsnittet, ange den URL som ska användas av dina anställda för att logga in på systemet. 
    I den **URL: en som skickas till anställda** lista, kan du välja mellan följande alternativ:
   
    **Icke-SSO-URL**
   
    Systemet skickar bara normala URL: en till medarbetaren. Medarbetaren kan inte logga in med enkel inloggning, och måste använda lösenord eller certifikat i stället.
   
    **URL FÖR ENKEL INLOGGNING** 
   
    Systemet skickar endast URL för enkel inloggning till medarbetaren. Medarbetaren kan logga in med enkel inloggning. Begäran om autentisering dirigeras via IDP: N.
   
    **Automatiskt val**
   
    Om enkel inloggning inte är aktiv, skickar systemet normala URL: en till medarbetaren. Om enkel inloggning är aktiv, kontrollerar systemet om medarbetaren har ett lösenord. Om det finns ett lösenord skickas både SSO Webbadressen och Webbadressen för icke-enkel inloggning till medarbetaren. Om anställda har inget lösenord, skickas bara URL för enkel inloggning till medarbetaren.
   
    k. Spara ändringarna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/sap-customer-cloud-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/sap-customer-cloud-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/sap-customer-cloud-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/sap-customer-cloud-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-sap-cloud-for-customer-test-user"></a>Skapa ett SAP-moln för kunden testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i SAP-moln för kunden. Kontakta [SAP-moln för kundsupporten](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) att lägga till användare i SAP-moln för kund-plattformen. 

> [!NOTE]
> Kontrollera att NameID-värdet måste matcha med användarnamnfältet i SAP-moln för kund-plattformen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP-moln för kunden.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon SAP-moln för kunden, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **SAP-moln för kund**.

    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på SAP-moln för kund-panel i åtkomstpanelen du bör få automatiskt loggat in på ditt SAP-moln för kund-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

