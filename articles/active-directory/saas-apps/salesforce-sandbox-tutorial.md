---
title: 'Självstudier: Azure Active Directory-integration med Salesforce Sandbox | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.openlocfilehash: 6feafba41cf65a752dd5bf0819b0b93bacff0aff
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "42054131"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Självstudier: Azure Active Directory-integrering med Salesforce begränsat läge

I den här självstudien får du lära dig hur du integrerar Salesforce Sandbox med Azure Active Directory (AD Azure).

Sandboxar ger dig möjlighet att skapa flera kopior av din organisation i separata miljöer för olika syften, till exempel utveckling, testning och utbildning, utan att kompromissa med data och program i din Salesforce-produktion organisation.
Mer information finns i [Sandbox översikt](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

Integrera Salesforce Sandbox med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Salesforce Sandbox.
- Du kan aktivera användarna att automatiskt få loggat in på Salesforce begränsat läge (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Salesforce begränsat läge, behöver du följande objekt:

- En Azure AD-prenumeration
- En Salesforce-Sandbox enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Salesforce Sandbox från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Att lägga till Salesforce Sandbox från galleriet

Om du vill konfigurera integreringen av Salesforce begränsat till Azure AD, som du behöver lägga till Salesforce Sandbox från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Salesforce Sandbox från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Salesforce Sandbox**väljer **Salesforce Sandbox** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Salesforce-Sandbox i resultatlistan](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Salesforce Sandbox baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Salesforce begränsat till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Salesforce Sandbox upprättas.

I Salesforce Sandbox tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Salesforce begränsat läge, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare i Salesforce Sandbox](#create-a-salesforce-sandbox-test-user)**  – du har en motsvarighet för Britta Simon i Salesforce-Sandbox som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Salesforce-Sandbox-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Salesforce begränsat läge:**

1. I Azure-portalen på den **Salesforce Sandbox** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. På den **Salesforce Sandbox-domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

   ![Salesforce Sandbox-domän och URL: er med enkel inloggning för information](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

   I den **svars-URL** textrutan skriver din organisation specifika **svars-URL**.

   > [!NOTE]
   > Uppdatera svars-URL-värdet med faktiska svars-URL som beskrivs senare i den här självstudien.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(RAW)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/salesforce-sandbox-tutorial/tutorial_general_400.png)

6. På den **Salesforce Sandbox Configuration** klickar du på **konfigurera Salesforce Sandbox** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png)

7. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce-Sandbox-administratörskonto.

8. Klicka på den **installationsprogrammet** under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure1.png)

9. Rulla ned till den **inställningar** i det vänstra navigeringsfönstret klickar du på **identitet** att expandera avsnittet relaterade. Klicka sedan på **inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. På den **inställningar för enkel inloggning** klickar du på den **redigera** knappen.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure3.png)

11. Välj **SAML aktiverat**, och klicka sedan på **spara**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. Om du vill konfigurera SAML enkel inloggning för, klickar du på **New**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. På den **inställningar för enkel inloggning** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-saml-config1.png)

    a. Välj **SAML aktiverat** kryssrutan.

    b. I den **utfärdare** fältet, klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure-portalen.

    c. Ladda upp den **providern identitetscertifikat**, klickar du på **Bläddra** att bläddra och välja den certifikatfil som du har hämtat från Azure-portalen.

    d. I **inloggnings-URL för identitetsprovider** textrutan klistra in värdet för **enkel inloggnings-URL för**, som du har kopierat från Azure-portalen.

    e. Som **SAML identitetstypen**, väljer du något av följande alternativ:

      * Välj **Assertion innehåller användarens Salesforce användarnamn**om användarens Salesforce Username skickas i SAML-försäkran

      * Välj **Assertion innehåller Federation-ID från användarobjektet**om Federation-ID från användarobjektet skickas i SAML-försäkran
  
    f. Som **SAML identitet plats**väljer **identiteten är en attributelement**.

    g. SFDC stöder inte SAML-utloggning.  Som en lösning kan du klistra in `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` till den **anpassad URL för utloggning** textrutan.

    h. Klicka på **Spara**.

14. På den **inställningar för enkel inloggning** klickar du på den **hämta Metadata** knappen.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure4.png)

15. Öppna den hämtade Metadata i ett annat webbläsarfönster och kopiera den **plats** värde och klistra in den i den **svars-URL** textrutan på den **Salesforce Sandbox-domän och URL: er**avsnitt i Azure-portalen.  

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure5.png)

16. Om du vill konfigurera programmet i **SP** initierad läge är förutsättningarna för som:

    a. Du bör ha en verifierad domän.

    b. Du måste du konfigurera och aktivera din domän på Salesforce Sandbox beskrivs stegen för detta senare i den här självstudien.

    c. I Azure-portalen på den **Salesforce Sandbox-domän och URL: er** avsnittet, kontrollera **visa avancerade URL-inställningar** och utföra följande steg:
  
    ![Salesforce Sandbox-domän och URL: er med enkel inloggning för information](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    I den **inloggnings-URL** textrutan skriver du värdet med följande mönster: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Det här värdet ska kopieras från Salesforce Sandbox-portalen när du har aktiverat domänen.

17. På den **SAML-signeringscertifikat** klickar du på **Certificate(RAW)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/salesforce-sandbox-tutorial/tutorial_general_400.png)

19. På den **Salesforce Sandbox Configuration** klickar du på **konfigurera Salesforce Sandbox** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png)

20. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce-Sandbox-administratörskonto.

21. Klicka på den **installationsprogrammet** under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure1.png)

22. Rulla ned till den **inställningar** i det vänstra navigeringsfönstret klickar du på **identitet** att expandera avsnittet relaterade. Klicka sedan på **inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

23. På den **inställningar för enkel inloggning** klickar du på den **redigera** knappen.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure3.png)

24. Välj **SAML aktiverat**, och klicka sedan på **spara**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

25. Om du vill konfigurera SAML enkel inloggning för, klickar du på **New**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

26. Om du lägger till en andra instans, måste du aktivera en domän som nämnts ovan (SP-initierat fall). Utför följande steg i avsnittet SAML enkel inloggning inställningar:

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

    a. I den **namn** textrutan skriver du namnet på konfigurationen (till exempel: *SPSSOWAAD_Test*).

    b. I den **utfärdare** fältet, klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure-portalen.

    c. I den **entitets-ID** textrutan Använd `https://test.salesforce.com` värdet för den första instansen och du kan använda klient-specifikt ID-värde från andra instans av programmet.

    d. Ladda upp den **providern identitetscertifikat**, klickar du på **Välj fil** att bläddra och välja den certifikatfil som du har hämtat från Azure-portalen.

    e. Som **SAML identitetstypen**, väljer du något av följande alternativ:

      * Välj **Assertion innehåller användarens Salesforce användarnamn**om användarens Salesforce Username skickas i SAML-försäkran

      * Välj **Assertion innehåller Federation-ID från användarobjektet**om Federation-ID från användarobjektet skickas i SAML-försäkran

      * Välj **Assertion innehåller ID för användning från användarobjektet**om användar-ID från användarobjektet skickas i SAML-försäkran

    f. Som **SAML identitet plats**väljer **identitet är i NameIdentifier-elementet i instruktionen ämne**.

    g. Som **providern initierade begära Tjänstbindning**väljer **HTTP POST**.

    h. I **inloggnings-URL för identitetsprovider** textrutan klistra in värdet för **enkel inloggnings-URL för**, som du har kopierat från Azure-portalen.

    i. SFDC stöder inte SAML-utloggning.  Som en lösning kan du klistra in `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` den i den **anpassad URL för utloggning** textrutan.

    j. Klicka på **Spara**.

27. Om du vill aktivera din domän på Salesforce sandbox-miljön, utför du följande steg:

    > [!NOTE]
    > Innan du aktiverar domänen måste du skapa samma på Salesforce Sandbox. Mer information finns i [definiera domännamn](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). När domänen har skapats, kontrollera att den är korrekt konfigurerad.

    * Klicka på det vänstra navigeringsfönstret i Salesforce Sandbox **Företagsinställningar** Expandera avsnittet relaterade och klicka sedan på **min domän**.

         ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

    * I den **Autentiseringskonfiguration** klickar du på **redigera**.

        ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

    * I den **Autentiseringskonfiguration** avsnittet som **autentiseringstjänst**, Välj namnet på SAML enkel inloggning inställningen som du har angett under konfigurationen för enkel inloggning i Salesforce sandbox-miljön och Klicka på **spara**.

        ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/salesforce-sandbox-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/salesforce-sandbox-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/salesforce-sandbox-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-salesforce-sandbox-test-user"></a>Skapa en testanvändare i Salesforce Sandbox

I det här avsnittet skapas en användare som kallas Britta Simon i Salesforce Sandbox. Salesforce-Sandbox stöder just-in-time-etablering, som är aktiverat som standard. Det finns inga uppgift åt dig i det här avsnittet. Om en användare inte redan finns i Salesforce Sandbox, skapas en ny när du försöker få åtkomst till Salesforce Sandbox. Salesforce-Sandbox stöder även automatisk användaretablering, kan du hitta mer information om [här](salesforce-sandbox-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Salesforce Sandbox.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Salesforce sandbox-miljön, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Salesforce Sandbox**.

    ![Salesforce-Sandbox-länk i listan med program](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Salesforce Sandbox i åtkomstpanelen du bör få automatiskt loggat in på ditt Salesforce-Sandbox-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](salesforce-sandbox-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png