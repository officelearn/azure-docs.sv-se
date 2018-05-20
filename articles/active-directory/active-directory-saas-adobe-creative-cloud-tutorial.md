---
title: 'Självstudier: Azure Active Directory-integrering med Adobe kreativa molntjänster | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adobe kreativa moln.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 808debcee11eb83f7e6afa1f3443a35c9f6767c4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Självstudier: Azure Active Directory-integrering med Adobe kreativa moln

I kursen får lära du att integrera Adobe kreativa moln med Azure Active Directory (AD Azure).

Integrera Adobe kreativa moln med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Adobe kreativa moln.
- Du kan aktivera användarna att automatiskt hämta loggat in på Adobe kreativa molnet (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Adobe kreativa molnet, behöver du följande:

- En Azure AD-prenumeration
- En Adobe kreativa enkel inloggning aktiverad molnprenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Adobe kreativa moln från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Att lägga till Adobe kreativa moln från galleriet
Du måste lägga till Adobe kreativa moln från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Adobe kreativa moln i Azure AD.

**Utför följande steg för att lägga till Adobe kreativa moln från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Adobe kreativa molnet**väljer **Adobe kreativa molnet** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Adobe kreativa moln i resultatlistan](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Adobe kreativa molnet baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Adobe kreativa molnet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Adobe kreativa molnet upprättas.

I Adobe kreativa moln, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Adobe kreativa molnet, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Adobe kreativa molnet](#create-an-adobe-creative-cloud-test-user)**  – du har en motsvarighet för Britta Simon i Adobe kreativa moln som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Adobe kreativa molnet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Adobe kreativa molnet:**

1. I Azure-portalen på den **Adobe kreativa molnet** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_samlbase.png)

3. På den **Adobe kreativa molnet domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i IDP initierade läge:

    ![URL: er och Adobe kreativa molnet domän med enkel inloggning information](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://www.okta.com/saml2/service-provider/<token>`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare och Reply-URL. Kontakta [Adobe kreativa Cloud klienten supportteamet](https://helpx.adobe.com/in/contact/support.html) att hämta dessa värden. 

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Adobe kreativa molnet domän med enkel inloggning information](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url2.png)

    I den **inloggnings-URL** textruta Skriv värdet som: `https://adobe.com`

5. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_certificate.png)
     
6. Adobe kreativa molnapp förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den **användarattribut** för programmet. Följande skärmbild visar ett exempel för det här.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute.png)

7. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:

    | Attributnamn | Attributvärde |
    | ---------------| ----------------|
    | Förnamn |User.givenName |
    | Efternamn |User.surname |
    | E-post |User.Mail |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **OK**.
    
8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_400.png)
    
9. På den **Adobe kreativa Molnkonfigurationen** klickar du på **Konfigurera Adobe kreativa molnet** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnittet**.

    ![Adobe kreativa Molnkonfigurationen](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_configure.png)
    
10. I en annan webbläsarfönstret, logga in på [Adobe administratörskonsolen](https://adminconsole.adobe.com) som administratör.

11. Gå till **inställningar** på det övre navigeringsfältet och välj sedan **identitet**. Listan över domäner öppnas. Klicka på **konfigurera** länken mot din domän. Utför följande steg på **enkel inloggning på konfiguration krävs** avsnitt. Mer information finns i [konfigurera en domän](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Inställningar för](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "inställningar")
    
    a. Klicka på **Bläddra** hämtade certifikatet från Azure AD för att överföra **IDP certifikat**.
    
    b. I den **IDP utfärdaren** textruta, ange värdet för **SAML enhets-Id** som du kopierade från **konfigurera inloggning** avsnitt i Azure-portalen.
    
    c. I den **IDP inloggnings-URL** textruta, ange värdet för **URL för SAML SSO-Service** som du kopierade från **konfigurera inloggning** avsnitt i Azure-portalen.
    
    d. Välj **HTTP - omdirigering** som **IDP bindning**.
    
    e. Välj **e-postadress** som **inloggningen Användarinställning**.
    
    f. Klicka på **spara** knappen.

12. Instrumentpanelen visas nu XML **”hämta Metadata”** filen. Den innehåller Adobe EntityDescriptor Webbadressen och AssertionConsumerService. Öppna filen och konfigurera dem i Azure AD-program.

    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Använd EntityDescriptor värdet Adobe för **identifierare** på den **konfigurera Appinställningar** dialogrutan.

    b. Använd AssertionConsumerService värdet Adobe för **Reply URL** på den **konfigurera Appinställningar** dialogrutan.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-adobe-creative-cloud-test-user"></a>Skapa en testanvändare Adobe kreativa moln

För att aktivera Azure AD-användare att logga in på Adobe kreativa molnet, måste de etableras i Adobe kreativa moln. Adobe kreativa moln är etablering en manuell aktivitet.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Utför följande steg för att etablera en användarkonton:

1. Logga in på [Adobe administratörskonsolen](https://adminconsole.adobe.com) platsen som en administratör.

2. Lägga till användaren i Adobe-konsolen som federerad ID och tilldela dem till en profil för produkten. Detaljerad information om att lägga till användare finns [lägga till användare i administratörskonsolen för Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. Nu ange din e-postadress/upn i formuläret Adobe inloggning, tryck på tab och du bör federerad tillbaka till Azure AD:
    * Webbåtkomst: www.adobe.com > Logga in
    * I verktyget skrivbordsapp > Logga in
    * I programmet > Hjälp > Logga in

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Adobe kreativa molnet.

![Tilldela rollen][200] 

**Om du vill tilldela Adobe kreativa molnet Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Adobe kreativa molnet**.

    ![Länken Adobe kreativa moln i listan med program](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Adobe kreativa molnet på åtkomstpanelen du bör få automatiskt inloggade i tillämpningsprogrammet Adobe kreativa molnet.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfigurera en domän (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
* [Konfigurera Azure för användning med Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png

