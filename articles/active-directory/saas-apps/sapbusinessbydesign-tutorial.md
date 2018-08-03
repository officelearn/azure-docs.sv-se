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
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: a7a08eb6062f134058bb63f5a3e2a78f661026ff
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445224"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Självstudier: Azure Active Directory-integration med SAP Business ByDesign

I den här självstudien får du lära dig hur du integrerar SAP Business ByDesign med Azure Active Directory (AD Azure).

Integrera SAP Business ByDesign med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAP Business ByDesign.
- Du kan aktivera användarna att automatiskt få loggat in på SAP Business ByDesign (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAP Business ByDesign, behöver du följande objekt:

- En Azure AD-prenumeration
- En SAP Business ByDesign enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SAP Business ByDesign från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Att lägga till SAP Business ByDesign från galleriet
För att konfigurera integrering av SAP Business ByDesign i Azure AD, som du behöver lägga till SAP Business ByDesign från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SAP Business ByDesign från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **SAP Business ByDesign**väljer **SAP Business ByDesign** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![SAP Business ByDesign i resultatlistan](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SAP Business ByDesign baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i SAP Business ByDesign är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i SAP Business ByDesign upprättas.

I SAP Business ByDesign tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAP Business ByDesign, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare för SAP Business ByDesign](#create-an-sap-business-bydesign-test-user)**  – du har en motsvarighet för Britta Simon i SAP Business ByDesign som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i SAP Business ByDesign programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SAP Business ByDesign:**

1. I Azure-portalen på den **SAP Business ByDesign** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_samlbase.png)

1. På den **SAP Business ByDesign domän och URL: er** avsnittet, utför följande steg:

    ![SAP Business ByDesign domän och URL: er med enkel inloggning för information](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<servername>.sapbydesign.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<servername>.sapbydesign.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [SAP Business ByDesign Client supportteamet](https://www.sap.com/products/cloud-analytics.support.html) att hämta dessa värden.

1. På den **användarattribut** avsnittet, utför följande steg:

    ![SAP Business ByDesign attributet avsnittet](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_attribute.png)
    
    a. I **användaridentifierare** väljer den **ExtractMailPrefix()** funktion.
    
    b. Från den **e** väljer du det användarattribut som du vill använda för din implementering. Till exempel om du vill använda EmployeeID som unikt användar-ID och du har lagrat attributvärdet i ExtensionAttribute2, välj sedan user.extensionattribute2.     

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/sapbusinessbydesign-tutorial/tutorial_general_400.png)

1. På den **SAP Business ByDesign Configuration** klickar du på **konfigurera SAP Business ByDesign** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![SAP Business ByDesign konfiguration](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_configure.png) 

1. Utför följande steg för att få SSO konfigurerats för ditt program:
   
    a. Logga in på din SAP Business ByDesign portal med administratörsbehörighet.
   
    b. Gå till **program och vanliga Användarhanteringsuppgift** och klicka på den **identitetsprovider** fliken.
   
    c. Klicka på **nya identitetsprovider** och välj den XML-fil som du har hämtat från Azure-portalen. Genom att importera metadata, överför systemet automatiskt de nödvändiga Signaturcertifikat och krypteringscertifikat.
   
    ![Konfigurera enkel inloggning](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d. Att inkludera den **URL för Konsumenttjänst för försäkran** i SAML-begäran väljer **inkluderar Assertion URL för Konsumenttjänst**.
   
    e. Klicka på **aktivera enkel inloggning**.
   
    f. Spara ändringarna.
   
    g. Klicka på den **Mina System** fliken.
   
    ![Konfigurera enkel inloggning](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. Klistra in **SAML enkel inloggning för tjänst-URL**, som det har kopierats från Azure-portalen till den **Azure AD URL: en inloggning** textrutan.
   
    ![Konfigurera enkel inloggning](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    i. Ange om medarbetaren kan manuellt välja mellan att logga in med användar-ID och lösenord eller SSO genom att välja **manuell identitet providern val av**.
   
    j. I den **SSO URL** avsnittet, ange den URL som ska användas av anställda möjlighet att logga in på systemet. 
    I den URL: en skickas till anställda listrutan kan du välja mellan följande alternativ:
   
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

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/sapbusinessbydesign-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/sapbusinessbydesign-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/sapbusinessbydesign-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/sapbusinessbydesign-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-sap-business-bydesign-test-user"></a>Skapa en testanvändare för SAP Business ByDesign

I det här avsnittet skapar du en användare som kallas Britta Simon i SAP Business ByDesign. Kontakta [SAP Business ByDesign Client supportteamet](https://www.sap.com/products/cloud-analytics.support.html) att lägga till användare i SAP Business ByDesign-plattformen. 

> [!NOTE]
> Kontrollera att NameID-värdet måste matcha med fältet för användarnamn i SAP Business ByDesign-plattformen.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP Business ByDesign.

![Tilldela rollen][200] 

**Om du vill tilldela SAP Business ByDesign Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **SAP Business ByDesign**.

    ![SAP Business ByDesign länken i listan med program](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen SAP Business ByDesign i åtkomstpanelen du bör få automatiskt loggat in på ditt SAP Business ByDesign program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/sapbusinessbydesign-tutorial/tutorial_general_04.png

[100]: ./media/sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/sapbusinessbydesign-tutorial/tutorial_general_201.png
[202]: ./media/sapbusinessbydesign-tutorial/tutorial_general_202.png
[203]: ./media/sapbusinessbydesign-tutorial/tutorial_general_203.png

