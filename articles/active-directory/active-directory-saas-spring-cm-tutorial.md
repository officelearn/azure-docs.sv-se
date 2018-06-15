---
title: 'Självstudier: Azure Active Directory-integrering med SpringCM | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SpringCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: jeedes
ms.openlocfilehash: 70d3ef14875ef7af730b41e02b5f04e3930797ac
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34351587"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Självstudier: Azure Active Directory-integrering med SpringCM

I kursen får lära du att integrera SpringCM med Azure Active Directory (AD Azure).

Integrera SpringCM med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SpringCM
- Du kan aktivera användarna att automatiskt hämta loggat in på SpringCM (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med SpringCM, behöver du följande:

- En Azure AD-prenumeration
- En SpringCM enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SpringCM från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-springcm-from-the-gallery"></a>Att lägga till SpringCM från galleriet
Du måste lägga till SpringCM från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SpringCM i Azure AD.

**Utför följande steg för att lägga till SpringCM från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **SpringCM**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_search.png)

5. Välj i resultatpanelen **SpringCM**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med SpringCM baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i SpringCM motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SpringCM upprättas.

I SpringCM, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med SpringCM, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare SpringCM](#creating-a-springcm-test-user)**  – du har en motsvarighet för Britta Simon i SpringCM som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt SpringCM program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SpringCM:**

1. I Azure-portalen på den **SpringCM** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_samlbase.png)

3. På den **SpringCM domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_url.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska inloggnings-URL. Kontakta [SpringCM klienten supportteamet](https://knowledge.springcm.com/support) att hämta det här värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Raw)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-spring-cm-tutorial/tutorial_general_400.png)

6. På den **SpringCM Configuration** klickar du på **konfigurera SpringCM** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_configure.png)   

7. I en annan webbläsarfönstret, logga in på ditt **SpringCM** företagets webbplats som administratör.

8. Klicka på menyn högst upp **gå till**, klickar du på **inställningar**, och sedan, i den **kontoinställningar** klickar du på **SAML SSO**.
   
    ![SAML SSO](./media/active-directory-saas-spring-cm-tutorial/ic797051.png "SAML SSO")

9. Utför följande steg i konfigurationsavsnittet identitet providern:
   
    ![Identitet providerkonfigurationen](./media/active-directory-saas-spring-cm-tutorial/ic797052.png "identitet providerkonfigurationen")
    
    a. Om du vill överföra din hämtat Azure Active Directory-certifikat, klickar du på **Välj utfärdarcertifikatet** eller **ändra utfärdarcertifikatet**.
    
    b. Klistra in **SAML enhets-ID** -värde som du har kopierat från Azure-portalen i den **utfärdaren** textruta.
    
    c. Klistra in **SAML enkel inloggning Tjänstwebbadress** -värde som du har kopierat från Azure-portalen i den **Service Provider (SP) initierade Endpoint** textruta.
            
    d. Välj **SAML aktiverat** som **aktivera**.

    e. Klicka på **Spara**.
 
> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-springcm-test-user"></a>Skapa en testanvändare SpringCM

Om du vill aktivera Azure Active Directory-användare kan logga in på SpringCM etableras de i SpringCM. När det gäller SpringCM är etablering en manuell aktivitet.

>[!NOTE]
>Mer information finns i [skapa och redigera användare SpringCM](http://knowledge.springcm.com/create-and-edit-a-springcm-user). 

**Utför följande steg för att etablera ett användarkonto för SpringCM:**

1. Logga in på ditt **SpringCM** företagets webbplats som administratör.

2. Klicka på **GOTO**, och klicka sedan på **ADRESSBOKEN**.
   
    ![Skapa användare](./media/active-directory-saas-spring-cm-tutorial/ic797054.png "skapa användare")

3. Klicka på **skapa användare**.

4. Välj en **användarrollen**.

5. Välj **skickar Aktiveringsmeddelandet**.

6. Ange förnamn, efternamn och e-postadressen till ett giltigt Azure Active Directory-användarkonto som du vill etablera i relaterade textrutor.

7. Lägg till användaren till en **säkerhetsgrupp**.

8. Klicka på **Spara**.

  >[!NOTE]
  >Du kan använda något annat SpringCM användarens konto skapas verktyg eller API: er som tillhandahålls av SpringCM att etablera AAD-användarkonton.  
  > 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SpringCM.

![Tilldela användare][200] 

**Om du vill tilldela SpringCM Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SpringCM**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.
 
När du klickar på panelen SpringCM på åtkomstpanelen du bör få automatiskt loggat in på ditt SpringCM program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_203.png

