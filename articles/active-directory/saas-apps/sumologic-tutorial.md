---
title: 'Självstudier: Azure Active Directory-integrering med SumoLogic | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SumoLogic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 784509ea111b59521a3278813d45d76f191b8c1f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35965859"
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Självstudier: Azure Active Directory-integrering med SumoLogic

I kursen får lära du att integrera SumoLogic med Azure Active Directory (AD Azure).

Integrera SumoLogic med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SumoLogic
- Du kan aktivera användarna att automatiskt hämta loggat in på SumoLogic (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med SumoLogic, behöver du följande:

- En Azure AD-prenumeration
- En SumoLogic enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SumoLogic från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sumologic-from-the-gallery"></a>Att lägga till SumoLogic från galleriet
Du måste lägga till SumoLogic från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SumoLogic i Azure AD.

**Utför följande steg för att lägga till SumoLogic från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **SumoLogic**.

    ![Skapa en testanvändare i Azure AD](./media/sumologic-tutorial/tutorial_sumologic_search.png)

5. Välj i resultatpanelen **SumoLogic**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/sumologic-tutorial/tutorial_sumologic_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med SumoLogic baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i SumoLogic motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SumoLogic upprättas.

I SumoLogic, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med SumoLogic, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare SumoLogic](#creating-a-sumologic-test-user)**  – du har en motsvarighet för Britta Simon i SumoLogic som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt SumoLogic program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SumoLogic:**

1. I Azure-portalen på den **SumoLogic** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/sumologic-tutorial/tutorial_sumologic_samlbase.png)

3. På den **SumoLogic domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/sumologic-tutorial/tutorial_sumologic_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<tenantname>.SumoLogic.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [SumoLogic klienten supportteamet](https://www.sumologic.com/contact-us/) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/sumologic-tutorial/tutorial_sumologic_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/sumologic-tutorial/tutorial_general_400.png)

6. På den **SumoLogic Configuration** klickar du på **konfigurera SumoLogic** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/sumologic-tutorial/tutorial_sumologic_configure.png) 

7. I en annan webbläsarfönster loggar du in på webbplatsen SumoLogic företag som administratör.

8. Gå till **hantera \> säkerhet**.
   
    ![Hantera](./media/sumologic-tutorial/ic778556.png "hantera")

9. Klicka på **SAML**.
   
    ![Globala säkerhetsinställningar](./media/sumologic-tutorial/ic778557.png "globala säkerhetsinställningar")

10. Från den **väljer en konfiguration eller skapa en ny** väljer **Azure AD**, och klicka sedan på **konfigurera**.
   
    ![Konfigurera SAML 2.0](./media/sumologic-tutorial/ic778558.png "konfigurera SAML 2.0")

11. På den **konfigurera SAML 2.0** dialogrutan, utför följande steg:
   
    ![Konfigurera SAML 2.0](./media/sumologic-tutorial/ic778559.png "konfigurera SAML 2.0")
   
    a. I den **Konfigurationsnamnet** textruta typen **Azure AD**. 

    b. Välj **felsökningsläge**.

    c. I den **utfärdaren** textruta klistra in värdet för **SAML enhets-ID**, som du har kopierat från Azure-portalen. 

    d. I den **Authn begära URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.

    e. Öppna din Base64-kodade certifikatet i anteckningar, kopiera innehållet i den till Urklipp och klistra in hela certifikatet till **X.509-certifikat** textruta.

    f. Som **e-attributet**väljer **Använd SAML ämne**.  

    g. Välj **SP initierade inloggningen Configuration**.

    h. I den **inloggningen sökvägen** textruta typen **Azure** och på **spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/sumologic-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/sumologic-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/sumologic-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/sumologic-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-sumologic-test-user"></a>Skapa en testanvändare SumoLogic

För att aktivera Azure AD-användare kan logga in på SumoLogic etableras de för SumoLogic.  

* När det gäller SumoLogic är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **SumoLogic** klient.

2. Gå till **hantera \> användare**.
   
    ![Användare](./media/sumologic-tutorial/ic778561.png "användare")

3. Klicka på **Lägg till**.
   
    ![Användare](./media/sumologic-tutorial/ic778562.png "användare")

4. På den **ny användare** dialogrutan, utför följande steg:
   
    ![Ny användare](./media/sumologic-tutorial/ic778563.png "ny användare") 
 
    a. Skriv relaterad information av Azure AD-kontot som du vill etablera i den **Förnamn**, **efternamn**, och **e-post** textrutor.
  
    b. Välj en roll.
  
    c. Som **Status**väljer **Active**.
  
    d. Klicka på **Spara**.

>[!NOTE]
>Du kan använda något annat SumoLogic användarens konto skapas verktyg eller API: er som tillhandahålls av SumoLogic att etablera AAD-användarkonton. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SumoLogic.

![Tilldela användare][200] 

**Om du vill tilldela SumoLogic Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SumoLogic**.

    ![Konfigurera enkel inloggning](./media/sumologic-tutorial/tutorial_sumologic_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen SumoLogic på åtkomstpanelen du bör få automatiskt loggat in på ditt SumoLogic program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sumologic-tutorial/tutorial_general_01.png
[2]: ./media/sumologic-tutorial/tutorial_general_02.png
[3]: ./media/sumologic-tutorial/tutorial_general_03.png
[4]: ./media/sumologic-tutorial/tutorial_general_04.png

[100]: ./media/sumologic-tutorial/tutorial_general_100.png

[200]: ./media/sumologic-tutorial/tutorial_general_200.png
[201]: ./media/sumologic-tutorial/tutorial_general_201.png
[202]: ./media/sumologic-tutorial/tutorial_general_202.png
[203]: ./media/sumologic-tutorial/tutorial_general_203.png

