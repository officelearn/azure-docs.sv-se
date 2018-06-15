---
title: 'Självstudier: Azure Active Directory-integrering med mindWireless | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och mindWireless.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bd00a339-27c9-4904-b66f-a95bf597ac3c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: jeedes
ms.openlocfilehash: b3be6f8a0c34278b699d0eac8faa2abaaa93d114
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349173"
---
# <a name="tutorial-azure-active-directory-integration-with-mindwireless"></a>Självstudier: Azure Active Directory-integrering med mindWireless

I kursen får lära du att integrera mindWireless med Azure Active Directory (AD Azure).

Integrera mindWireless med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till mindWireless.
- Du kan aktivera användarna att automatiskt hämta loggat in på mindWireless (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med mindWireless, behöver du följande:

- En Azure AD-prenumeration
- En mindWireless enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till mindWireless från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-mindwireless-from-the-gallery"></a>Att lägga till mindWireless från galleriet
Du måste lägga till mindWireless från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av mindWireless i Azure AD.

**Utför följande steg för att lägga till mindWireless från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **mindWireless**väljer **mindWireless** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![mindWireless i resultatlistan](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med mindWireless baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i mindWireless motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i mindWireless upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med mindWireless, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare mindWireless](#create-a-mindwireless-test-user)**  – du har en motsvarighet för Britta Simon i mindWireless som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt mindWireless program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med mindWireless:**

1. I Azure-portalen på den **mindWireless** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning dialogrutan](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_samlbase.png)

3. På den **mindWireless domän och URL: er** avsnittet, utför följande steg:

    ![mindWireless domän och URL: er med enkel inloggning information](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<subdomain>.mwsmart.com/`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.mwsmart.com/SAML/AssertionConsumerService.aspx`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare och Reply-URL. Kontakta [mindWireless supportteam](mailto:sdulloor@mindwireless.com) att hämta dessa värden.

4. Programmet mindWireless förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut.

5. Följande skärmbild visar ett exempel för den. Anspråkets namn alltid vara **anställnings-ID** och värdet som vi har mappats till user.employeeid som innehåller EmployeeID för användaren. Mappning från Azure AD att mindWireless görs här på EmployeeID men du kan mappa den till ett annat värde som också baserat på dina inställningar för programmet. Du kan arbeta med den [mindWireless supportteam](mailto:sdulloor@mindwireless.com) först att använda rätt ID för en användare och mappa värdet med den **anställnings-ID** anspråk.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-mindwireless-tutorial/tutorial_attribute.png)

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i den föregående bilden och utför följande steg:
    
    | Attributnamn | Attributvärde | Namespace-värde |
    | -------------- | --------------- | ----------------|
    | Anställnings-ID | User.EmployeeID | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-mindwireless-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/active-directory-saas-mindwireless-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. I den **Namespace** textruta skriver namnområdesvärdet som visas för den raden.
    
    e. Klicka på **OK**.
    
7. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_certificate.png) 

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-mindwireless-tutorial/tutorial_general_400.png)

9. På den **mindWireless Configuration** klickar du på **konfigurera mindWireless** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![mindWireless konfiguration](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_configure.png) 

10. Konfigurera enkel inloggning på **mindWireless** sida, måste du skicka den hämtade **Certificate(Base64), SAML enkel inloggning Tjänstwebbadress**, och **SAML enhets-ID** till [mindWireless supportteam](mailto:sdulloor@mindwireless.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-mindwireless-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-mindwireless-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-mindwireless-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-mindwireless-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.

### <a name="create-a-mindwireless-test-user"></a>Skapa en testanvändare mindWireless

I det här avsnittet skapar du en användare som kallas Britta Simon i mindWireless. Arbeta med [mindWireless supportteam](mailto:sdulloor@mindwireless.com) att lägga till användare i mindWireless-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till mindWireless.

![Tilldela rollen][200] 

**Om du vill tilldela mindWireless Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **mindWireless**.

    ![Länken mindWireless i listan med program](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen mindWireless på åtkomstpanelen du bör få automatiskt loggat in på ditt mindWireless program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_203.png

