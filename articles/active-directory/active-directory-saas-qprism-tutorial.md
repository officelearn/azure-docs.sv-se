---
title: "Självstudier: Azure Active Directory-integrering med QPrism | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och QPrism."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: jeedes
ms.openlocfilehash: a380e9c07710e0c79a44e036f9c5bd72a731fe40
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>Självstudier: Azure Active Directory-integrering med QPrism

I kursen får lära du att integrera QPrism med Azure Active Directory (AD Azure).

Integrera QPrism med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till QPrism.
- Du kan aktivera användarna att automatiskt hämta loggat in på QPrism (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats: Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med QPrism, behöver du följande:

- En Azure AD-prenumeration
- En QPrism enkel inloggning på aktiverade prenumeration

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till QPrism från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-qprism-from-the-gallery"></a>Lägg till QPrism från galleriet
Du måste lägga till QPrism från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av QPrism i Azure AD.

**Lägg till QPrism från galleriet:**

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj **Azure Active Directory**. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till ett nytt program överst i dialogrutan Välj **nytt program**.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **QPrism**, och välj **QPrism** resultatet-panelen. Klicka på **Lägg till** lägga till programmet.

    ![QPrism i resultatlistan](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med QPrism, baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD att veta vilka motsvarande användaren i QPrism är att en användare i Azure AD. Med andra ord måste det finnas ett länkat förhållande mellan en Azure AD-användare och relaterade användaren i QPrism.

För att upprätta den här relationen i QPrism, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn**.

Slutför följande byggblock för att konfigurera och testa Azure AD enkel inloggning med QPrism:

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on) att användarna ska kunna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare QPrism](#create-a-qprism-test-user) har en motsvarighet för Britta Simon QPrism som är kopplad till Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#test-single-sign-on) att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet QPrism.

1. I Azure-portalen på den **QPrism** programmet integration anger **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_samlbase.png)

3. I den **QPrism domän och URL: er** avsnittet, gör du följande:

    ![URL: er och QPrism domän med enkel inloggning information](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_url.png)

    a. I den **inloggnings-URL** text skriver en URL som använder följande mönster:`https://<customer domain>.qmyzone.com/login`

    b. I den **identifierare** text skriver en URL som använder följande mönster:`https://<customer domain>.qmyzone.com/metadata.php`
         
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och URL: en inloggning. Kontakta [QPrism klienten supportteamet](mailto:qsupport-ce@quatrro.com) att hämta dessa värden. 

4. Att generera den **Metadata** URL, gör du följande:

    a. Välj **App registreringar**.
    
    ![Konfigurera enkel inloggning app registreringar](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appregistrations.png)
   
    b. Välj **slutpunkter** att öppna **slutpunkter** dialogrutan.  
    
    ![Konfigurera enkel inloggning slutpunkt](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpointicon.png)

    c. Välj kopieringsknappen för att kopiera den **FEDERATION METADATADOKUMENTET** URL, och klistra in den i anteckningar.
    
    ![Konfigurera enkel inloggning slutpunkt](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpoint.png)
     
    d. Gå till egenskapssidan för **QPrism**, och kopierar den **program-ID** med hjälp av **kopiera**. Klistra in den i anteckningar.
 
    ![Konfigurera enkel inloggning program-ID](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appid.png)

    e. Generera den **URL för tjänstmetadata** med hjälp av följande mönster:`<FEDERATION METADATA DOCUMENT url>?appid=<application id>` 

5. Välj **Spara**.

    ![Konfigurera enkel inloggning spara knappen](./media/active-directory-saas-qprism-tutorial/tutorial_general_400.png)
    
6. Konfigurera enkel inloggning på den **QPrism** sida, skicka den **URL för tjänstmetadata** till den [QPrism supportteamet](mailto:qsupport-ce@quatrro.com). De se till att SAML enkel inloggning anslutningen är korrekt inställda på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen. När du lägger till den här appen från den **Active Directory** > **företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [Azure AD inbäddade dokumentationen]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan, Välj **Azure Active Directory**.

    ![Azure Active Directory-knappen](./media/active-directory-saas-qprism-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-qprism-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan överst i den **alla användare** dialogrutan **Lägg till**.

    ![Knappen Lägg till](./media/active-directory-saas-qprism-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan Gör följande:

    ![Dialogrutan användare](./media/active-directory-saas-qprism-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Välj **Skapa**.
 
### <a name="create-a-qprism-test-user"></a>Skapa en testanvändare QPrism

I det här avsnittet skapar du en användare som kallas Britta Simon i QPrism. Arbeta med den [QPrism supportteamet](mailto:qsupport-ce@quatrro.com) att lägga till användare i QPrism-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till QPrism.

![Tilldela rollen][200] 

**Tilldela Britta Simon QPrism:**

1. Öppna vyn program i Azure-portalen och gå sedan till vyn directory. Gå till **företagsprogram**, och välj **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **QPrism**.

    ![Länken QPrism i listan med program](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_app.png)  

3. Välj på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj **Lägg till**. Sedan, under **Lägg uppdrag**väljer **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. På den **användare och grupper** dialogrutan **Britta Simon** i den **användare** lista.

6. På den **användare och grupper** dialogrutan **Välj**.

7. Under **Lägg uppdrag**väljer **tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

I panelen åtkomst när du väljer panelen QPrism ska hämta loggas du automatiskt i tillämpningsprogrammet QPrism.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_203.png

