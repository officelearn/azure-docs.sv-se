---
title: "Självstudier: Azure Active Directory-integrering med Salesforce | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 9aecd7310ad44eb585c1326a3c705ed822735b9a
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Självstudier: Azure Active Directory-integrering med Salesforce

I kursen får lära du att integrera Salesforce med Azure Active Directory (AD Azure).

Integrera Salesforce med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Salesforce.
- Du kan aktivera användarna att automatiskt hämta loggat in till Salesforce (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Salesforce, behöver du följande:

- En Azure AD-prenumeration
- En Salesforce enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Salesforce från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-salesforce-from-the-gallery"></a>Att lägga till Salesforce från galleriet
Du måste lägga till Salesforce från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Salesforce i Azure AD.

**Utför följande steg för att lägga till Salesforce från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Salesforce**väljer **Salesforce** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Salesforce i resultatlistan](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Salesforce baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Salesforce motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Salesforce upprättas.

I Salesforce, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Salesforce, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Salesforce](#create-a-salesforce-test-user)**  – du har en motsvarighet för Britta Simon i Salesforce som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Salesforce-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Salesforce:**

1. I Azure-portalen på den **Salesforce** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. På den **Salesforce-domänen och URL: er** avsnittet, utför följande steg:

    ![URL: er och Salesforce-domän med enkel inloggning information](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)
    
    a. I den **inloggnings-URL** textruta Skriv det värde som använder följande mönster:
    
    Enterprise-konto:`https://<subdomain>.my.salesforce.com`

    Utvecklarkonto för:`https://<subdomain>-dev-ed.my.salesforce.com`
    
    b. I den **identifierare** textruta Skriv det värde som använder följande mönster:
    
    Enterprise-konto:`https://<subdomain>.my.salesforce.com`

    Utvecklarkonto för:`https://<subdomain>-dev-ed.my.salesforce.com`
    
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Salesforce klienten supportteamet](https://help.salesforce.com/support) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **certifikat** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. På den **Salesforce Configuration** klickar du på **konfigurera Salesforce** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Salesforce-konfiguration](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 

7. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce-administratörskonto.

8. Klicka på den **installationsprogrammet** under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/configure1.png)

9. Rulla ned till den **inställningar** i navigeringsfönstret klickar du på **identitet** att expandera avsnittet relaterade. Klicka på **inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

10. På den **inställningar för enkel inloggning** klickar du på den **redigera** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)
    
    > [!NOTE]
    > Om det inte går att aktivera enkel inloggning inställningarna för ditt Salesforce-konto kan du behöva kontakta [Salesforce klienten supportteamet](https://help.salesforce.com/support). 

11. Välj **SAML aktiverat**, och klicka sedan på **spara**.

      ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
12. Om du vill konfigurera SAML enkel inloggning för, klickar du på **ny**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

13. På den **SAML enkel inloggning inställningen redigera** kontrollerar du följande konfigurationer:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a. För den **namnet** skriver du ett eget namn för den här konfigurationen. Att tillhandahålla ett värde för **namn** automatiskt fylla i **API-namnet** textruta.

    b. I den **utfärdaren** och klistra in värdet för fältet **SAML enhets-ID**, som du har kopierat från Azure-portalen.

    c. I den **enhets-Id textruta**, ange ditt Salesforce-domännamn med hjälp av följande mönster:
      
      * Enterprise-konto:`https://<subdomain>.my.salesforce.com`
      * Utvecklarkonto för:`https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Att överföra den **providern identitetscertifikat**, klickar du på **Välj fil** att bläddra och välja den certifikatfil som du har hämtat från Azure-portalen.

    e. Som **SAML identitetstyp**, väljer du något av följande alternativ:
    
      * Välj **Assertion innehåller användarens Salesforce användarnamn**om användarens Salesforce Username skickas i SAML-kontroll

      * Välj **Assertion innehåller Federation-ID från användarobjektet**om Federation-ID från användarobjektet skickas i SAML-kontroll

      * Välj **Assertion innehåller Använd ID från användarobjektet**om användar-ID från användarobjektet skickas i SAML-kontroll

    f. För **SAML identitet plats**väljer **identitet är i elementet NameIdentifier i instruktionen ämne**.

    g. För **providern initierade begära Tjänstbindning**väljer **HTTP-omdirigering**.

    h. I **identitet providern inloggnings-URL** textruta klistra in värdet för **inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen
    
    Jag. Klicka slutligen på **spara** tillämpa SAML enkel inloggning inställningarna.

14. Klicka på det vänstra navigeringsfönstret i Salesforce **Företagsinställningar** Expandera avsnittet relaterade och klicka sedan på **min domän**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

15. Rulla ned till den **Autentiseringskonfiguration** avsnittet och klicka på den **redigera** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

16. I den **Autentiseringskonfiguration** avsnitt, kontrollera den **inloggningssidan** som **autentisering Servie** SAML SSO-konfigurationen och klicka sedan på  **Spara**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Om mer än en autentiseringstjänst är markerat uppmanas användarna att välja vilka Autentiseringstjänsten som de vill logga in med vid initiering av enkel inloggning till Salesforce-miljön. Om du inte vill att det ska ske, så du bör **lämnar du alla andra autentiseringstjänster avmarkerat**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-salesforce-test-user"></a>Skapa en testanvändare Salesforce

I det här avsnittet skapas en användare som kallas Britta Simon i Salesforce. Salesforce stöder just-in-time-allokering som är aktiverad som standard.
Det finns ingen åtgärd objekt i det här avsnittet. Om en användare inte redan finns i Salesforce, skapas en ny när du försöker komma åt Salesforce.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Salesforce.

![Tilldela rollen][200] 

**Om du vill tilldela Salesforce Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Salesforce**.

    ![Salesforce-länken i listan med program](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Salesforce på åtkomstpanelen du bör få automatiskt loggat in på ditt Salesforce-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_203.png

