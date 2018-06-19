---
title: 'Självstudier: Azure Active Directory-integrering med SD element | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SD-element.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: a6bad0faa6247036b1e0eb6e1aa8ca95239193db
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35964001"
---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>Självstudier: Azure Active Directory-integrering med SD-element

I kursen får lära du att integrera SD-element med Azure Active Directory (AD Azure).

Integrera SD-element med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SD-element
- Du kan aktivera användarna att automatiskt hämta loggat in på SD-element (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SD-element behöver du följande:

- En Azure AD-prenumeration
- Ett SD-element enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SD-element från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sd-elements-from-the-gallery"></a>Att lägga till SD-element från galleriet
Du måste lägga till SD-element från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SD-element i Azure AD.

**Utför följande steg för att lägga till SD-element från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **SD element**.

    ![Skapa en testanvändare i Azure AD](./media/sd-elements-tutorial/tutorial_sdelements_search.png)

5. Välj i resultatpanelen **SD element**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/sd-elements-tutorial/tutorial_sdelements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med SD-element som baseras på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i SD-element för en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i SD-element upprättas.

I SD-element, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med SD-element, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare SD element](#creating-a-sd-elements-test-user)**  – har en motsvarighet för Britta Simon SD-element som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för SD-element.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SD-element:**

1. I Azure-portalen på den **SD element** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/sd-elements-tutorial/tutorial_sdelements_samlbase.png)

3. På den **SD element domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/sd-elements-tutorial/tutorial_sdelements_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare och Reply-URL. Kontakta [SD element supportteam](mailto:support@sdelements.com) att hämta dessa värden.

4. Programmet SD-element förväntas SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den **”användarattribut”** för programmet. Följande skärmbild visar ett exempel för det här.

    ![Konfigurera enkel inloggning](./media/sd-elements-tutorial/tutorial_sdelements_attribute.png)

5. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden och utför följande steg: 

    | Attributnamn | Attributvärde |
    | --- | --- |
    | e-post |User.Mail |
    | Förnamn |User.givenName |
    | Efternamn |User.surname |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/sd-elements-tutorial/tutorial_officespace_04.png)

    ![Konfigurera enkel inloggning](./media/sd-elements-tutorial/tutorial_officespace_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Klicka på **OK**.
 
6. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/sd-elements-tutorial/tutorial_sdelements_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/sd-elements-tutorial/tutorial_general_400.png)

8. På den **SD element Configuration** klickar du på **konfigurera SD-element** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/sd-elements-tutorial/tutorial_sdelements_configure.png)

9. För att få enkel inloggning aktiverad, kontakta din [SD element supportteam](mailto:support@sdelements.com) och ge dem hämtade certifikatfilen. 

10. I ett annat webbläsarfönster inloggning till SD-element-klient som administratör.

11. Klicka på menyn högst upp **System**, och sedan **enkel inloggning**. 
   
    ![Konfigurera enkel inloggning](./media/sd-elements-tutorial/tutorial_sd-elements_09.png) 

12. På den **inställningar för enkel inloggning** dialogrutan, utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/sd-elements-tutorial/tutorial_sd-elements_10.png) 
   
    a. Som **SSO typen**väljer **SAML**.
   
    b. I den **identitet providern enhets-ID** textruta klistra in värdet för **SAML enhets-ID**, som du har kopierat från Azure-portalen. 
   
    c. I den **identitet providern enkel inloggning** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen. 
   
    d. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/sd-elements-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/sd-elements-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/sd-elements-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/sd-elements-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-sd-elements-test-user"></a>Skapa en testanvändare SD-element

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i SD-element. SD-element är skapa SD element användare en manuell aktivitet.

**Utför följande steg för att skapa Britta Simon i SD-element:**

1. I ett webbläsarfönster inloggning till webbplatsen för företagets SD-element som administratör.

2. Klicka på menyn högst upp **Användarhantering**, och sedan **användare**.
   
    ![Skapa en testanvändare SD-element](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

3. Klicka på **lägga till nya användare**.
   
    ![Skapa en testanvändare SD-element](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)
 
4. På den **Lägg till nya användare** dialogrutan, utför följande steg:
   
    ![Skapa en testanvändare SD-element](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 
   
    a. I den **e-post** textruta ange e-postadress för användaren som **brittasimon@contoso.com**.
   
    b. I den **Förnamn** textruta Ange först namnet på användaren som **Britta**.
   
    c. I den **efternamn** textruta Ange efternamn för användaren som **Simon**.
   
    d. Som **rollen**väljer **användaren**. 
   
    e. Klicka på **skapa användare**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SD-element.

![Tilldela användare][200] 

**Utför följande steg om du vill tilldela Britta Simon till SD-element:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SD element**.

    ![Konfigurera enkel inloggning](./media/sd-elements-tutorial/tutorial_sdelements_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.
  
När du klickar på panelen SD-element i åtkomstpanelen du bör få automatiskt loggat in på ditt SD-element-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/sd-elements-tutorial/tutorial_general_04.png

[100]: ./media/sd-elements-tutorial/tutorial_general_100.png

[200]: ./media/sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/sd-elements-tutorial/tutorial_general_201.png
[202]: ./media/sd-elements-tutorial/tutorial_general_202.png
[203]: ./media/sd-elements-tutorial/tutorial_general_203.png

