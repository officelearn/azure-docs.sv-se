---
title: 'Självstudier: Azure Active Directory-integrering med ADP Globalview | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ADP Globalview.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ffb6464f-714d-41a9-869a-2b7e5ae9f125
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: fb2391224522ed152ddf23fc6684a1a95e08608e
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35962158"
---
# <a name="tutorial-azure-active-directory-integration-with-adp-globalview"></a>Självstudier: Azure Active Directory-integrering med ADP Globalview

I kursen får lära du att integrera ADP Globalview med Azure Active Directory (AD Azure).

Integrera ADP Globalview med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har tillgång till ADP Globalview
- Du kan aktivera användarna att automatiskt hämta loggat in på ADP Globalview (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ADP Globalview behöver du följande:

- En Azure AD-prenumeration
- En ADP Globalview enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till ADP Globalview från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-adp-globalview-from-the-gallery"></a>Att lägga till ADP Globalview från galleriet
Du måste lägga till ADP Globalview från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av ADP Globalview i Azure AD.

**Utför följande steg för att lägga till ADP Globalview från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **ADP Globalview**.

    ![Skapa en testanvändare i Azure AD](./media/adglobalview-tutorial/tutorial_adpglobalview_search.png)

5. Välj i resultatpanelen **ADP Globalview**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/adglobalview-tutorial/tutorial_adpglobalview_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med ADP Globalview baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i ADP Globalview motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i ADP Globalview upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i ADP Globalview.

Om du vill konfigurera och testa Azure AD enkel inloggning med ADP Globalview, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare ADP Globalview](#creating-an-adp-globalview-test-user)**  – du har en motsvarighet för Britta Simon i ADP Globalview som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet ADP Globalview.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ADP Globalview:**

1. I Azure-portalen på den **ADP Globalview** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/adglobalview-tutorial/tutorial_adpglobalview_samlbase.png)

3. På den **ADP Globalview domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/adglobalview-tutorial/tutorial_adpglobalview_url.png)

     I den **identifierare** textruta Skriv en URL med följande mönster: `https://<subdomain>.globalview.adp.com/federate` eller `https://<subdomain>.globalview.adp.com/federate2`

    > [!NOTE] 
    > Värdet är inte verkliga. Uppdatera värdet med den faktiska identifieraren. Kontakta [ADP Globalview stöd](https://www.adp.com/contact-us/overview.aspx) värdet hämtas.
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/adglobalview-tutorial/tutorial_adpglobalview_certificate.png) 

5. Programmet ADP GlobalView förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. 

6. Följande skärmbild visar ett exempel för den. Anspråk alltid vara **”PersonImmutableID”** och värdet som vi har mappats till ExtensionAttribute2 som innehåller EmployeeID för användaren. Mappning från Azure AD till ADP GlobalView görs här på EmployeeID men du kan mappa den till ett annat värde som också baserat på dina inställningar för programmet. Du kan arbeta med ADP GlobalView teamet först att använda rätt ID för en användare och mappa värdet med den **”PersonImmutableID”** anspråk. Du kan också mappa anspråk e-post och användar-ID som visas i bilden.

    ![Konfigurera enkel inloggning](./media/adglobalview-tutorial/tutorial_adpglobalview_attribute.png)

7. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ------------------- | -------------------- |    
    | personalimmutableid | User.extensionattribute2 |
    | e-post               | User.Mail |
    | användar-ID              | User.userPrincipalName|
    
    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/adglobalview-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/adglobalview-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **OK**.

    > [!NOTE] 
    > Innan du kan konfigurera SAML-kontroll måste du kontakta din [ADP Globalview stöd](https://www.adp.com/contact-us/overview.aspx) och begära värdet för attributet för unik identifierare för din klient. Du behöver det här värdet för att konfigurera det anpassade anspråket för ditt program. 

8. På den **ADP Globalview Configuration** klickar du på **konfigurera ADP Globalview** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/adglobalview-tutorial/tutorial_adpglobalview_configure.png) 

9. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/adglobalview-tutorial/tutorial_general_400.png)

10. Konfigurera enkel inloggning på **ADP Globalview** sida, måste du skicka den hämtade **certifikat (Base64)**, **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** till [ADP Globalview support](https://www.adp.com/contact-us/overview.aspx).

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/adglobalview-tutorial/create_aaduser_01.png) 

2.  Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/adglobalview-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/adglobalview-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/adglobalview-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-adp-globalview-test-user"></a>Skapa en testanvändare ADP Globalview

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i ADP GlobalView. Arbeta med [ADP Globalview stöd](https://www.adp.com/contact-us/overview.aspx) att lägga till användare i ADP GlobalView-konto. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ADP Globalview.

![Tilldela användare][200] 

**Om du vill tilldela ADP Globalview Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **ADP Globalview**.

    ![Konfigurera enkel inloggning](./media/adglobalview-tutorial/tutorial_adpglobalview_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.  

När du klickar på panelen ADP GlobalView på åtkomstpanelen du bör få automatiskt loggat in på ditt ADP GlobalView program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/adglobalview-tutorial/tutorial_general_01.png
[2]: ./media/adglobalview-tutorial/tutorial_general_02.png
[3]: ./media/adglobalview-tutorial/tutorial_general_03.png
[4]: ./media/adglobalview-tutorial/tutorial_general_04.png

[100]: ./media/adglobalview-tutorial/tutorial_general_100.png

[200]: ./media/adglobalview-tutorial/tutorial_general_200.png
[201]: ./media/adglobalview-tutorial/tutorial_general_201.png
[202]: ./media/adglobalview-tutorial/tutorial_general_202.png
[203]: ./media/adglobalview-tutorial/tutorial_general_203.png

