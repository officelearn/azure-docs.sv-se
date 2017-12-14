---
title: "Självstudier: Azure Active Directory-integrering med Shmoop för skolorna | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Shmoop för skolorna."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jeedes
ms.openlocfilehash: 48db70834f96adbb7097457caca8489ea1a57da5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>Självstudier: Azure Active Directory-integrering med Shmoop för skolorna

I kursen får lära du att integrera Shmoop för skolorna med Azure Active Directory (AD Azure).

Integrera Shmoop för skolorna med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Shmoop för skolorna.
- Du kan aktivera användarna att få loggas automatiskt Shmoop för skolorna med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats--Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Shmoop för skolorna behöver du följande:

- En Azure AD-prenumeration
- En Shmoop för skolorna enkel inloggning på aktiverat prenumeration

> [!NOTE]
> Vi rekommenderar inte använda en produktionsmiljö för att testa stegen i den här självstudiekursen.

Vi rekommenderar följande för att testa stegen i den här självstudiekursen:

- Med hjälp av din produktion evironment endast om det behövs.
- Hämta en [kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/) om du inte redan har en utvärderingsversion Azure AD-miljö.

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Shmoop för skolorna från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-shmoop-for-schools-from-the-gallery"></a>Lägg till Shmoop för skolorna från galleriet
Du måste lägga till Shmoop för skolorna från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Shmoop för skolorna i Azure AD.

**Om du vill lägga till Shmoop för skolorna från galleriet, gör du följande:**

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till ett nytt program, Välj den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Shmoop för skolorna**. Välj **Shmoop för skolorna** från resultaten, Välj den **Lägg till** för att lägga till programmet.

    ![Shmoop för skolorna i resultatlistan](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Shmoop för skolorna utifrån en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD att veta vilka motsvarande användaren i Shmoop för skolorna är att en användare i Azure AD. Med andra ord måste du upprätta en anslutning mellan en Azure AD-användare och relaterade användaren i Shmoop för skolorna.

I Shmoop för skolorna ger den **användarnamn** värdet samma värde i **användarnamn** i Azure AD. Du har nu skapat länken relationen.

Slutför följande byggblock för att konfigurera och testa Azure AD enkel inloggning med Shmoop för skolorna:

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on) att användarna ska kunna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare Shmoop för skolorna](#create-a-shmoop-for-schools-test-user) har en motsvarighet för Britta Simon Shmoop för skolorna som är kopplad till Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#test-single-sign-on) att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Shmoop för skolorna program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Shmoop för skolorna:**

1. I Azure-portalen på den **Shmoop för skolorna** programmet integration anger **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. I den **enkel inloggning** i listrutan under dialogrutan **läget för enkel inloggning**väljer **SAML-baserade inloggning**.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. I den **Shmoop för skolorna domän och URL: er** avsnittet, gör du följande:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. I den **inloggnings-URL** Skriv en URL med följande mönster:`https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. I den **identifierare** Skriv en URL med följande mönster:`https://schools.shmoop.com/<uniqueid>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta den [Shmoop för skolorna klienten supportteamet](mailto:support@shmoop.com) att hämta dessa värden. 
 
4. Programmet Shmoop för skolorna förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den **användarattribut** avsnitt på sidan program integrering. Följande skärmbild visar hur du konfigurerar intyg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Shmoop för skolan stöder två roller för användare: **lärare** och **Student**. Konfigurera dessa roller i Azure AD så att användare kan tilldelas lämpliga roller. Information om hur du konfigurerar roller i Azure AD finns [rollbaserad åtkomstkontroll i molnprogram med Azure AD](http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/).
    
5. I den **användarattribut** under den **enkel inloggning** dialogrutan Konfigurera attributet SAML-token som visas i föregående bild.  Sedan gör du följande:

    | Attributets namn | Attributvärdet |
    | -------------- | --------------- |
    | roll           | User.assignedroles |

    a. Öppna den **lägga till attributet** dialogrutan **Lägg till attributet**.
    
    ![Konfigurera enkel inloggning ](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    b. I den **namnet** skriver attributets namn som visas för den raden.
    
    c. Från den **värdet** väljer du det attributvärde som visas för den raden.

    d. Lämna den **Namespace** tom.
    
    e. Välj **Ok**.

6. Välj knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_400.png)

7. Att generera den **Metadata** URL, gör du följande:

    a. Välj **App registreringar**.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_appregistrations.png)
   
    b. Öppna den **slutpunkter** dialogrutan **slutpunkter**.  
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_endpointicon.png)

    c. Välj kopieringsknappen för att kopiera den **FEDERATION METADATADOKUMENTET** URL och klistra in den i anteckningar.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_endpoint.png)
     
    d. Gå till egenskapssidan för **Shmoop för skolorna**. Kopiera den **program-ID** med hjälp av den **kopiera** knappen. Klistra in den i anteckningar.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_appid.png)

    e. Generera den **URL för tjänstmetadata** med hjälp av följande mönster: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`.   

8. Konfigurera enkel inloggning på den **Shmoop för skolorna** sida, måste du skicka den **URL för tjänstmetadata** till den [Shmoop för skolorna supportteam](mailto:support@shmoop.com).

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) medan du installerar appen. När du lägger till den här appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentation på [Azure AD inbäddade dokumentationen]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare kallas Britta Simon i Azure-portalen.

   ![Skapa en testanvändare i Azure AD][100]

**Om du vill skapa en testanvändare i Azure AD, gör du följande:**

1. I Azure-portalen i den vänstra rutan, Välj den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**. Välj sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan rutan, gör du följande:

    ![Dialogrutan användare](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Välj **Skapa**.
 
### <a name="create-a-shmoop-for-schools-test-user"></a>Skapa en Shmoop för skolorna testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Shmoop för skolorna. Shmoop för skolorna stöder just-in-time-allokering som är aktiverad som standard. Det finns ingen åtgärd objekt i det här avsnittet. Om en ny användare ännu inte finns, skapas det under försök att komma åt Shmoop för skolorna.

>[!NOTE]
>Om du behöver skapa en användare manuellt kan kontakta den [Shmoop för skolorna supportteam](mailto:support@shmoop.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Shmoop för skolorna.

![Tilldela rollen][200] 

**Om du vill tilldela Shmoop för skolorna Britta Simon gör du följande:**

1. Öppna program i Azure-portalen. Gå till **företagsprogram** i vyn directory.  Välj därefter **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Shmoop för skolorna**.

    ![Länken Shmoop för skolorna i listan med program](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. Välj på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj den **Lägg till** knappen. I den **Lägg uppdrag** dialogrutan **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** dialogrutan **Britta Simon** på användarlistan.

6. I den **användare och grupper** dialogrutan klickar du på den **Välj** knappen. 

7. I den **Lägg uppdrag** dialogrutan markerar du den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

När du väljer den **Shmoop för skolorna** panelen i panelen åtkomst ska hämta loggas du automatiskt till Shmoop för skolorna-program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier för att integrera SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_203.png

