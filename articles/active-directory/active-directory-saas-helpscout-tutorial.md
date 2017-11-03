---
title: "Självstudier: Azure Active Directory-integrering med hjälp Scout | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och hjälpa Scout."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: jeedes
ms.openlocfilehash: fe8775bd39173b2adf1f82d32f5e851ef1c19931
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Självstudier: Azure Active Directory-integrering med hjälp Scout

I kursen får lära du att integrera hjälpa Scout med Azure Active Directory (AD Azure).

Integrera hjälpa Scout med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till hjälpen Scout.
- Du kan aktivera användarna att automatiskt hämta loggat in på att Scout (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med hjälp Scout behöver du följande:

- En Azure AD-prenumeration
- En hjälp Scout enkel inloggning på aktiverade prenumeration

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till hjälp Scout från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-help-scout-from-the-gallery"></a>Att lägga till hjälp Scout från galleriet
Du måste lägga till hjälp Scout från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av hjälp Scout i Azure AD.

**Utför följande steg för att lägga till hjälp Scout från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **hjälp Scout**väljer **hjälp Scout** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Hjälp Scout i resultatlistan](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med hjälp Scout baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Hjälp Scout motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Hjälp Scout upprättas.

Hjälp Scout använder e-postadresser för inloggningar, så om du vill upprätta en länk-relation samma **e-postadress** som **användarnamn** i Azure AD.

Om du vill konfigurera och testa Azure AD enkel inloggning med hjälp Scout, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare hjälpa Scout](#create-a-help-scout-test-user)**  – du har en motsvarighet för Britta Simon i Hjälp Scout som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet att Scout.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med hjälp Scout:**

1. I Azure-portalen på den **hjälp Scout** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. På den **hjälp Scout domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Hjälpinformation Scout domän URL: er och enkel inloggning](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

    a. **Identifierare** är den **”målgruppen URI (Service Provider enhets-ID)”** från hjälp Scout som börjar med`urn:`

    b. **Reply URL** är den **”efter tillbaka URL (Assertion konsumenten tjänsten URL)”** från hjälp Scout som börjar med`https://` 

    > [!NOTE] 
    > Värdena i dessa URL: er är bara exempel. Du måste uppdatera dessa värden från faktiska Reply URL och identifierare. Du kan få värdena från den **enkel inloggning** fliken under avsnittet för autentisering som beskrivs senare i självstudierna.

4. Om du vill konfigurera programmet i **SP** initierade läge, kontrollera **visa avancerade inställningar för URL: en** och utföra följande steg:

    ![Hjälpinformation Scout domän URL: er och enkel inloggning](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL som:`https://secure.helpscout.net/members/login/`
     
5. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)


7. På den **hjälp Scout Configuration** klickar du på **konfigurera hjälp Scout** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnittet**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-helpscout-tutorial/config.png) 

8. I en annan webbläsarfönster loggar du in på webbplatsen för företag att Scout som administratör.

9. När du är inloggad i klickar du på **”hantera”** översta menyn och välj sedan **”företagets”** från listrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-helpscout-tutorial/settings1.png) 
 
10. Välj **”autentisering”** i den vänstra menyn. 

    ![Konfigurera enkel inloggning](./media/active-directory-saas-helpscout-tutorial/settings2.png) 

11. Det går du till avsnittet SAML-inställningar och utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-helpscout-tutorial/settings3.png) 
 
    a. Kopiera den **återanslående URL (Assertion konsumenten tjänsten URL)** värdet och klistrar in värdet i den **Reply URL** rutan i Azure-portalen under Hjälp Scout **domän och URL: er** avsnitt.
    
    b. Kopiera den **Målgrupps-URI (Service Provider enhets-ID)** värdet och klistrar in värdet i den **identifierare** rutan i Azure-portalen under Hjälp Scout **domän och URL: er** avsnitt.

12. Växla **aktivera SAML** på och utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-helpscout-tutorial/settings4.png) 
 
    a. I **URL för enkel inloggning** textruta klistra in värdet för **inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.
    
    b. Klicka på **överför certifikat** att överföra den **Certificate(Base64)** hämtas från Azure-portalen.

    c. Ange din organisations e-domäner e.x. - `contoso.com` i den **e-postdomäner** textruta. Du kan avgränsa flera domäner med ett kommatecken. När som helst en hjälp Scout användare eller administratör som anger den specifika domänen på den [Scout för att logga in på sidan](https://secure.helpscout.net/members/login/) kommer att dirigeras till identitetsleverantör att autentisera med sina autentiseringsuppgifter.

    d. Slutligen kan du växla **inloggning kraft SAML** om du vill att användarna bara logga in på att Scout via via den här metoden. Om du fortfarande vill lämna alternativet att logga in med sina autentiseringsuppgifter för att Scout, kan du lämna den inaktiverad så inaktiverar. Även om den är aktiverad, kommer en Kontoägaren alltid kunna logga in på att Scout med sina kontolösenord.

    e. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-help-scout-test-user"></a>Skapa en hjälp Scout testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Hjälp Scout. Hjälp Scout stöder just-in-time-etablering, vilket är aktiverat som standard.

Det finns ingen åtgärd objekt i det här avsnittet. Om en användare inte redan finns i Hjälp Scout, skapas en ny när du försöker komma åt Hjälp Scout.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till hjälpen Scout.

![Tilldela rollen][200] 

**Om du vill tilldela hjälpa Scout Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **hjälp Scout**.

    ![Länken Hjälp Scout i listan med program](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen hjälpa Scout på åtkomstpanelen du bör få automatiskt loggat in på ditt program hjälper Scout.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png

