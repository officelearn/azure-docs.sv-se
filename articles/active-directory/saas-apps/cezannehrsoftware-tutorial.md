---
title: 'Självstudier: Azure Active Directory-integrering med Cezanne HR-program | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cezanne HR-programvara.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 7515d1d88e3652ba609286b1805e58853763434d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55183243"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Självstudier: Azure Active Directory-integrering med Cezanne HR-program

I den här självstudien får du lära dig hur du integrerar Cezanne HR-program med Azure Active Directory (AD Azure).

Integrera Cezanne HR-program med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Cezanne HR-program.
- Du kan aktivera användarna att automatiskt få loggat in på Cezanne HR-program (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Cezanne HR-program, behöver du följande objekt:

- En Azure AD-prenumeration
- En Cezanne HR programvara enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägga till Cezanne HR programvara från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Lägga till Cezanne HR programvara från galleriet
För att konfigurera integrering av Cezanne HR-program i Azure AD, som du behöver lägga till Cezanne HR-program från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Cezanne HR-program från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Cezanne HR programvara**väljer **Cezanne HR programvara** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Cezanne HR-programvara i resultatlistan](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Cezanne HR-program baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Cezanne HR programvara till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Cezanne HR programvara upprättas.

I Cezanne HR-programvara, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Cezanne HR-program, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Cezanne HR programvara](#create-a-cezannehrsoftware-test-user)**  – du har en motsvarighet för Britta Simon i Cezanne HR-programvara som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktiverar Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din Cezanne HR-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Cezanne HR-program:**

1. I Azure-portalen på den **Cezanne HR programvara** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

1. På den **Cezanne HR programvara domän och URL: er** avsnittet, utför följande steg:

    ![Cezanne HR programvara domän och URL: er med enkel inloggning för information](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. I den **inloggnings-URL** textrutan anger du URL: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. I den **identifierare** textrutan anger du URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. I den **svars-URL** textrutan anger du URL: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med en faktisk inloggnings-URL och svars-URL. Kontakta [Cezanne HR-klientprogrammet supportteamet](https://cezannehr.com/services/support/) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/cezannehrsoftware-tutorial/tutorial_general_400.png)

1. På den **Cezanne HR programvarukonfiguration** klickar du på **konfigurera Cezanne HR programvara** att öppna **konfigurera inloggning** fönster.

    ![Cezanne HR programvarukonfiguration](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png)

1. Rulla ned till den **Snabbreferens** avsnittet. Kopiera den **SAML enkel inloggnings-URL: en och SAML entitets-ID** från den **Snabbreferens avsnittet.**

    ![Cezanne HR programvarukonfiguration](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure1.png)

1. I ett annat webbläsarfönster inloggning till klienten Cezanne HR-program som administratör.

1. I det vänstra navigeringsfönstret klickar du på **systeminställningarna**. Gå till **säkerhetsinställningar**. Gå sedan till **konfigurationen för enkel inloggning**.

    ![Konfigurera enkel inloggning på App-sida](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

1. I den **Tillåt användare att logga in med följande tjänst för enkel inloggning (SSO)** panelen, kontrollera den **SAML 2.0** rutan och välj den **Advanced Configuration** alternativet.

    ![Konfigurera enkel inloggning på App-sida](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

1. Klicka på **Lägg till ny** knappen.

    ![Konfigurera enkel inloggning på App-sida](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

1. Utför följande steg på **SAML 2.0-IDENTITETSPROVIDERS** avsnittet.

    ![Konfigurera enkel inloggning på App-sida](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Ange namnet på din identitetsprovider som den **visningsnamn**.

    b. I den **entitetsidentifierare** textrutan klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen. 

    c. Ändra den **SAML-bindning** till ”POST”.

    d. I den **Security Token tjänstslutpunkt** textrutan klistra in värdet för **SAML enkel inloggnings-URL för** som du har kopierat från Azure-portalen.

    e. Ange i textrutan användarnamn ID attributet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Klicka på **överför** ikon för att ladda upp det nedladdade certifikatet från Azure-portalen.
    
    g. Klicka på knappen **OK**. 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning på App-sida](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/cezannehrsoftware-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/cezannehrsoftware-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/cezannehrsoftware-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/cezannehrsoftware-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Skapa en testanvändare Cezanne HR-program

För att aktivera Azure AD-användare att logga in på Cezanne HR-program, måste de etableras till Cezanne HR-program. När det gäller Cezanne HR-programvara är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1.  Logga in på webbplatsen för företagets Cezanne HR-program som administratör.

1.  I det vänstra navigeringsfönstret klickar du på **systeminställningarna**. Gå till **hantera användare**. Gå sedan till **Lägg till ny användare**.

    ![Ny användare](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Ny användare")

1.  På **Person information** avsnittet, utföra stegen nedan:

    ![Ny användare](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Ny användare")
    
    a. Ange **intern användare** som OFF.
    
    b. I den **Förnamn** textrutan Ange först namnet på användaren som **Britta**.  
 
    c. I den **efternamn** textrutan typ efternamn för användaren som **Simon**.
    
    d. I den **e** textrutan typ e-postadressen för användaren som Brittasimon@contoso.com.

1.  På **kontoinformation** avsnittet, utföra stegen nedan:

    ![Ny användare](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Ny användare")
    
    a. Skriv e-postadressen för användaren i textrutan **Användarnamn** som Brittasimon@contoso.com.
    
    b. I textrutan **Password** (Lösenord) skriver du lösenordet för användaren.
    
    c. Välj **HR Professional** som **säkerhetsroll**.
    
    d. Klicka på **OK**.

1. Gå till **enkel inloggning** fliken och markera **Lägg till ny** i den **SAML 2.0 identifierare** området.

    ![Användare](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Användare")

1. Välj din identitetsprovider för den **identitetsprovider** och i textrutan i **användaridentifierare**, ange den e-postadressen för Britta Simon konto.

    ![Användare](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Användare")
    
1. Klicka på knappen **Spara**.

    ![Användare](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Användare")

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Cezanne HR-program.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Cezanne HR programvara, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Cezanne HR programvara**.

    ![Länken Cezanne HR-program i listan med program](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Cezanne HR-program i åtkomstpanelen du bör få automatiskt loggat in på ditt Cezanne HR-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/cezannehrsoftware-tutorial/tutorial_general_203.png

