---
title: 'Självstudier: Azure Active Directory-integrering med kunskaper Manager | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och kunskaper Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: dab8debd-3b7b-4656-9bf0-1963ad8fce05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.openlocfilehash: 610ef23dff752a1503b81e9b6a4ffb651e2b0cf5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346844"
---
# <a name="tutorial-azure-active-directory-integration-with-skills-manager"></a>Självstudier: Azure Active Directory-integrering med kunskaper Manager

I kursen får lära du att integrera kunskaper Manager med Azure Active Directory (AD Azure).

Integrera kunskaper Manager med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till kunskaper Manager.
- Du kan aktivera användarna att automatiskt hämta loggat in på kunskaper Manager (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med kunskaper Manager behöver du följande:

- En Azure AD-prenumeration
- En kunskaper Manager enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägger till kunskaper Manager från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-skills-manager-from-the-gallery"></a>Lägger till kunskaper Manager från galleriet
Du måste lägga till kunskaper Manager från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av kunskaper Manager i Azure AD.

**Utför följande steg för att lägga till kunskaper Manager från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **kunskaper Manager**väljer **kunskaper Manager** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Kunskaper Manager i resultatlistan](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med kunskaper Manager baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i kunskaper Manager motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i kunskaper Manager upprättas.

I kunskaper Manager tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med kunskaper Manager, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare kunskaper Manager](#create-a-skills-manager-test-user)**  – du har en motsvarighet för Britta Simon i kunskaper Manager som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i dina kunskaper Manager-programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med kunskaper Manager:**

1. I Azure-portalen på den **kunskaper Manager** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_samlbase.png)

3. På den **kunskaper Manager-domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och kunskaper Manager-domän med enkel inloggning information](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://subdomain.skills-manager.com/kennametal`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://subdomain.skills-manager.com/public/SamlLogin2.aspx`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare och Reply-URL. Kontakta [kunskaper Manager supportteamet](https://www.ibm.com/support/uk/?lnk=msu_uk) att hämta dessa värden.
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_400.png)

6. På den **kunskaper Manager Configuration** klickar du på **konfigurera kunskaper Manager** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfiguration av kunskaper Manager](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_configure.png) 

7. Konfigurera enkel inloggning på **kunskaper Manager** sida, måste du skicka den hämtade **Certificate(Base64)**, **Sign-Out URL, SAML enhets-ID och SAML inloggning tjänst-URL för enkel** till [kunskaper Manager supportteamet](https://www.ibm.com/support/uk/?lnk=msu_uk). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-skillsmanager-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-skillsmanager-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-skillsmanager-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-skillsmanager-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-a-skills-manager-test-user"></a>Skapa en testanvändare kunskaper Manager

I det här avsnittet skapar du en användare som kallas Britta Simon i kunskaper Manager. Arbeta med [kunskaper Manager supportteamet](https://www.ibm.com/support/uk/?lnk=msu_uk) att lägga till användare i kunskaper Manager-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till kunskaper Manager.

![Tilldela rollen][200] 

**Om du vill tilldela kunskaper Manager Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **kunskaper Manager**.

    ![Länken kunskaper Manager i listan med program](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen kunskaper Manager på åtkomstpanelen du bör få automatiskt loggat in på dina kunskaper Manager-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_203.png

