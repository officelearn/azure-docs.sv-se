---
title: 'Självstudier: Azure Active Directory-integrering med FiscalNote | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FiscalNote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 55274f26-be7e-4514-964c-7186ecb55c4a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 72b2f53025bcc614605cb62f6e2d9895940dfd8c
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35978220"
---
# <a name="tutorial-azure-active-directory-integration-with-fiscalnote"></a>Självstudier: Azure Active Directory-integrering med FiscalNote

I kursen får lära du att integrera FiscalNote med Azure Active Directory (AD Azure).

Integrera FiscalNote med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till FiscalNote.
- Du kan aktivera användarna att automatiskt hämta loggat in på FiscalNote (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med FiscalNote, behöver du följande:

- En Azure AD-prenumeration
- En FiscalNote enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till FiscalNote från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-fiscalnote-from-the-gallery"></a>Att lägga till FiscalNote från galleriet
Du måste lägga till FiscalNote från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av FiscalNote i Azure AD.

**Utför följande steg för att lägga till FiscalNote från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **FiscalNote**väljer **FiscalNote** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![FiscalNote i resultatlistan](./media/fiscalnote-tutorial/tutorial_fiscalnote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med FiscalNote baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i FiscalNote motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i FiscalNote upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med FiscalNote, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare FiscalNote](#create-a-fiscalnote-test-user)**  – du har en motsvarighet för Britta Simon i FiscalNote som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt FiscalNote program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med FiscalNote:**

1. I Azure-portalen på den **FiscalNote** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning dialogrutan](./media/fiscalnote-tutorial/tutorial_fiscalnote_samlbase.png)

3. På den **FiscalNote domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och FiscalNote domän med enkel inloggning information](./media/fiscalnote-tutorial/tutorial_fiscalnote_url.png)
    
    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<InstanceName>.fiscalnote.com/login?client=<ClientID>&redirect_uri=https://app.fiscalnote.com/saml-login.html&audience=https://api.fiscalnote.com/&connection=<CONNECTION_NAME>&response_type=id_token%20token`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `urn:auth0:fiscalnote:<CONNECTIONNAME>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [FiscalNote klienten supportteamet](mailto:support@fiscalnote.com) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Raw)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/fiscalnote-tutorial/tutorial_fiscalnote_certificate.png)

5. Programmet FiscalNote förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet.

    ![Konfigurera enkel inloggning](./media/fiscalnote-tutorial/tutorial_attribute.png)

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i den föregående bilden och utför följande steg:
           
    | Attributnamn | Attributvärde |
    | ---------------| ----------------|
    | namn | User.userPrincipalName|
    | givenName| User.givenName|
    | familyName| User.surname|
    | e-post| User.Mail|
    
    a. Ta bort befintliga attribut och lägga till nya attribut. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/fiscalnote-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurera enkel inloggning](./media/fiscalnote-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Lämna namnområde som tomt.
    
    e. Klicka på **OK**.

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/fiscalnote-tutorial/tutorial_general_400.png)

8. På den **FiscalNote Configuration** klickar du på **konfigurera FiscalNote** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![FiscalNote konfiguration](./media/fiscalnote-tutorial/tutorial_fiscalnote_configure.png) 

9. Konfigurera enkel inloggning på **FiscalNote** sida, måste du skicka den hämtade **Certificate(Raw), Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** till [FiscalNote supportteam](mailto:support@fiscalnote.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/fiscalnote-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/fiscalnote-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/fiscalnote-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/fiscalnote-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-fiscalnote-test-user"></a>Skapa en testanvändare FiscalNote

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i FiscalNote. FiscalNote stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt FiscalNote om den inte finns.
>[!Note]
>Om du behöver skapa en användare manuellt Kontakta [FiscalNote supportteamet](mailto:support@fiscalnote.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till FiscalNote.

![Tilldela rollen][200] 

**Om du vill tilldela FiscalNote Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **FiscalNote**.

    ![Länken FiscalNote i listan med program](./media/fiscalnote-tutorial/tutorial_fiscalnote_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen FiscalNote på åtkomstpanelen du bör få automatiskt loggat in på ditt FiscalNote program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fiscalnote-tutorial/tutorial_general_01.png
[2]: ./media/fiscalnote-tutorial/tutorial_general_02.png
[3]: ./media/fiscalnote-tutorial/tutorial_general_03.png
[4]: ./media/fiscalnote-tutorial/tutorial_general_04.png

[100]: ./media/fiscalnote-tutorial/tutorial_general_100.png

[200]: ./media/fiscalnote-tutorial/tutorial_general_200.png
[201]: ./media/fiscalnote-tutorial/tutorial_general_201.png
[202]: ./media/fiscalnote-tutorial/tutorial_general_202.png
[203]: ./media/fiscalnote-tutorial/tutorial_general_203.png

