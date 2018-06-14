---
title: 'Självstudier: Azure Active Directory-integrering med TigerText Secure Messenger | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TigerText Secure Messenger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: 6b21f3474466c8338046ebdad5a9cae18bdda4fe
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350679"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Självstudier: Azure Active Directory-integrering med TigerText Secure Messenger

I kursen får lära du att integrera TigerText Secure Messenger med Azure Active Directory (AD Azure).

Integrera TigerText Secure Messenger med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TigerText Secure Messenger
- Du kan aktivera användarna att automatiskt hämta inloggade TigerText Secure Messenger (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med TigerText Secure Messenger, behöver du följande:

- En Azure AD-prenumeration
- En TigerText Secure Messenger enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägg till TigerText Secure Messenger från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-tigertext-secure-messenger-from-the-gallery"></a>Lägg till TigerText Secure Messenger från galleriet
Du måste lägga till TigerText Secure Messenger från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av TigerText säkra Messenger i Azure AD.

**Utför följande steg för att lägga till TigerText Secure Messenger från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **TigerText Secure Messenger**väljer **TigerText Secure Messenger** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Lägg till från galleriet](./media/active-directory-saas-tigertext-tutorial/tutorial_tigertext_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med TigerText Secure Messenger baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i TigerText Secure Messenger till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i TigerText Secure Messenger upprättas.

I TigerText Secure Messenger, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med TigerText Secure Messenger, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare TigerText Secure Messenger](#create-a-tigertext-secure-messenger-test-user)**  – du har en motsvarighet för Britta Simon i TigerText Secure Messenger som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i TigerText Secure Messenger-programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med TigerText Secure Messenger:**

1. I Azure-portalen på den **TigerText Secure Messenger** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![SAML-baserad inloggning](./media/active-directory-saas-tigertext-tutorial/tutorial_tigertext_samlbase.png)

3. På den **TigerText Secure Messenger domän och URL: er** avsnittet, utför följande steg:

    ![Avsnittet TigerText Secure Messenger domän och URL: er](./media/active-directory-saas-tigertext-tutorial/tutorial_tigertext_url.png)

    a. I den **inloggnings-URL** textruta typen URL: en som: `https://home.tigertext.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://saml-lb.tigertext.me/v1/organization/<instance Id>`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med den faktiska identifieraren. Kontakta [TigerText Secure Messenger-klienten supportteamet](mailTo:prosupport@tigertext.com) att hämta det här värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Signeringscertifikat för SAML-avsnitt](./media/active-directory-saas-tigertext-tutorial/tutorial_tigertext_certificate.png) 

5. Klicka på **spara** knappen.

    ![Knappen Spara](./media/active-directory-saas-tigertext-tutorial/tutorial_general_400.png)

6. För att få enkel inloggning konfigurerats för ditt program, kontakta [TigerText Secure Messenger supportteamet](mailTo:prosupport@tigertext.com) och ger dem den **hämtade metadata**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tigertext-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Användare och grupper -> alla användare](./media/active-directory-saas-tigertext-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Knappen Lägg till](./media/active-directory-saas-tigertext-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Användardialogrutan](./media/active-directory-saas-tigertext-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-tigertext-secure-messenger-test-user"></a>Skapa en testanvändare TigerText Secure Messenger

I det här avsnittet skapar du en användare som kallas Britta Simon i TigerText. Kontakta [TigerText Secure Messenger-klienten supportteamet](mailTo:prosupport@tigertext.com) att lägga till användare i TigerText-plattformen.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TigerText Secure Messenger.

![Tilldela användare][200] 

**Om du vill tilldela TigerText Secure Messenger Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **TigerText Secure Messenger**.

    ![TigerText Secure Messenger i listan över appar](./media/active-directory-saas-tigertext-tutorial/tutorial_tigertext_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen TigerText på åtkomstpanelen du bör få automatiskt loggat in på ditt TigerText program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tigertext-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tigertext-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tigertext-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tigertext-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tigertext-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tigertext-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tigertext-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tigertext-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tigertext-tutorial/tutorial_general_203.png

