---
title: 'Självstudier: Azure Active Directory-integrering med FilesAnywhere | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FilesAnywhere.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: jeedes
ms.openlocfilehash: 8d80e537bc031a777ae037f5147b2c84b7e76281
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215978"
---
# <a name="tutorial-azure-active-directory-integration-with-filesanywhere"></a>Självstudier: Azure Active Directory-integrering med FilesAnywhere

I kursen får lära du att integrera FilesAnywhere med Azure Active Directory (AD Azure).

Integrera FilesAnywhere med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till FilesAnywhere
- Du kan aktivera användarna att automatiskt hämta loggat in på FilesAnywhere (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - till Azure-hanteringsportalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med FilesAnywhere, behöver du följande:

- En Azure AD-prenumeration
- En FilesAnywhere enkel inloggning på aktiverade prenumeration


> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.


Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion av en månad [här](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till FilesAnywhere från galleriet
2. Konfigurera och testa Azure AD enkel inloggning


## <a name="adding-filesanywhere-from-the-gallery"></a>Att lägga till FilesAnywhere från galleriet
Du måste lägga till FilesAnywhere från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av FilesAnywhere i Azure AD.

**Utför följande steg för att lägga till FilesAnywhere från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **FilesAnywhere**.

    ![Skapa en testanvändare i Azure AD](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_search.png)

5. Välj i resultatpanelen **FilesAnywhere**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med FilesAnywhere baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i FilesAnywhere motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i FilesAnywhere upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i FilesAnywhere.

Om du vill konfigurera och testa Azure AD enkel inloggning med FilesAnywhere, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare FilesAnywhere](#creating-a-filesanywhere-test-user)**  – du har en motsvarighet för Britta Simon i FilesAnywhere som är kopplad till Azure AD-representation av henne.
3. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
4. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt FilesAnywhere program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med FilesAnywhere:**

1. I Azure-hanteringsportalen på den **FilesAnywhere** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserade inloggning** att aktivera enkel inloggning på.
 
    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_samlbase.png)

3. På den **FilesAnywhere domän och URL: er** om du vill konfigurera programmet i **IDP initierade läge**:

    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_filesanywhere_url.png)
    
    a. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<company name>.filesanywhere.com/saml20.aspx?c=215`
> [!NOTE]
> Observera att värdet **215** är en **clientid** och är bara ett exempel. Du måste ersätta den med det faktiska clientid-värdet.

4. På den **FilesAnywhere domän och URL: er** om du vill konfigurera programmet i **SP initierade läge**, utför följande steg:
    
    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_filesanywhere_url1.png)

    a. Klicka på den **visa avancerade inställningar för URL: en** alternativet

    b. I den **logga URL** textruta Skriv en URL med följande mönster: `https://<sub domain>.filesanywhere.com/`

    > [!NOTE] 
    > Observera att detta inte är verkliga värden. Du måste uppdatera dessa värden med de faktiska logga URL och Reply-URL. Kontakta [FilesAnywhere supportteamet](mailto:support@FilesAnywhere.com) att hämta dessa värden. 

5. FilesAnywhere program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.
    
    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_filesanywhere_attribute.png)
    
    När användarna loggar med FilesAnywhere de hämta värdet för **clientid** -attributet från [FilesAnywhere team](mailto:support@FilesAnywhere.com). Du måste lägga till ”klient-Id”-attribut med det unika värdet som tillhandahålls av FilesAnywhere. Dessa attribut som visas ovan krävs.
    > [!NOTE] 
    > Observera att värdet **2331** av **clientid** är bara ett exempel. Du måste ange det faktiska värdet.


6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ---------------| --------------- |    
    | clientid | *”uniquevalue”* |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_04.png)

    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **Ok**

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_general_400.png)

8. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_certificate.png) 

9. På den **FilesAnywhere Configuration** klickar du på **konfigurera FilesAnywhere** att öppna **konfigurera inloggning** fönster.

    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configure.png) 

    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configuresignon.png)

10. För att få SSO konfigurationen klar för tillämpningsprogrammet FilesAnywhere slutet, kontakta [FilesAnywhere supportteamet](mailto:support@FilesAnywhere.com) och ge dem hämtade SAML tokensignering certifikat och enkel inloggning (SSO)-URL.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/filesanywhere-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och på **alla användare** att visa en lista över användare.
    
    ![Skapa en testanvändare i Azure AD](./media/filesanywhere-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/filesanywhere-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/filesanywhere-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**. 



### <a name="creating-a-filesanywhere-test-user"></a>Skapa en testanvändare FilesAnywhere

Programmet stöder bara i tid användaretablering och authentication-användare kommer automatiskt att skapas i programmet. 


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till FilesAnywhere.

![Tilldela användare][200] 

**Om du vill tilldela FilesAnywhere Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **FilesAnywhere**.

    ![Konfigurera enkel inloggning](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    


### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen FilesAnywhere på åtkomstpanelen du bör få automatiskt loggat in på ditt FilesAnywhere program.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/FilesAnywhere-tutorial/tutorial_general_01.png
[2]: ./media/FilesAnywhere-tutorial/tutorial_general_02.png
[3]: ./media/FilesAnywhere-tutorial/tutorial_general_03.png
[4]: ./media/FilesAnywhere-tutorial/tutorial_general_04.png

[100]: ./media/FilesAnywhere-tutorial/tutorial_general_100.png

[200]: ./media/FilesAnywhere-tutorial/tutorial_general_200.png
[201]: ./media/FilesAnywhere-tutorial/tutorial_general_201.png
[202]: ./media/FilesAnywhere-tutorial/tutorial_general_202.png
[203]: ./media/FilesAnywhere-tutorial/tutorial_general_203.png
