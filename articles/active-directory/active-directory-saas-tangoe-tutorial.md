---
title: 'Självstudier: Azure Active Directory-integrering med Tangoe kommandot Premium Mobile | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Tangoe kommandot Premium Mobile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: f6a2ffe56e4456ab3bf5a8234837425c60db1353
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352539"
---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>Självstudier: Azure Active Directory-integrering med Tangoe kommandot Premium Mobile

I kursen får lära du att integrera Tangoe kommandot Premium Mobile med Azure Active Directory (AD Azure).

Integrera Tangoe kommandot Premium Mobile med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Tangoe kommandot Premium Mobile
- Du kan aktivera användarna att automatiskt hämta loggat in på Tangoe kommandot Premium Mobile (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Tangoe kommandot Premium Mobile behöver du följande:

- En Azure AD-prenumeration
- En Tangoe kommandot Premium Mobile enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägg till Tangoe kommandot Premium Mobile från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-tangoe-command-premium-mobile-from-the-gallery"></a>Lägg till Tangoe kommandot Premium Mobile från galleriet
Du måste lägga till Tangoe kommandot Premium Mobile från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Tangoe kommandot Premium Mobile i Azure AD.

**Utför följande steg för att lägga till Tangoe kommandot Premium Mobile från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Tangoe kommandot Premium Mobile**väljer **Tangoe kommandot Premium Mobile** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Lägg till Tangoe kommandot Premium Mobile från galleriet ](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med Tangoe kommandot Premium Mobile baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Tangoe kommandot Premium Mobile till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Tangoe kommandot Premium Mobile upprättas.

I Tangoe kommandot Premium Mobile tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Tangoe kommandot Premium Mobile, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Tangoe kommandot Premium Mobile](#create-a-tangoe-command-premium-mobile-test-user)**  – du har en motsvarighet för Britta Simon i Tangoe kommandot Premium Mobile som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Tangoe kommandot Premium mobila program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Tangoe kommandot Premium Mobile:**

1. I Azure-portalen på den **Tangoe kommandot Premium Mobile** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![SAML-baserad inloggning](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_samlbase.png)

3. På den **Tangoe kommandot Premium Mobile domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Tangoe kommandot Premium mobila domän](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=<tenant issuer>&TARGET=<target page url>`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://sso.tangoe.com/sp/ACS.saml2`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska Reply URL och inloggnings-URL. Kontakta [Tangoe kommandot Premium Mobile Client supportteamet](https://www.tangoe.com/contact-2/) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Signeringscertifikat för SAML-avsnitt](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_certificate.png) 

5. Klicka på **spara** knappen.

    ![Knappen Spara](./media/active-directory-saas-tangoe-tutorial/tutorial_general_400.png)
    
6. På den **Tangoe Premium Mobile konfiguration** klickar du på **konfigurera Tangoe kommandot Premium Mobile** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurationsavsnittet Tangoe kommandot Premium Mobile](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_configure.png) 

7. För att få SSO konfigurerats för ditt program, kontakta din [Tangoe kommandot Premium Mobile Client supportteamet](https://www.tangoe.com/contact-2/) och ange följande:

   - Den hämtade metadatafilen
   - Den **SAML enhets-ID**
   - Den **URL för SAML-tjänst för enkel inloggning**
   - Den **URL för utloggning**

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Användare och grupper -> alla användare](./media/active-directory-saas-tangoe-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Lägga till användare](./media/active-directory-saas-tangoe-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan Användarsida](./media/active-directory-saas-tangoe-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-tangoe-command-premium-mobile-test-user"></a>Skapa en Tangoe kommandot Premium mobila användare

I det här avsnittet kan du skapa en användare som kallas Britta Simon i Tangoe kommandot Premium Mobile. 

Tangoe kommandot Premium Mobile-programmet måste alla användare som ska etableras i programmet innan du utför enkel inloggning. Så kan du arbeta med den [Tangoe kommandot Premium Mobile Client supportteamet](https://www.tangoe.com/contact-2/) att etablera dessa användare till programmet. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Tangoe kommandot Premium Mobile.

![Tilldela användare][200] 

**Om du vill tilldela Tangoe kommandot Premium Mobile Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Tangoe kommandot Premium Mobile**.

    ![Tangoe kommandot Premium Mobile i listan över appar](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på panelen Tangoe kommandot Premium Mobile på åtkomstpanelen du ska hämta automatiskt loggat in på ditt Tangoe kommandot Premium mobila program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_203.png

