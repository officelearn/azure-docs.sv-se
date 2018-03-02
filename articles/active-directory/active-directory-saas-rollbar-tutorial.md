---
title: "Självstudier: Azure Active Directory-integrering med Rollbar | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Rollbar."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/04/2017
ms.author: jeedes
ms.openlocfilehash: 43dc50d0a5381ace8bcfeb3cae39e249ba743876
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Självstudier: Azure Active Directory-integrering med Rollbar

I kursen får lära du att integrera Rollbar med Azure Active Directory (AD Azure).

Integrera Rollbar med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Rollbar.
- Du kan aktivera användarna att automatiskt hämta loggat in på Rollbar (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Rollbar, behöver du följande:

- En Azure AD-prenumeration
- En Rollbar enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Rollbar från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-rollbar-from-the-gallery"></a>Att lägga till Rollbar från galleriet
Du måste lägga till Rollbar från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Rollbar i Azure AD.

**Utför följande steg för att lägga till Rollbar från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Rollbar**väljer **Rollbar** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Rollbar i resultatlistan](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Rollbar baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Rollbar motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Rollbar upprättas.

I Rollbar, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Rollbar, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Rollbar](#create-a-rollbar-test-user)**  – du har en motsvarighet för Britta Simon i Rollbar som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Rollbar program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Rollbar:**

1. I Azure-portalen på den **Rollbar** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_samlbase.png)

3. På den **Rollbar domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Rollbar domän med enkel inloggning information](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_url.png)

    a. I den **identifierare** textruta anger du URL: `https://saml.rollbar.com`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://rollbar.com/<accountname>/saml/sso/azure/`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Rollbar domän med enkel inloggning information](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://rollbar.com/<accountname>/saml/login/azure/`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska Reply URL och inloggnings-URL. Kontakta [Rollbar klienten supportteamet](mailto:support@rollbar.com) att hämta dessa värden. 

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-rollbar-tutorial/tutorial_general_400.png)
    
7. I en annan webbläsarfönster loggar du in på webbplatsen Rollbar företag som administratör.

8. Klicka på den **profilinställningar** i övre högra hörnet och klicka sedan på **kontonamn inställningar**.
    
    ![Konfiguration](./media/active-directory-saas-rollbar-tutorial/general.png)

9. Klicka på **identitetsleverantör** under säkerhet.

    ![Konfiguration](./media/active-directory-saas-rollbar-tutorial/configure1.png)

10. I den **SAML-identitetsprovider** avsnittet, utför följande steg:
    
    ![Konfiguration](./media/active-directory-saas-rollbar-tutorial/configure2.png)

    a. Välj **AZURE** från den **SAML-identitetsprovider** listrutan.

    b. Öppna metadatafilen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **SAML Metadata** textruta.

    c. Klicka på **Spara**.

11. När du klickar på Spara knappen skärmen blir så här:
    
    ![Konfiguration](./media/active-directory-saas-rollbar-tutorial/configure3.png)
    > [!NOTE] 
    > För att kunna slutföra följande steg måste du först lägga till dig själv som en användare Rollbar app i Azure.
    a. Om du vill kräva att alla användare att autentisera via Azure och klicka sedan på **logga in via din identitetsleverantör** återautentisera via Azure.  

    b.  När du kommer tillbaka till skärmen, välja den **kräver inloggning via SAML-identitetsprovider** kryssrutan.

    b. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-rollbar-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-rollbar-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-rollbar-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-rollbar-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-rollbar-test-user"></a>Skapa en testanvändare Rollbar

Om du vill aktivera Azure AD-användare kan logga in på Rollbar etableras de i Rollbar. När det gäller Rollbar är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på webbplatsen Rollbar företag som administratör.

2. Klicka på den **profilinställningar** i övre högra hörnet och klicka sedan på **kontonamn inställningar**.

    ![Användare](./media/active-directory-saas-rollbar-tutorial/general.png)

3. Klicka på **användare**.
    
    ![Lägga till medarbetare](./media/active-directory-saas-rollbar-tutorial/user1.png)

4. Klicka på **bjuda in gruppmedlemmarna**.

    ![Bjud in personer](./media/active-directory-saas-rollbar-tutorial/user2.png)

5. I textrutan, anger du namnet på användaren som  **brittasimon@contoso.com**  och klicka på **Lägg till/inbjudan**.

    ![Bjud in personer](./media/active-directory-saas-rollbar-tutorial/user3.png)

6. Användaren får en inbjudan och när accepteras han eller hon har skapat i systemet.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Rollbar.

![Tilldela rollen][200] 

**Om du vill tilldela Rollbar Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Rollbar**.

    ![Länken Rollbar i listan med program](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Rollbar på åtkomstpanelen du bör få automatiskt loggat in på ditt Rollbar program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_203.png

