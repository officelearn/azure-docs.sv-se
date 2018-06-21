---
title: 'Självstudier: Azure Active Directory-integrering med PurelyHR | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PurelyHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 86a9c454-596d-4902-829a-fe126708f739
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.openlocfilehash: ba24422f7ff5759153a6e067cc61251b43a7a091
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286940"
---
# <a name="tutorial-azure-active-directory-integration-with-purelyhr"></a>Självstudier: Azure Active Directory-integrering med PurelyHR

I kursen får lära du att integrera PurelyHR med Azure Active Directory (AD Azure).

Integrera PurelyHR med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till PurelyHR
- Du kan aktivera användarna att automatiskt hämta loggat in på PurelyHR (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med PurelyHR, behöver du följande:

- En Azure AD-prenumeration
- En PurelyHR enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till PurelyHR från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-purelyhr-from-the-gallery"></a>Att lägga till PurelyHR från galleriet
Du måste lägga till PurelyHR från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av PurelyHR i Azure AD.

**Utför följande steg för att lägga till PurelyHR från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **PurelyHR**.

    ![Skapa en testanvändare i Azure AD](./media/purelyhr-tutorial/tutorial_purelyhr_search.png)

5. Välj i resultatpanelen **PurelyHR**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/purelyhr-tutorial/tutorial_purelyhr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med PurelyHR baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i PurelyHR motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i PurelyHR upprättas.

I PurelyHR, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med PurelyHR, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare PurelyHR](#creating-a-purelyhr-test-user)**  – du har en motsvarighet för Britta Simon i PurelyHR som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt PurelyHR program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med PurelyHR:**

1. I Azure-portalen på den **PurelyHR** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/purelyhr-tutorial/tutorial_purelyhr_samlbase.png)

3. På den **PurelyHR domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/purelyhr-tutorial/tutorial_purelyhr_url.png)
   
    I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<companyID>.purelyhr.com/sso-consume`

4. Kontrollera **visa avancerade inställningar för URL: en**, om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/purelyhr-tutorial/tutorial_purelyhr_url1.png)
    
    I den **inloggnings-URL** textruta Skriv det värde som använder följande mönster: `https://<companyID>.purelyhr.com/sso-initiate`
     
    > [!NOTE]
    > Dessa värden är inte verkligt. Uppdatera dessa värden med den faktiska Reply URL och inloggnings-URL. Kontakta [PurelyHR klienten supportteamet](http://support.purelyhr.com/) att hämta dessa värden. 

5. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/purelyhr-tutorial/tutorial_purelyhr_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/purelyhr-tutorial/tutorial_general_400.png)
    
7. På den **PurelyHR Configuration** klickar du på **konfigurera PurelyHR** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/purelyhr-tutorial/tutorial_purelyhr_configure.png) 

8. Konfigurera enkel inloggning på **PurelyHR** sida, logga in på webbplatsen som en administratör.

9. Öppna den **instrumentpanelen** från alternativen i verktygsfältet och på **SSO-inställningarna**.

10. Klistra in värden i rutorna enligt beskrivningen nedan-

    ![Konfigurera enkel inloggning](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Öppna den **Certificate(Bas64)** hämtas från Azure-portalen i anteckningar och kopiera certifikatvärdet. Klistra in det kopierade värdet till den **X.509-certifikat** rutan.

    b. I den **Idp utfärdar-URL** klistra in den **SAML enhets-ID** kopieras från Azure-portalen.

    c. I den **Idp slutpunkts-URL** klistra in den **SAML inloggning tjänst-URL för enkel** kopieras från Azure-portalen. 

    d. Kontrollera den **skapa automatiskt användare** kryssrutan för att aktivera automatisk användaretablering i PurelyHR.

    e. Klicka på **spara ändringar** spara inställningarna.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/purelyhr-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/purelyhr-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/purelyhr-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/purelyhr-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-purelyhr-test-user"></a>Skapa en testanvändare PurelyHR

Om du vill aktivera Azure AD-användare kan logga in på PurelyHR etableras de i PurelyHR. Etablering är en automatisk uppgift i PurelyHR, och inga manuella steg krävs när automatisk användaretablering är aktiverad.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till PurelyHR.

![Tilldela användare][200] 

**Om du vill tilldela PurelyHR Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **PurelyHR**.

    ![Konfigurera enkel inloggning](./media/purelyhr-tutorial/tutorial_purelyhr_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

Klicka på panelen upptar LMS på åtkomstpanelen, du får automatiskt loggat in på ditt program skulle kunna ta emot LMS.

Mer information om åtkomstpanelen finns i. [Introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/purelyhr-tutorial/tutorial_general_01.png
[2]: ./media/purelyhr-tutorial/tutorial_general_02.png
[3]: ./media/purelyhr-tutorial/tutorial_general_03.png
[4]: ./media/purelyhr-tutorial/tutorial_general_04.png

[100]: ./media/purelyhr-tutorial/tutorial_general_100.png

[200]: ./media/purelyhr-tutorial/tutorial_general_200.png
[201]: ./media/purelyhr-tutorial/tutorial_general_201.png
[202]: ./media/purelyhr-tutorial/tutorial_general_202.png
[203]: ./media/purelyhr-tutorial/tutorial_general_203.png

