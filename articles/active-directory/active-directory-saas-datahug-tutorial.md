---
title: "Självstudier: Azure Active Directory-integrering med Datahug | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Datahug."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5c0dc1ea-7ff4-4554-b60b-0f2fa9f5abaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: jeedes
ms.openlocfilehash: ec431dd5ccfa53e4b975e46da247704dd1e15c2c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-datahug"></a>Självstudier: Azure Active Directory-integrering med Datahug

I kursen får lära du att integrera Datahug med Azure Active Directory (AD Azure).

Integrera Datahug med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Datahug
- Du kan aktivera användarna att automatiskt hämta loggat in på Datahug (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns i. [Vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Datahug, behöver du följande:

- En Azure AD-prenumeration
- En Datahug enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Datahug från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-datahug-from-the-gallery"></a>Att lägga till Datahug från galleriet
Du måste lägga till Datahug från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Datahug i Azure AD.

**Utför följande steg för att lägga till Datahug från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Datahug**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_search.png)

5. Välj i resultatpanelen **Datahug**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Datahug baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Datahug motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Datahug upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Datahug.

Om du vill konfigurera och testa Azure AD enkel inloggning med Datahug, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Datahug](#creating-a-datahug-test-user)**  – du har en motsvarighet för Britta Simon i Datahug som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Datahug program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Datahug:**

1. I Azure-portalen på den **Datahug** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_samlbase.png)

3. På den **Datahug domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_ur1.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster:`https://apps.datahug.com/identity/<uniqueID>`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster:`https://apps.datahug.com/identity/<uniqueID>/acs`

4. Kontrollera **visa avancerade inställningar för URL: en**. Om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_url2.png)

    I den **inloggnings-URL** textruta Skriv en URL som:`https://apps.datahug.com/`
     
    > [!NOTE] 
    > Dessa värden är inte verkligt. Uppdatera dessa värden med den faktiska identifierare och Reply-URL. Här rekommenderar vi att du om du vill använda det unika värdet på strängen i identifierare och Reply-URL. Kontakta [Datahug klienten supportteamet](http://datahug.com/about/contact-us/) att hämta dessa värden. 

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_certificate.png) 

6.  Kontrollera **”visa avancerade inställningar för signering av certifikat”** och utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_cert.png)

    a. I **signering alternativet**väljer **signera SAML assertion**.
    
    b. I **signering algoritmen**väljer **SHA1**.
 
7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-datahug-tutorial/tutorial_general_400.png)
    
8. På den **Datahug Configuration** klickar du på **konfigurera Datahug** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID** och **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_configure.png) 

9. Konfigurera enkel inloggning på **Datahug** sida, måste du skicka den hämtade **XML-Metadata för**, **SAML enhets-ID** och **SAML enkel inloggning tjänst-URL**  till [Datahug support](http://datahug.com/about/contact-us/). De konfigurera programmet så att SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-datahug-test-user"></a>Skapa en testanvändare Datahug

Om du vill aktivera Azure AD-användare kan logga in på Datahug etableras de i Datahug.  
När Datahug, etablering är en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på webbplatsen Datahug företag som administratör.

2. Hovra över den **kugge** i övre högra hörnet och klicka på **inställningar**
   
   ![Lägga till medarbetare](./media/active-directory-saas-datahug-tutorial/1.png)

3. Välj **personer** och klicka på den **Lägg till användare** fliken

    ![Lägga till medarbetare](./media/active-directory-saas-datahug-tutorial/2.png)

4. Skriv e-postadressen till den person som du vill skapa ett konto för och klicka på **Lägg till**.

    ![Lägga till medarbetare](./media/active-directory-saas-datahug-tutorial/3.png)

    > [!NOTE] 
    > Du kan skicka e-post för registrering till användare genom att välja **skicka välkomstmeddelandet** kryssrutan.  
    > Om du skapar ett konto för Salesforce inte skicka välkomstmeddelandet.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Datahug.

![Tilldela användare][200] 

**Om du vill tilldela Datahug Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Datahug**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.
När du klickar på panelen Datahug på åtkomstpanelen du bör få automatiskt loggat in på ditt Datahug program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_203.png

