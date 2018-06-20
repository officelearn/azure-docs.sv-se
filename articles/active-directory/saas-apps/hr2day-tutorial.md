---
title: 'Självstudier: Azure Active Directory-integrering med HR2day av Merces | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HR2day av Merces.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.openlocfilehash: a63ef3b089ec80da6a75aaaf8a4bb2454fec6cf7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224189"
---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Självstudier: Azure Active Directory-integrering med HR2day av Merces

I kursen får lära du att integrera HR2day av Merces med Azure Active Directory (AD Azure).

Integrera HR2day av Merces med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till HR2day av Merces.
- Du kan aktivera användarna att få loggas automatiskt HR2day av Merces med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats--Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med HR2day av Merces, behöver du följande:

- En Azure AD-prenumeration.
- En HR2day av Merces enkel inloggning aktiverad prenumeration.

> [!NOTE]
> Vi rekommenderar inte använda en produktionsmiljö för att testa stegen i den här självstudiekursen.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte din produktionsmiljö om det är nödvändigt.
- Hämta en [en månaders kostnadsfri utvärderingsversion av Azure AD](https://azure.microsoft.com/pricing/free-trial/) om det inte redan har.  

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs här består av två huvudsakliga byggblock:

1. Lägger till HR2day av Merces från galleriet.
2. Konfigurera och testa Azure AD enkel inloggning.

## <a name="add-hr2day-by-merces-from-the-gallery"></a>Lägg till HR2day av Merces från galleriet
För att konfigurera integrering av HR2day av Merces i Azure AD, lägga till HR2day av Merces från galleriet i listan över hanterade SaaS-appar.

**Om du vill lägga till HR2day av Merces från galleriet, gör du följande:**

1. I den [Azure-portalen](https://portal.azure.com), på det vänstra navigeringsfönstret väljer du den **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till ett nytt program, Välj den **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **HR2day av Merces**.

    ![Skapa en testanvändare i Azure AD](./media/hr2day-tutorial/tutorial_hr2daybymerces_search.png)

5. Välj i resultatpanelen **HR2day av Merces**, och välj sedan den **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med HR2day av Merces baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD att veta vilka motsvarande användaren i HR2day av Merces är att en användare i Azure AD. Med andra ord måste du skapa en länk mellan en Azure AD-användare och relaterade användaren i HR2day av Merces.

Tilldela i HR2day av Merces den **användarnamn** i Azure AD **användarnamn** att upprätta en relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med HR2day av Merces, måste du utföra följande byggblock:

1. [Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on): att användarna kan använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user): testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en HR2day av Merces testanvändare](#creating-an-hr2day-by-merces-test-user): skapa en motsvarighet för Britta Simon i HR2day av Merces som är kopplad till Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user): aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#testing-single-sign-on): Kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din HR2day av Merces program.

**För att konfigurera Azure AD enkel inloggning med HR2day av Merces, gör du följande:**

1. I Azure-portalen på den **HR2day av Merces** programmet integration anger **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. Aktivera enkel inloggning, i den **enkel inloggning** dialogrutan **läge** som **SAML-baserade inloggning**.
 
    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

3. I den **HR2day Merces domänen och URL: er** avsnittet, gör du följande:

    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. I den **inloggnings-URL** Skriv en URL med hjälp av följande mönster: `https://<tenantname>.force.com/<instancename>`.

    b. I den **identifierare** Skriv en URL med hjälp av följande mönster: `https://hr2day.force.com/<companyname>`.

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta den [HR2day av Merces klienten supportteamet](mailto:servicedesk@merces.nl) att hämta dessa värden. 
 


4. På den **SAML-signeringscertifikat** väljer **Certificate(Base64)**, och sedan spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

5. Det här avsnittet beskrivs hur användarna att autentisera till HR2day av Merces med sitt konto i Azure AD. De kan göra detta med hjälp av federation som baseras på SAML-protokoll.

    Din HR2day av Merces program förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning SAML-token. Följande skärmbild visar ett exempel på detta. 

    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_hr2day_00.png)
    
    > [!NOTE] 
    Innan du kan konfigurera SAML-kontroll måste du kontakta den [HR2day av Merces klienten supportteamet](mailto:servicedesk@merces.nl) och begära värdet för attributet för unik identifierare för din klient. Du behöver det här värdet för att slutföra stegen i nästa avsnitt. 

6. I den **enkel inloggning** i dialogrutan den **användarattribut** avsnittet, konfigurera attributet SAML-token som visas i följande bild. Sedan gör du följande.
    
      | Attributets namn    |   Attributvärdet |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | `join([mail],"102938475Z","@"` |
    
      a. Öppna den **lägga till attributet** markerar **Lägg till attributet**.

    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_attribute_05.png)

    b. I den **namn** skriver **ATTR_LOGINCLAIM**.

    c. Från den **värdet** väljer **Join()**.

    d. Från den **sträng1** väljer **user.mail**.

    e. För **sträng2**, ange den unika identifieraren som tillhandahålls av din HR2day-teamet.

    f. I den **avgränsare** skriver **\@**.
    
    g. Välj **Ok**.

7. Välj knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_general_400.png)

8. I den **HR2day Merces konfigurationen** väljer **konfigurera HR2day av Merces** att öppna den **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL**, **SAML enhets-ID**, och **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens** avsnitt.

    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

9. Om du vill konfigurera enkel inloggning för ditt program ska kontakta den [HR2day av Merces klienten supportteamet](mailTo:servicedesk@merces.nl). Bifoga den hämtade **Certificate(Base64)** filen till din e-post. Ger också den **Sign-Out URL**, **SAML enhets-ID**, och **SAML inloggning tjänst-URL för enkel** så att de kan konfigureras för SSO-integrering.

    > [!NOTE]
    >Anges till Merces-teamet att den här integreringen behöver enhets-ID anges med mönstret **https://hr2day.force.com/INSTANCENAME**.

    > [!TIP]
    >Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken. Komma åt inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen i den [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Om du vill skapa en testanvändare i Azure AD, gör du följande:**

1. I den **Azure-portalen**, på det vänstra navigeringsfönstret väljer du den **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/hr2day-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och välj sedan **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/hr2day-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/hr2day-tutorial/create_aaduser_03.png) 

4. I den **användaren** dialogrutan rutan, gör du följande:
 
    ![Skapa en testanvändare i Azure AD](./media/hr2day-tutorial/create_aaduser_04.png) 

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet**, och sedan skriva ned lösenordet.

    d. Välj **Skapa**.
 
### <a name="create-an-hr2day-by-merces-test-user"></a>Skapa en HR2day av Merces testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i HR2day av Merces. Om du vill lägga till användare i kontot HR2day arbeta med den [HR2day av Merces klienten supportteamet](mailto:servicedesk@merces.nl). 

> [!NOTE]
> Om du behöver skapa en användare manuellt kan kontakta den [HR2day av Merces klienten supportteamet](mailto:servicedesk@merces.nl).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till HR2day av Merces.

![Tilldela användare][200] 

**Om du vill tilldela HR2day av Merces Britta Simon gör du följande:**

1. Öppna vyn program i Azure-portalen, gå till vyn katalog och gå sedan till **företagsprogram**. Välj därefter **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **HR2day av Merces**.

    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

3. Välj på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Välj den **Lägg till** knappen. I den **Lägg uppdrag** dialogrutan **användare och grupper**.

    ![Tilldela användare][203]

5. I den **användare och grupper** i dialogrutan den **användare** väljer **Britta Simon**.

6. Klicka på den **Välj** knappen.

7. I den **Lägg uppdrag** dialogrutan **tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.  

När du markerar HR2day genom Merces panelen på åtkomstpanelen loggas du automatiskt komma till din HR2day av Merces program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hr2day-tutorial/tutorial_general_01.png
[2]: ./media/hr2day-tutorial/tutorial_general_02.png
[3]: ./media/hr2day-tutorial/tutorial_general_03.png
[4]: ./media/hr2day-tutorial/tutorial_general_04.png

[100]: ./media/hr2day-tutorial/tutorial_general_100.png

[200]: ./media/hr2day-tutorial/tutorial_general_200.png
[201]: ./media/hr2day-tutorial/tutorial_general_201.png
[202]: ./media/hr2day-tutorial/tutorial_general_202.png
[203]: ./media/hr2day-tutorial/tutorial_general_203.png

