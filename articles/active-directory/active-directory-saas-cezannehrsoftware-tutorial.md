---
title: 'Självstudier: Azure Active Directory-integrering med Cezanne HR programvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Cezanne HR programvaran och Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: b6ba41b31df8f950e37724903d3bca31358a598c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34343563"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Självstudier: Azure Active Directory-integrering med Cezanne HR programvara

I kursen får lära du att integrera Cezanne HR-program med Azure Active Directory (AD Azure).

Integrera Cezanne HR-program med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Cezanne HR programvara.
- Du kan aktivera användarna att automatiskt hämta loggat in på Cezanne HR-programvara (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Cezanne HR programvara, behöver du följande:

- En Azure AD-prenumeration
- En Cezanne HR programvara enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till Cezanne HR programvara från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Lägga till Cezanne HR programvara från galleriet
Du måste lägga till Cezanne HR programvara från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Cezanne HR-program i Azure AD.

**Utför följande steg för att lägga till Cezanne HR programvara från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Cezanne HR programvara**väljer **Cezanne HR programvara** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Cezanne HR-programvara i resultatlistan](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Cezanne HR programvara baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Cezanne HR programvara till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Cezanne HR programvara upprättas.

I Cezanne HR-program, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Cezanne HR programvara, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Cezanne HR programvara](#create-a-cezannehrsoftware-test-user)**  – du har en motsvarighet för Britta Simon Cezanne HR programvara som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Cezanne HR programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Cezanne HR programvara:**

1. I Azure-portalen på den **Cezanne HR programvara** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. På den **Cezanne HR programvara domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Cezanne HR programvara domän med enkel inloggning information](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. I den **inloggnings-URL** textruta anger du URL: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. I den **identifierare** textruta anger du URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. I den **Reply URL** textruta anger du URL: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och Reply-URL. Kontakta [Cezanne HR-Programvaruklienten supportteamet](https://cezannehr.com/services/support/) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)

6. På den **Cezanne HR programvarukonfiguration** klickar du på **konfigurera Cezanne HR programvara** att öppna **konfigurera inloggning** fönster.

    ![Cezanne HR konfiguration av programvara](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png)

7. Rulla ned till den **Snabbreferens** avsnitt. Kopiera den **SAML enkel inloggning URL: en och SAML enhets-ID** från den **Snabbreferens avsnitt.**

    ![Cezanne HR konfiguration av programvara](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure1.png)

8. I en annan webbläsarfönster inloggning till klienten Cezanne HR programvara som en administratör.

9. I det vänstra navigeringsfönstret klickar du på **systeminställningarna**. Gå till **säkerhetsinställningar**. Gå till **konfiguration för enkel inloggning**.

    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

10. I den **Tillåt användare att logga in med följande tjänst för enkel inloggning (SSO)** panelen, kontrollera den **SAML 2.0** och välj den **Advanced Configuration** alternativet.

    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

11. Klicka på **Lägg till ny** knappen.

    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

12. Utför följande steg på **SAML 2.0 IDENTITETSLEVERANTÖRER** avsnitt.

    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Ange namnet på din identitetsleverantör som den **visningsnamn**.

    b. I den **identifiering** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen. 

    c. Ändra den **SAML bindning** till ”POST”.

    d. I den **säkerhetstoken tjänstslutpunkten** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    e. Ange i textrutan användarnamn ID-attributet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Klicka på **överför** ikon för att ladda upp det hämta certifikatet från Azure-portalen.
    
    g. Klicka på knappen **OK**. 

13. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Skapa en testanvändare Cezanne HR programvara

För att aktivera Azure AD-användare att logga in på Cezanne HR programvara, måste de etableras i Cezanne HR programvara. När det gäller Cezanne HR-program är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1.  Logga in på webbplatsen för företagets Cezanne HR programvara som en administratör.

2.  I det vänstra navigeringsfönstret klickar du på **systeminställningarna**. Gå till **hantera användare**. Gå till **Lägg till nya användare**.

    ![Ny användare](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "ny användare")

3.  På **detaljer** avsnittet, utföra nedanstående steg:

    ![Ny användare](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "ny användare")
    
    a. Ange **intern användare** som OFF.
    
    b. I den **Förnamn** textruta Ange först namnet på användaren som **Britta**.  
 
    c. I den **efternamn** textruta typen efternamn för användaren som **Simon**.
    
    d. I den **e-post** textruta typen e-postadressen för användaren som Brittasimon@contoso.com.

4.  På **kontoinformation** avsnittet, utföra nedanstående steg:

    ![Ny användare](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "ny användare")
    
    a. I den **användarnamn** textruta, ange den e-posten för användare som Brittasimon@contoso.com.
    
    b. I den **lösenord** textruta skriver du lösenordet för användaren.
    
    c. Välj **HR Professional** som **säkerhetsroll**.
    
    d. Klicka på **OK**.

5. Gå till **enkel inloggning** fliken och markera **Lägg till ny** i den **SAML 2.0 identifierare** område.

    ![Användaren](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "användare")

6. Välj din identitetsleverantör för den **identitetsleverantör** och i textrutan för **användar-ID**, ange e-postadress Britta Simon konto.

    ![Användaren](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "användare")
    
7. Klicka på **spara** knappen.

    ![Användaren](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "användare")

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Cezanne HR programvara.

![Tilldela rollen][200] 

**Om du vill tilldela Cezanne HR programvara Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Cezanne HR programvara**.

    ![Länken Cezanne HR-program i listan med program](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Cezanne HR programvara på åtkomstpanelen du bör få automatiskt loggat in på ditt Cezanne HR-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png

