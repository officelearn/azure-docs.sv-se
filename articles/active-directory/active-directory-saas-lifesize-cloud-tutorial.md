---
title: 'Självstudier: Azure Active Directory-integrering med molntjänster Lifesize | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Lifesize moln.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 84af3a9b726d990e585e2b12b1c0a6f4609fcb7e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344426"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Självstudier: Azure Active Directory-integrering med Lifesize moln

I kursen får lära du att integrera Lifesize moln med Azure Active Directory (AD Azure).

Integrera Lifesize moln med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Lifesize moln
- Du kan aktivera användarna att automatiskt hämta loggat in på molnet Lifesize (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Lifesize molnet, behöver du följande:

- En Azure AD-prenumeration
- En Lifesize enkel inloggning aktiverad molnprenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Lifesize moln från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Att lägga till Lifesize moln från galleriet
Du måste lägga till Lifesize moln från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Lifesize moln i Azure AD.

**Utför följande steg för att lägga till Lifesize moln från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Lifesize moln**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_search.png)

5. Välj i resultatpanelen **Lifesize moln**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
Du konfigurera och testa Azure AD enkel inloggning med Lifesize molnet baserat på en testanvändare som kallas ”Britta Simon” i det här avsnittet.

Azure AD måste du känna till motsvarande användaren i Lifesize molnet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Lifesize molnet upprättas.

I Lifesize moln, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Lifesize moln, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Lifesize moln](#creating-a-lifesize-cloud-test-user)**  – du har en motsvarighet för Britta Simon i Lifesize moln som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Lifesize moln.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Lifesize molnet:**

1. I Azure-portalen på den **Lifesize moln** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_samlbase.png)

3. På den **Lifesize moln domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://login.lifesizecloud.com/ls/?acs`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://login.lifesizecloud.com/<companyname>`

     
4. Kontrollera **visa avancerade inställningar för URL: en**, utföra följande steg:    
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_url1.png)

    I den **Relay tillstånd** textruta Skriv en URL med följande mönster: `https://webapp.lifesizecloud.com/?ent=<identifier>`
   
   > [!NOTE] 
   >Observera att detta inte är verkliga värden. Du måste uppdatera dessa värden med den faktiska inloggnings-URL, Relay tillstånd och identifierare. Kontakta [Lifesize Cloud klienten supportteamet](https://www.lifesize.com/support) få inloggnings-URL, och identifierar-värden och du kan hämta Relay tillstånd värde från SSO-konfigurationen som beskrivs senare under kursen.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_400.png)

6. På den **Lifesize Molnkonfigurationen** klickar du på **konfigurera Lifesize moln** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_configure.png) 

7. Att hämta SSO konfigurerats för ditt program, logga in på programmet Lifesize moln med administratörsrättigheter.

8. Klicka på ditt namn i övre högra hörnet och klicka sedan på den **avancerade inställningar**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

9. I avancerade inställningar nu klickar du på den **SSO Configuration** länk. Konfigurationssidan för enkel inloggning för din instans öppnas.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

10. Nu konfigurera följande värden i Användargränssnittet för SSO-konfigurationen.    
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
    
    a. I **identitet providern utfärdaren** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen.

    b.  I **Inloggningswebbadressen** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    c. Öppna din Base64-kodade certifikatet i anteckningar som hämtas från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** textruta.
  
    d. Ange värdet som i SAML attributmappning för textrutan förnamn **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
    
    e. I mappningen för SAML-attributet för den **efternamn** textrutan anger du värdet som **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
    
    f. I mappningen för SAML-attributet för den **e-post** textrutan anger du värdet som **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

11. Kontrollera konfigurationen kan du klicka på den **Test** knappen.
   
    >[!NOTE]
    >För lyckad testning måste du slutföra guiden för konfiguration av i Azure AD och även ge åtkomst till användare eller grupper som kan utföra testet.

12. Aktivera SSO genom att kontrollera om den **aktivera enkel inloggning** knappen.

13. Klicka på den **uppdatering** knappen så att alla inställningar har sparats. Detta genererar RelayState-värdet. Kopiera värdet RelayState genereras i textrutan, klistra in den i den **Relay tillstånd** textruta under **Lifesize moln domän och URL: er** avsnitt. 

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-lifesize-cloud-test-user"></a>Skapa en testanvändare Lifesize moln

I det här avsnittet kan du skapa en användare som kallas Britta Simon i Lifesize molnet. Lifesize molnet stöder automatisk användaretablering. Efter en lyckad autentisering vid Azure AD användaren automatiskt att etableras i programmet. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Lifesize moln.

![Tilldela användare][200] 

**Om du vill tilldela Lifesize moln Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Lifesize moln**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Lifesize moln på åtkomstpanelen bör du få inloggningssidan för Lifesize molnapp.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png

