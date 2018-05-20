---
title: 'Självstudier: Azure Active Directory-integrering med Salesforce Sandbox | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 0660bdb3592e36cb25f68243f16e32d7e51fb85c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Självstudier: Azure Active Directory-integrering med Salesforce Sandbox

I kursen får lära du att integrera Salesforce Sandbox med Azure Active Directory (AD Azure).

Integrera Salesforce Sandbox med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Salesforce Sandbox.
- Du kan aktivera användarna att automatiskt hämta loggat in på Salesforce begränsat läge (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Salesforce Sandbox behöver du följande:

- En Azure AD-prenumeration
- En Salesforce Sandbox enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Salesforce Sandbox från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Att lägga till Salesforce Sandbox från galleriet
Du måste lägga till Salesforce Sandbox från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Salesforce Sandbox i Azure AD.

**Utför följande steg för att lägga till Salesforce Sandbox från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Salesforce Sandbox**väljer **Salesforce Sandbox** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Salesforce Sandbox i resultatlistan](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Du konfigurera och testa Azure AD enkel inloggning med Salesforce Sandbox baserat på en testanvändare som kallas ”Britta Simon” i det här avsnittet.

Azure AD måste du känna till användaren i Salesforce Sandbox motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Salesforce Sandbox upprättas.

I Salesforce Sandbox tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Salesforce Sandbox, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Salesforce Sandbox](#create-a-salesforce-sandbox-test-user)**  – du har en motsvarighet för Britta Simon i Salesforce Sandbox som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Salesforce-Sandbox-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Salesforce Sandbox:**

1. I Azure-portalen på den **Salesforce Sandbox** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. På den **Salesforce Sandbox domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Salesforce Sandbox domän med enkel inloggning information](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    a. I den **inloggnings-URL** textruta Skriv det värde som använder följande mönster: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    b. I den **identifierare** textruta Skriv det värde som använder följande mönster: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`
    
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Salesforce klienten supportteamet](https://help.salesforce.com/support) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **certifikat** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_400.png)

6. På den **Salesforce Sandbox Configuration** klickar du på **konfigurera Salesforce Sandbox** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png) 

7. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce-Sandbox-administratörskonto.

8. Klicka på den **installationsprogrammet** under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-sandbox-tutorial/configure1.png)

9. Rulla ned till den **inställningar** i navigeringsfönstret klickar du på **identitet** att expandera avsnittet relaterade. Klicka på **inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Välj **SAML aktiverat**, och klicka sedan på **spara**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-enable-saml.png)

11. Om du vill konfigurera SAML enkel inloggning för, klickar du på **ny**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-admin-sso-new.png)

12. Utför följande steg på avsnittet SAML enkel inloggning inställningar:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-saml-config.png)

    a. I den **namn** textruta skriver du namnet på konfigurationen (till exempel: *SPSSOWAAD_Test*). 

    b. I den **utfärdaren** och klistra in värdet för fältet **SAML enhets-ID**, som du har kopierat från Azure-portalen

    c. I den **enhets-Id** textruta typen `https://<instancename>--Sandbox.<entityid>.my.salesforce.com` om det är den första Salesforce Sandbox-instans som du lägger till din katalog. Om du redan har lagt till en instans av Salesforce Sandbox sedan för den **enhets-ID** Skriv i den **logga URL**, som ska vara i formatet: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`  
 
    d. Att överföra den **providern identitetscertifikat**, klickar du på **Välj fil** att bläddra och välja den certifikatfil som du har hämtat från Azure-portalen.  

    e. Som **SAML identitetstyp**, väljer du något av följande alternativ:
    
      * Välj **Assertion innehåller användarens Salesforce användarnamn**om användarens Salesforce Username skickas i SAML-kontroll

      * Välj **Assertion innehåller Federation-ID från användarobjektet**om Federation-ID från användarobjektet skickas i SAML-kontroll

      * Välj **Assertion innehåller Använd ID från användarobjektet**om användar-ID från användarobjektet skickas i SAML-kontroll
 
    f. Som **SAML identitet plats**väljer **identitet är i elementet NameIdentifier i instruktionen ämne**.

    g. Som **providern initierade begära Tjänstbindning**väljer **HTTP POST**. 

    h. I **identitet providern inloggnings-URL** textruta klistra in värdet för **inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen. 

    i. SFDC har inte stöd för SAML logga ut.  Som en tillfällig lösning kan du klistra in `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` till den **identitet providern logga ut URL** textruta.

    j. Klicka på **Spara**.

### <a name="enable-your-domain"></a>Aktivera din domän
Det här avsnittet förutsätter att du redan har skapat en domän.  Mer information finns i [definierar domännamn](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

**Utför följande steg om du vill aktivera din domän:**

1. Klicka på det vänstra navigeringsfönstret i Salesforce **Företagsinställningar** Expandera avsnittet relaterade och klicka sedan på **min domän**.
   
     ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-my-domain.png)
   
   >[!NOTE]
   >Kontrollera att din domän har konfigurerats korrekt. 

2. I den **Autentiseringskonfiguration** klickar du på **redigera**, sedan som **Autentiseringstjänsten**, Välj namnet på SAML enkel inloggning inställningen från den tidigare avsnittet och klicka slutligen på **spara**.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-edit-auth-config.png)

Så snart du har en domän som har konfigurerats, bör användarna använda domän-URL för att logga in till Salesforce sandbox.  

Om du vill hämta värdet för URL: en, klickar du på SSO-profil som du har skapat i föregående avsnitt.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-salesforce-sandbox-test-user"></a>Skapa en testanvändare Salesforce Sandbox

I det här avsnittet skapas en användare som kallas Britta Simon i Salesforce Sandbox. Salesforce Sandbox stöder just-in-time-allokering som är aktiverad som standard.
Det finns ingen åtgärd objekt i det här avsnittet. Om en användare inte redan finns i Salesforce Sandbox, skapas en ny när du försöker komma åt Salesforce Sandbox.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Salesforce Sandbox.

![Tilldela rollen][200] 

**Om du vill tilldela Salesforce Sandbox Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Salesforce Sandbox**.

    ![Länken Salesforce Sandbox i listan med program](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Salesforce Sandbox på åtkomstpanelen du bör få automatiskt loggat in på ditt Salesforce-Sandbox-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_203.png

