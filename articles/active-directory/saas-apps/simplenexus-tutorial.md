---
title: 'Självstudier: Azure Active Directory-integrering med SimpleNexus | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SimpleNexus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 89821a05-88e2-4579-b144-0123b2b9cb95
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 759052a073103304f89096a43f0cbef88e0a1624
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308266"
---
# <a name="tutorial-azure-active-directory-integration-with-simplenexus"></a>Självstudier: Azure Active Directory-integrering med SimpleNexus

I kursen får lära du att integrera SimpleNexus med Azure Active Directory (AD Azure).

Integrera SimpleNexus med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SimpleNexus
- Du kan aktivera användarna att automatiskt hämta loggat in på SimpleNexus (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med SimpleNexus, behöver du följande:

- En Azure AD-prenumeration
- En SimpleNexus enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SimpleNexus från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-simplenexus-from-the-gallery"></a>Att lägga till SimpleNexus från galleriet
Du måste lägga till SimpleNexus från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SimpleNexus i Azure AD.

**Utför följande steg för att lägga till SimpleNexus från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **SimpleNexus**.

    ![Skapa en testanvändare i Azure AD](./media/simplenexus-tutorial/tutorial_simplenexus_search.png)

5. Välj i resultatpanelen **SimpleNexus**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/simplenexus-tutorial/tutorial_simplenexus_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med SimpleNexus baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i SimpleNexus motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SimpleNexus upprättas.

I SimpleNexus, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med SimpleNexus, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare SimpleNexus](#creating-a-simplenexus-test-user)**  – du har en motsvarighet för Britta Simon i SimpleNexus som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt SimpleNexus program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SimpleNexus:**

1. I Azure-portalen på den **SimpleNexus** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/simplenexus-tutorial/tutorial_simplenexus_samlbase.png)

3. På den **SimpleNexus domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/simplenexus-tutorial/tutorial_simplenexus_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://simplenexus.com/<companyname>_login`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://simplenexus.com/<companyname>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [SimpleNexus klienten supportteamet](https://simplenexus.com/sn/contact-us/) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/simplenexus-tutorial/tutorial_simplenexus_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/simplenexus-tutorial/tutorial_general_400.png)

6. Konfigurera enkel inloggning på **SimpleNexus** sida, måste du skicka den hämtade **XML-Metadata för** till [SimpleNexus supportteam](https://simplenexus.com/sn/contact-us/). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/simplenexus-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/simplenexus-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/simplenexus-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/simplenexus-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-simplenexus-test-user"></a>Skapa en testanvändare SimpleNexus

För att aktivera Azure AD-användare kan logga in på SimpleNexus etableras de i SimpleNexus.

När det gäller SimpleNexus är etablering en manuell aktivitet som utförs av klientorganisationsadministratören.

>[!NOTE]
>Du kan använda något annat SimpleNexus användarens konto skapas verktyg eller API: er som tillhandahålls av SimpleNexus att etablera AAD-användarkonton. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SimpleNexus.

![Tilldela användare][200] 

**Om du vill tilldela SimpleNexus Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SimpleNexus**.

    ![Konfigurera enkel inloggning](./media/simplenexus-tutorial/tutorial_simplenexus_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen SimpleNexus på åtkomstpanelen du bör få automatiskt loggat in på ditt SimpleNexus program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/simplenexus-tutorial/tutorial_general_01.png
[2]: ./media/simplenexus-tutorial/tutorial_general_02.png
[3]: ./media/simplenexus-tutorial/tutorial_general_03.png
[4]: ./media/simplenexus-tutorial/tutorial_general_04.png

[100]: ./media/simplenexus-tutorial/tutorial_general_100.png

[200]: ./media/simplenexus-tutorial/tutorial_general_200.png
[201]: ./media/simplenexus-tutorial/tutorial_general_201.png
[202]: ./media/simplenexus-tutorial/tutorial_general_202.png
[203]: ./media/simplenexus-tutorial/tutorial_general_203.png

