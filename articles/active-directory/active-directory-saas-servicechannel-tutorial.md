---
title: 'Självstudier: Azure Active Directory-integrering med ServiceChannel | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ServiceChannel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c3546eab-96b5-489b-a309-b895eb428053
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/3/2017
ms.author: jeedes
ms.openlocfilehash: 2b1c7461cb1ed232290cf78a03449611f9ef95da
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349659"
---
# <a name="tutorial-azure-active-directory-integration-with-servicechannel"></a>Självstudier: Azure Active Directory-integrering med ServiceChannel

I kursen får lära du att integrera ServiceChannel med Azure Active Directory (AD Azure).

Integrera ServiceChannel med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ServiceChannel
- Du kan aktivera användarna att automatiskt hämta loggat in på ServiceChannel (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - till Azure-hanteringsportalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med ServiceChannel, behöver du följande:

- En Azure AD-prenumeration
- En ServiceChannel enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion av en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till ServiceChannel från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-servicechannel-from-the-gallery"></a>Att lägga till ServiceChannel från galleriet
Du måste lägga till ServiceChannel från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av ServiceChannel i Azure AD.

**Utför följande steg för att lägga till ServiceChannel från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **ServiceChannel**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_000.png)

5. Välj i resultatpanelen **ServiceChannel**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_2.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med ServiceChannel baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i ServiceChannel motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i ServiceChannel upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i ServiceChannel.

Om du vill konfigurera och testa Azure AD enkel inloggning med ServiceChannel, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare ServiceChannel](#creating-a-servicechannel-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt ServiceChannel-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ServiceChannel:**

1. I Azure-hanteringsportalen på den **ServiceChannel** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserade inloggning** att aktivera enkel inloggning på.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_01.png)

3. På den **ServiceChannel domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_urls.png)

    a. I den **identifierare** textruta Skriv värdet som: `http://adfs.<domain>.com/adfs/service/trust`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<customer domain>.servicechannel.com/saml/acs`

    > [!NOTE] 
    > Observera att detta inte är verkliga värden. Du måste uppdatera dessa värden med de faktiska identifierare och Reply-URL. Här rekommenderar vi att du om du vill använda det unika värdet på strängen i identifieraren. Kontakta [ServiceChannel supportteamet](https://servicechannel.zendesk.com/hc/en-us) att hämta dessa värden.

4. Tillämpningsprogrammet ServiceChannel förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. Följande skärmbild visar ett exempel för det här. **NameIdentifier (användar-ID)** är bara obligatoriskt anspråk och standardvärdet är **user.userprincipalname** men ServiceChannel förväntar detta mappas med **user.mail**. Om du planerar att aktivera bara In Time användaretablering, bör du till följande anspråk som visas nedan. **Rollen** anspråk måste mappas till **user.assignedroles** som innehåller rollen för användaren.  

    Du kan se ServiceChannel guide [här](https://servicechannel.zendesk.com/hc/en-us/articles/217514326-Azure-AD-Configuration-Example) för mer information om anspråk.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicechannel-tutorial/tutorial_servicechannel_attribute.png)

    > [!NOTE] 
    > Klicka på [här](http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/) veta hur du konfigurerar **rollen** i Azure AD

5. I **användarattribut** klickar du på **visa och redigera andra användarattribut** och ange attribut.

    | Attributnamn | Attributvärde |
    | --- | --- |    
    | Roll| User.assignedroles |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicechannel-tutorial/tutorial_servicechannel_04.png)

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicechannel-tutorial/tutorial_servicechannel_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **Ok**
    
6. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_05.png) 

7. Klicka på **Spara**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicechannel-tutorial/tutorial_general_400.png)

8. På den **ServiceChannel Configuration** klickar du på **konfigurera ServiceChannel** att öppna **konfigurera inloggning** fönster. Observera den **SAML Enitity ID** från den **Snabbreferens** avsnitt.

9. Konfigurera enkel inloggning på **ServiceChannel** sida, måste du skicka den hämtade **certifikat (Base64)** och **SAML enhets-ID** till [ServiceChannel supportteamet](https://servicechannel.zendesk.com/hc/en-us). De ska ange detta för att få SAML SSO anslutningen korrekt på båda sidor.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och på **alla användare** att visa en lista över användare.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**. 

### <a name="creating-a-servicechannel-test-user"></a>Skapa en testanvändare ServiceChannel

Programmet stöder bara i tid användaretablering och authentication-användare kommer automatiskt att skapas i programmet. För fullständig användaretablering, kontakta [ServiceChannel-teamet](https://servicechannel.zendesk.com/hc/en-us)

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till ServiceChannel.

![Tilldela användare][200] 

**Om du vill tilldela ServiceChannel Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **ServiceChannel**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_app01.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen ServiceChannel på åtkomstpanelen du bör få automatiskt loggat in på ditt ServiceChannel-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_203.png