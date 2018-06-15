---
title: 'Självstudier: Azure Active Directory-integrering med båge Publishing - SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och båge Publishing - enkel inloggning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ae609583-f875-4cb8-b68e-1b0b7938e9a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: 26e80153438eb9894753c74b81d6622c7b4c7b14
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34337919"
---
# <a name="tutorial-azure-active-directory-integration-with-arc-publishing---sso"></a>Självstudier: Azure Active Directory-integrering med båge Publishing - SSO

I kursen får lära du att integrera båge Publishing - enkel inloggning med Azure Active Directory (AD Azure).

Integrera båge publicering - enkel inloggning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Båge Publishing - enkel inloggning.
- Du kan aktivera användarna att automatiskt hämta loggat in på båge Publishing - SSO (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med båge Publishing - SSO, behöver du följande:

- En Azure AD-prenumeration
- En båge publicering - aktiverad prenumeration SSO enkel inloggning

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Båge publicering - SSO från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Att lägga till Båge publicering - SSO från galleriet
Du måste lägga till Båge Publishing - SSO från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av båge Publishing - SSO till Azure AD.

**Utför följande steg för att lägga till Båge Publishing - SSO från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **båge Publishing - SSO**väljer **båge Publishing - SSO** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Båge publicering - SSO i resultatlistan](./media/active-directory-saas-arc-tutorial/tutorial_arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med båge Publishing - SSO baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste veta vilka motsvarande användaren i båge Publishing - SSO är att en användare i Azure AD för enkel inloggning ska fungera. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i båge Publishing - SSO måste upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med båge Publishing - SSO, måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en båge Publishing - SSO testanvändare](#create-an-arc-publishing---sso-test-user)**  – du har en motsvarighet för Britta Simon i båge Publishing - enkel inloggning som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i programmet för enkel inloggning din båge - publicering.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med båge Publishing - SSO:**

1. I Azure-portalen på den **båge Publishing - SSO** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-arc-tutorial/tutorial_arc_samlbase.png)

3. På den **båge Publishing - URL: er och domänen för enkel inloggning** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Båge publicering - domän för SSO och URL: er enkel inloggning information](./media/active-directory-saas-arc-tutorial/tutorial_arc_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://www.okta.com/saml2/service-provider/<Unique ID>`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Båge publicering - domän för SSO och URL: er enkel inloggning information](./media/active-directory-saas-arc-tutorial/tutorial_arc_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Kontakta [båge Publishing - klientfilerna supportteamet](mailto:inf@washpost.com) att hämta dessa värden. 

5. Båge publicering - programmet för enkel inloggning förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-arc-tutorial/tutorial_arc_attribute.png)

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ---------------| --------------- |    
    | Förnamn | User.givenName |
    | Efternamn | User.surname |
    | e-post | User.Mail |
    | grupper | User.assignedroles |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

     ![Konfigurera enkel inloggning](./media/active-directory-saas-arc-tutorial/tutorial_attribute_04.png)

     ![Konfigurera enkel inloggning](./media/active-directory-saas-arc-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Lämna den **Namespace** tomt.
    
    d. Klicka på **Ok**

    > [!NOTE]
    > Här den **grupper** attributet är mappad med **user.assignedroles**. Dessa är anpassade roller som skapats i Azure AD att mappa gruppnamn tillbaka i programmet. Du hittar mer information [här](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-enterprise-app-role-management) om hur du skapar anpassade roller i Azure AD. 

7. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-arc-tutorial/tutorial_arc_certificate.png) 

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-arc-tutorial/tutorial_general_400.png)
    
9. På den **båge Publishing - konfiguration av SSO** klickar du på **konfigurera båge Publishing - SSO** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Båge publicering - konfiguration för enkel inloggning](./media/active-directory-saas-arc-tutorial/tutorial_arc_configure.png) 

10. Konfigurera enkel inloggning på **båge Publishing - SSO** sida, måste du skicka den hämtade **certifikat (Base64), Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** till [båge Publicering - SSO supportteamet](mailto:inf@washpost.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-arc-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-arc-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-arc-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-arc-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-arc-publishing---sso-test-user"></a>Skapa testanvändare för enkel inloggning en båge - publicering

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i båge Publishing - enkel inloggning. Båge publicering - SSO stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt båge Publishing - SSO om den inte finns.

>[!Note]
>Om du behöver skapa en användare manuellt Kontakta [båge Publishing - SSO supportteamet](mailto:inf@washpost.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Båge Publishing - enkel inloggning.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon båge Publishing - SSO, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **båge Publishing - SSO**.

    ![Båge publicering - SSO länken i listan med program](./media/active-directory-saas-arc-tutorial/tutorial_arc_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på SSO-panelen på panelen åtkomst på båge - Publishing du ska hämta automatiskt loggat in på SSO-program din båge - publicering.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-arc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-arc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-arc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-arc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-arc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-arc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-arc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-arc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-arc-tutorial/tutorial_general_203.png

