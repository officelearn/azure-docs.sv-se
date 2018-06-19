---
title: 'Självstudier: Azure Active Directory-integrering med AwardSpring | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AwardSpring.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f115be6-4fbe-42aa-9319-7462e7a75736
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.openlocfilehash: 1c9f8dab4d872d045c4781cd8b6b0405c74df231
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35963980"
---
# <a name="tutorial-azure-active-directory-integration-with-awardspring"></a>Självstudier: Azure Active Directory-integrering med AwardSpring

I kursen får lära du att integrera AwardSpring med Azure Active Directory (AD Azure).

Integrera AwardSpring med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till AwardSpring.
- Du kan aktivera användarna att automatiskt hämta loggat in på AwardSpring (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med AwardSpring, behöver du följande:

- En Azure AD-prenumeration
- En AwardSpring enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till AwardSpring från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-awardspring-from-the-gallery"></a>Att lägga till AwardSpring från galleriet
Du måste lägga till AwardSpring från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av AwardSpring i Azure AD.

**Utför följande steg för att lägga till AwardSpring från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **AwardSpring**väljer **AwardSpring** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![AwardSpring i resultatlistan](./media/awardspring-tutorial/tutorial_awardspring_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med AwardSpring baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i AwardSpring motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i AwardSpring upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med AwardSpring, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare AwardSpring](#create-an-awardspring-test-user)**  – du har en motsvarighet för Britta Simon i AwardSpring som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt AwardSpring program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med AwardSpring:**

1. I Azure-portalen på den **AwardSpring** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/awardspring-tutorial/tutorial_awardspring_samlbase.png)

3. På den **AwardSpring domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och AwardSpring domän med enkel inloggning information](./media/awardspring-tutorial/tutorial_awardspring_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<subdomain>.awardspring.com/SignIn/SamlMetaData`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.awardspring.com/SignIn/SamlAcs`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och AwardSpring domän med enkel inloggning information](./media/awardspring-tutorial/tutorial_awardspring_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.awardspring.com/`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Kontakta [AwardSpring klienten supportteamet](mailto:support@awardspring.com) att hämta dessa värden. 

5. AwardSpring program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.
    
    ![Konfigurera enkel inloggning](./media/awardspring-tutorial/tutorial_awardSpring_attribute.png)

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ---------------| --------------- |    
    | Förnamn | User.givenName |
    | Efternamn | User.surname |
    | E-post | User.Mail |
    | Användarnamn | User.userprinicipalname |
    | studentID | < Student-ID > |

    > [!NOTE]
    > Attributet StudentID mappas med det faktiska Student-ID som måste överföras i anspråk. Kontakta [AwardSpring klienten supportteamet](mailto:support@awardspring.com) att hämta det här värdet.

    a. Ta bort befintliga attribut och lägga till nya attribut. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/awardspring-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/awardspring-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Lämna den **Namespace** tomt.
    
    d. Klicka på **Ok**

7. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/awardspring-tutorial/tutorial_awardspring_certificate.png) 

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/awardspring-tutorial/tutorial_general_400.png)
    
9. Konfigurera enkel inloggning på **AwardSpring** sida, måste du skicka den hämtade **XML-Metadata för** till [AwardSpring supportteamet](mailto:support@awardspring.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/awardspring-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/awardspring-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/awardspring-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/awardspring-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-awardspring-test-user"></a>Skapa en testanvändare AwardSpring

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i AwardSpring. AwardSpring stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt AwardSpring om den inte finns.

>[!Note]
>Om du behöver skapa en användare manuellt Kontakta [AwardSpring supportteamet](maito:support@awardspring.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till AwardSpring.

![Tilldela rollen][200] 

**Om du vill tilldela AwardSpring Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **AwardSpring**.

    ![Länken AwardSpring i listan med program](./media/awardspring-tutorial/tutorial_awardspring_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen AwardSpring på åtkomstpanelen du bör få automatiskt loggat in på ditt AwardSpring program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/awardspring-tutorial/tutorial_general_01.png
[2]: ./media/awardspring-tutorial/tutorial_general_02.png
[3]: ./media/awardspring-tutorial/tutorial_general_03.png
[4]: ./media/awardspring-tutorial/tutorial_general_04.png

[100]: ./media/awardspring-tutorial/tutorial_general_100.png

[200]: ./media/awardspring-tutorial/tutorial_general_200.png
[201]: ./media/awardspring-tutorial/tutorial_general_201.png
[202]: ./media/awardspring-tutorial/tutorial_general_202.png
[203]: ./media/awardspring-tutorial/tutorial_general_203.png

