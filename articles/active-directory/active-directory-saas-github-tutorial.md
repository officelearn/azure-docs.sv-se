---
title: "Självstudier: Azure Active Directory-integrering med GitHub | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GitHub."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4395bd95-05de-4deb-87a5-dc3bc8ac4d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 9dc12bc2e313bcb2000724d035156c5054d14e1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Självstudier: Azure Active Directory-integrering med GitHub

I kursen får lära du att integrera GitHub med Azure Active Directory (AD Azure).

Integrera GitHub med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till GitHub
- Du kan aktivera användarna att automatiskt hämta loggat in på GitHub (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - till Azure-hanteringsportalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med GitHub, behöver du följande:

- En Azure AD-prenumeration
- En GitHub enkel inloggning på aktiverade prenumeration


> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.


Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion av en månad [här](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till GitHub från galleriet
2. Konfigurera och testa Azure AD enkel inloggning


## <a name="adding-github-from-the-gallery"></a>Att lägga till GitHub från galleriet
Du måste lägga till GitHub från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av GitHub i Azure AD.

**Utför följande steg för att lägga till GitHub från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **GitHub.com**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-github-tutorial/tutorial_github_search02.png)

5. Välj i resultatpanelen **GitHub**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-github-tutorial/tutorial_github_search_result02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med GitHub baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i GitHub motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i GitHub upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i GitHub.

Om du vill konfigurera och testa Azure AD enkel inloggning med GitHub, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare i GitHub](#creating-a-GitHub-test-user)**  – du har en motsvarighet för Britta Simon i GitHub som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt GitHub-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med GitHub:**

1. I Azure-hanteringsportalen på den **GitHub** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserade inloggning** att aktivera enkel inloggning på.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-github-tutorial/tutorial_github_01.png)

3. På den **GitHub domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-github-tutorial/tutorial_github_saml011.png)

    a. I den **inloggnings-URL** textruta Skriv värdet som:`https://github.com/orgs/<entity-id>/sso`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://github.com/orgs/<entity-id>`

    > [!NOTE] 
    > Observera att detta inte är verkliga värden. Du måste uppdatera dessa värden med de faktiska rör-URL och identifierare. Här rekommenderar vi att du om du vill använda det unika värdet på strängen i identifieraren. Gå till GitHub-administratör att hämta dessa värden. 

4. På den **användarattribut** väljer **användar-ID** som user.mail.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-github-tutorial/tutorial_github_attribute_new01.png)
    
5. På den **SAML-signeringscertifikat** klickar du på **Skapa nytt certifikat**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-github-tutorial/tutorial_github_03.png)

6. På den **skapa nya certifikat** dialogrutan, klicka på kalenderikonen och välj en **förfallodatum**. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-github-tutorial/tutorial_general_300.png)

7. På den **SAML-signeringscertifikat** väljer **aktivera nya certifikatet** och på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-github-tutorial/tutorial_github_04.png)

8. På popup-fönstret **förnyelsecertifikat** -fönstret klickar du på **OK**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-github-tutorial/tutorial_general_400.png)

9. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-github-tutorial/tutorial_github_05.png) 

10. På den **GitHub Configuration** klickar du på **konfigurera GitHub** att öppna **konfigurera inloggning** fönster.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-github-tutorial/tutorial_github_06.png) 

    ![Konfigurera enkel inloggning](./media/active-directory-saas-github-tutorial/tutorial_github_07.png)

11. Logga in på webbplatsen GitHub organisation som en administratör i en annan webbläsarfönster.

12. Gå till **inställningar** och på **säkerhet**

    ![Inställningar](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_03.png)

13. Kontrollera den **aktivera SAML-autentisering** rutan avslöja konfigurationsfält enkel inloggning. Använd sedan på enkel inloggning URL-adressen för att uppdatera den enda inloggnings-URL på Azure AD-konfiguration.

    ![Inställningar](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_13.png)

14. Konfigurera följande fält:

    a. **Logga in URL: en**: Ange **SAML enkel inloggning Tjänstwebbadress** från den **konfigurera GitHub** avsnittet om Azure AD

    b. **Utfärdaren**: Ange **SAML enhets-ID** från den **konfigurera GitHub** avsnittet om Azure AD

    c. **Certifikatets offentliga**: öppna hämtat certifikat från Azure AD i en anteckningar och kopiera innehållet inklusive ”BEGIN CERTIFICATE” och ”END CERTIFICATE”

    ![Inställningar](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_051.png)

15. Klicka på **Test SAML-konfiguration** Kontrollera att inga verifieringsfel eller fel under enkel inloggning.

    ![Inställningar](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_06.png)

16. Klicka på **spara**

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och på **alla användare** att visa en lista över användare.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**. 


### <a name="creating-a-github-test-user"></a>Skapa en testanvändare i GitHub

För att aktivera Azure AD-användare att logga in på GitHub, måste de etableras i GitHub.  
När det gäller GitHub är etablering en manuell aktivitet.

**Utför följande steg för att etablera en användarkonton:**

1. Logga in på webbplatsen GitHub företag som administratör.

2. Klicka på **personer**.

    ![Personer](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_08.png "personer")

3. Klicka på **inbjudan medlem**.

    ![Bjuda in användare](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_09.png "bjuda in användare")

4. På den **inbjudan medlem** dialogrutan utför följande steg:

    a. I den **e-post** textruta skriver Britta Simon konto e-postadress.

    ![Bjuda in](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_10.png "bjuda in")
    
    b. Klicka på **skicka inbjudan**.

    ![Bjuda in](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_11.png "bjuda in")

    > [!NOTE]
    > Innehavaren för Azure Active Directory-konto ett e-postmeddelande och länken för att bekräfta sina konton innan den aktiveras.


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till GitHub.

![Tilldela användare][200] 

**Om du vill tilldela GitHub Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **GitHub.com**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-github-tutorial/tutorial_github_search_result021.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    


### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på GitHub-panelen på åtkomstpanelen du ska hämta loggat in på ditt GitHub-program. Du måste logga in som en organisation konto men sedan måste du logga in med ditt eget konto.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-github-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-github-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-github-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-github-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-github-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-github-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-github-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-github-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-github-tutorial/tutorial_general_203.png
