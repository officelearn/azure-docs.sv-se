---
title: 'Självstudier: Azure Active Directory-integrering med Ziflow | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: jeedes
ms.openlocfilehash: 4f667edbf6b0fab8c8dac4442d50d14252c4c4cd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354249"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Självstudier: Azure Active Directory-integrering med Ziflow

I kursen får lära du att integrera Ziflow med Azure Active Directory (AD Azure).

Integrera Ziflow med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Ziflow.
- Du kan aktivera användarna att automatiskt hämta loggat in på Ziflow (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Ziflow, behöver du följande:

- En Azure AD-prenumeration
- En Ziflow enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Ziflow från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-ziflow-from-the-gallery"></a>Att lägga till Ziflow från galleriet
Du måste lägga till Ziflow från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Ziflow i Azure AD.

**Utför följande steg för att lägga till Ziflow från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Ziflow**väljer **Ziflow** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Ziflow i resultatlistan](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Ziflow baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Ziflow motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Ziflow upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Ziflow, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Ziflow](#create-a-ziflow-test-user)**  – du har en motsvarighet för Britta Simon i Ziflow som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Ziflow program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Ziflow:**

1. I Azure-portalen på den **Ziflow** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_samlbase.png)

3. På den **Ziflow domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Ziflow domän med enkel inloggning information](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_url.png)

    a. I den **inloggning URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.ziflow.io/#/login-sso/<Unique ID>`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `urn:auth0:ziflow-production:<Unique ID>`

    > [!NOTE] 
    > Föregående värden är inte verkliga. Det unika ID-värdet i identifierare och URL: en inloggning uppdateras med faktiskt värde, som beskrivs senare i självstudierna. Kontakta [Ziflow supportteamet](mailto:support@ziflow.com) underdomän värde i URL för inloggning.
    
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-ziflow-tutorial/tutorial_general_400.png)

6. På den **Ziflow Configuration** klickar du på **konfigurera Ziflow** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out Webbadressen och SAML enkel inloggning Service** från den **Snabbreferens avsnitt.**

    ![Ziflow konfiguration](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_configure.png) 

7. I en annan webbläsarfönstret, logga in på Ziflow som en administratör.


8. Klicka på Avatar i övre högra hörnet och klicka sedan på **hantera kontot**.

    ![Hantera Ziflow konfiguration](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_manage.png)

9. Klicka på upp till vänster, **enkel inloggning**.

    ![Ziflow Configuration inloggning](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_signon.png)

10. På den **enkel inloggning** utför följande steg:

    ![Ziflow konfiguration enkel](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_page.png)

    a. Välj **typen** som **SAML2.0**.

    b.In den **tecken i URL: en** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.

    c. Överför Base64-kodade certifikatet som du har hämtat från Azure-portalen i den **X509 signeringscertifikat**.

    d. I den **logga ut URL** textruta klistra in värdet för **Sign-Out URL**, som du har kopierat från Azure-portalen.

    e. Från den **konfigurationsinställningar för leverantören identifierare** avsnittet, kopiera det markerade unika ID-värdet och lägger till dem med identifierare och logga in på URL: en i den **Ziflow domän och URL: er avsnittet** på Azure-portalen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-ziflow-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-ziflow-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-ziflow-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-ziflow-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-a-ziflow-test-user"></a>Skapa en testanvändare Ziflow

Om du vill aktivera Azure AD-användare kan logga in på Ziflow etableras de i Ziflow. I Ziflow är etablering en manuell aktivitet.

Utför följande steg om du vill konfigurera ett användarkonto:

1. Logga in på Ziflow som en administratör.

2. Gå till **personer** högst upp.

    ![Konfiguration av Ziflow personer](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_people.png)

3. Klicka på **Lägg till** och klicka sedan på **Lägg till användare**.

    ![Ziflow Configuration att lägga till användaren](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_add.png)

4. På den **lägga till en användare** popup, utför följande steg:

    ![Ziflow Configuration att lägga till användaren](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. I **e-post** text Ange e-postadressen för användaren som brittasimon@contoso.com.

    b. I **Förnamn** text Ange först namnet på användaren som Britta.

    c. I **efternamn** text Ange efternamn för användaren som Simon.

    d. Välj din Ziflow roll.

    e. Klicka på **Lägg till 1 användaren**.

    > [!NOTE]
    > Azure Active Directory kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta sina konton innan den aktiveras.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Ziflow.

![Tilldela rollen][200] 

**Om du vill tilldela Ziflow Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Ziflow**.

    ![Länken Ziflow i listan med program](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Ziflow på åtkomstpanelen du bör få automatiskt loggat in på ditt Ziflow program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_203.png

