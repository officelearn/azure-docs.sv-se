---
title: 'Självstudier: Azure Active Directory-integrering med XaitPorter | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och XaitPorter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d33c7cb7-0550-425b-882a-619a713a71b7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: jeedes
ms.openlocfilehash: 6851c3e6a58a405b5ee6d214dc87f85fa287a51b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354307"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Självstudier: Azure Active Directory-integrering med XaitPorter

I kursen får lära du att integrera XaitPorter med Azure Active Directory (AD Azure).

Integrera XaitPorter med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till XaitPorter.
- Du kan aktivera användarna att automatiskt hämta loggat in på XaitPorter (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med XaitPorter, behöver du följande:

- En Azure AD-prenumeration
- En XaitPorter enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till XaitPorter från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-xaitporter-from-the-gallery"></a>Att lägga till XaitPorter från galleriet
Du måste lägga till XaitPorter från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av XaitPorter i Azure AD.

**Utför följande steg för att lägga till XaitPorter från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **XaitPorter**väljer **XaitPorter** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![XaitPorter i resultatlistan](./media/active-directory-saas-xaitporter-tutorial/tutorial_xaitporter_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med XaitPorter baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i XaitPorter motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i XaitPorter upprättas.

I XaitPorter, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med XaitPorter, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare XaitPorter](#create-a-xaitporter-test-user)**  – du har en motsvarighet för Britta Simon i XaitPorter som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt XaitPorter program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med XaitPorter:**

1. I Azure-portalen på den **XaitPorter** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-xaitporter-tutorial/tutorial_xaitporter_samlbase.png)

3. På den **XaitPorter domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och XaitPorter domän med enkel inloggning information](./media/active-directory-saas-xaitporter-tutorial/tutorial_xaitporter_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.xaitporter.com/saml/login`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<subdomain>.xaitporter.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [XaitPorter klienten supportteamet](https://www.xait.com/support/) att hämta dessa värden.
     
4. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **webbadress Federation Metadata** och klistra in den i anteckningar. 

    ![Länken hämta certifikatet](./media/active-directory-saas-xaitporter-tutorial/tutorial_xaitporter_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-xaitporter-tutorial/tutorial_general_400.png)

6. Ange den **IP-adress** eller **webbadress Federation Metadata** till den [SmartRecruiters supportteam](https://www.smartrecruiters.com/about-us/contact-us/), så att XaitPorter kan se till att IP-adress kan nås från din Konfigurera listan på sidan XaitPorter-instans. 

7. I en annan webbläsarfönster loggar du in på webbplatsen XaitPorter företag som administratör.

8. Klicka på **Admin**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-xaitporter-tutorial/user1.png)

9. Välj **hantera enkel inloggning** från den **systeminställningarna** listrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-xaitporter-tutorial/user2.png)

10. I den **hantera enstaka SIGN-ON** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-xaitporter-tutorial/user3.png)

    a. Välj **aktivera enkel inloggning, autentisering**.

    b. I **identitet providerinställningar** textruta klistra in **webbadress Federation Metadata** som du har kopierat från Azure-portalen och klicka på **hämta**.

    c. Välj **Aktivera skapa automatiskt användare**.

    d. Klicka på **OK**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-xaitporter-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-xaitporter-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-xaitporter-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-xaitporter-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-xaitporter-test-user"></a>Skapa en testanvändare XaitPorter

I det här avsnittet skapar du en användare som kallas Britta Simon i XaitPorter. Arbeta med [XaitPorter klienten supportteamet](https://www.xait.com/support/) att lägga till användare i XaitPorter-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till XaitPorter.

![Tilldela rollen][200] 

**Om du vill tilldela XaitPorter Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **XaitPorter**.

    ![Länken XaitPorter i listan med program](./media/active-directory-saas-xaitporter-tutorial/tutorial_xaitporter_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen XaitPorter på åtkomstpanelen du bör få automatiskt loggat in på ditt XaitPorter program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-xaitporter-tutorial/tutorial_general_203.png

