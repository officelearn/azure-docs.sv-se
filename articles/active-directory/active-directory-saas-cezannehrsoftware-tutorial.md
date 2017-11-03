---
title: "Självstudier: Azure Active Directory-integrering med Cezanne HR programvara | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Cezanne HR-programvaran och Azure Active Directory."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: fb8f95bf-c3c1-4e1f-b2b3-3b67526be72d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 623c438edfce5f98c2d32d8bb25a97d86aa77909
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-integrate-azure-active-directory-with-cezanne-hr-software"></a>Självstudier: Integrera Azure Active Directory med Cezanne HR-programvara

I kursen får lära du att integrera Cezanne HR-program med Azure Active Directory (AD Azure).

Integrera Cezanne HR-program med Azure AD ger dig följande fördelar. Du kan:

- Kontrollera i Azure AD som har åtkomst till Cezanne HR-programvara.
- Ge användarna logga in automatiskt Cezanne HR-program med enkel inloggning (SSO) med sina Azure AD-konton.
- Hantera dina konton i en central plats: Azure-portalen.

Läs mer om programvara som en tjänst (SaaS) appintegrering med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Cezanne HR programvara, behöver du följande:

- En Azure AD-prenumeration
- Cezanne HR-programvara SSO-aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudiekursen, rekommenderar vi att du inte använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD SSO i en testmiljö. 

Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

* Lägga till Cezanne HR programvara från galleriet
* Konfigurera och testa Azure AD-SSO

## <a name="add-cezanne-hr-software-from-the-gallery"></a>Lägg till Cezanne HR programvara från galleriet
Lägg till Cezanne HR programvara från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Cezanne HR-program i Azure AD.

Lägg till Cezanne HR programvara från galleriet genom att göra följande:

1. I den  **[Azure-portalen](https://portal.azure.com)**, i den vänstra rutan, Välj den **Azure Active Directory** knappen. 

    ![Knappen ”Azure Active Directory”][1]

2. Välj **företagsprogram** > **alla program**.

    ![Länken ”alla program”][2]
    
3. Att lägga till ett nytt program överst i den **alla program** dialogrutan **nytt program**.

    ![”Det nya programmet” knappen][3]

4. I sökrutan skriver **Cezanne HR programvara**.

    ![Sökrutan](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_search.png)

5. Välj i resultatlistan **Cezanne HR programvara** och välj sedan den **Lägg till** för att lägga till programmet.

    ![Resultatlistan](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD SSO med Cezanne HR programvara baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, Azure AD som behöver veta Cezanne HR programvarumotsvarighet till Azure AD-användare. Med andra ord måste du upprätta en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Cezanne HR-programvara.

Tilldela Cezanne HR-programvara för att upprätta länken relationen, **användarnamn** värde som Azure AD **användarnamn** värde.

Slutför följande byggblock för att konfigurera och testa Azure AD SSO med Cezanne HR-programvara.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD för enkel inloggning

I det här avsnittet kan du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Cezanne HR programmet genom att göra följande:

1. I Azure-portalen på den **Cezanne HR programvara** programmet integration anger **enkel inloggning**.

    ![Kommandot ”enkel inloggning”][4]

2. Att aktivera enkel inloggning, i den **enkel inloggning** dialogrutan markerar du den **läge** som **SAML-baserade inloggning**.
 
    ![Rutan ”läge”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. Under **Cezanne HR programvara domän och URL: er**, gör du följande:

    ![Avsnittet ”Cezanne HR programvara domän och URL: er”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. I den **inloggnings-URL** skriver en URL som har följande syntax:`https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`

    b. I den **Reply URL** skriver en URL som har följande syntax:`https://w3.cezanneondemand.com:443/<tenantid>`    
     
    > [!NOTE] 
    > Föregående värden är inte verkliga. Uppdatera dem med faktiska reply-Webbadressen och Webbadressen för inloggning. För att få värdena kan kontakta den [Cezanne HR programvara klienten supportteamet](mailto:info@cezannehr.com).

4. Under **SAML-signeringscertifikat**väljer **certifikat (Base64)**, och sedan spara certifikatfilen på datorn.

    ![Avsnittet ”SAML signeringscertifikat”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Välj **Spara**.

    ![Knappen ”Spara”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)
    
6. Under **Cezanne HR programvarukonfiguration**väljer **konfigurera Cezanne HR programvara** att öppna den **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID** och **SAML enkel inloggning** URL från den **Snabbreferens** avsnitt.

    ![I avsnittet ”Cezanne HR programvarukonfiguration”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png) 

7. Logga in på din Cezanne HR-klient för programvara som en administratör i en annan webbläsarfönster.

8. I den vänstra rutan, Välj **systeminställningarna**. Välj **säkerhetsinställningar** > **enkel inloggning Configuration**.

    ![Länken ”enkel inloggning-konfiguration”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

9. I den **Tillåt användare att logga in med följande tjänster för enkel inloggning (SSO)** väljer den **SAML 2.0** kryssrutan och välj den **Advanced Configuration** alternativet.

    ![Enkel inloggning services-alternativ](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

10. Välj **lägga till nya**.

    ![Knappen ”Lägg till ny”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

11. Under **SAML 2.0 identitetsleverantörer**, gör du följande:

    ![Avsnittet ”identitetsleverantörer SAML 2.0”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. I den **visningsnamn** anger du namnet på din identitetsleverantör.

    b. I den **identifiering** klistra in den **SAML enhets-ID** som du kopierade från Azure-portalen. 

    c. I den **SAML bindning** väljer **efter**.

    d. I den **säkerhetstoken tjänstslutpunkten** klistra in den **SAML enkel inloggning** URL som du kopierade från Azure-portalen. 
    
    e. I den **användarnamn ID-attributet** ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Ladda upp det hämta certifikatet från Azure AD, Välj den **överför** knappen.
    
    g. Välj **OK**. 

12. Välj **Spara**.

    ![Knappen ”Spara”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> När du skapar appen kan du läsa en kortare version av föregående anvisningarna i den [Azure-portalen](https://portal.azure.com). När du har lagt till appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken. Komma åt inbäddade dokumentationen från den **Configuration** avsnitt. 

Mer information om funktionen inbäddade dokumentation finns [Azure AD inbäddade dokumentationen]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
I det här avsnittet skapar du testanvändare Britta Simon i Azure-portalen.

![Testanvändare Britta Simon][100]

Om du vill skapa en testanvändare i Azure AD, gör du följande:

1. I den **Azure-portalen**, i den vänstra rutan, Välj den **Azure Active Directory** knappen.

    ![Knappen ”Azure Active Directory”](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, Välj **användare och grupper** > **alla användare**.
    
    ![Länken ”alla användare”](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 
    
    Den **alla användare** öppnas.

3. Öppna den **användare** dialogrutan **Lägg till**.
 
    ![Knappen ”Lägg till”](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. I den **användaren** dialogrutan Gör följande:
 
    ![Dialogrutan ”användare”](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användaren Britta Simon **e-postadress**.

    c. Välj den **visa lösenordet** och anteckna värdet som har genererats i den **lösenord** rutan.

    d. Välj **Skapa**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Skapa en testanvändare för Cezanne HR-programvara

Om du vill aktivera Azure AD-användare att logga in på Cezanne HR programvara, måste de etableras i Cezanne HR programvara. När det gäller Cezanne HR-program är etablering en manuell aktivitet.

Konfigurera ett användarkonto genom att göra följande:

1.  Logga in på webbplatsen Cezanne HR programvara företag som administratör.

2.  I den vänstra rutan, Välj **systeminställningarna** > **hantera användare** > **Lägg till nya användare**.

    ![Länken ”Lägg till nya användare”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "ny användare")

3.  Under **detaljer**, gör du följande:

    ![Avsnittet ”Person information”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "ny användare")
    
    a. Ange **intern användare** som **OFF**.
    
    b. I den **Förnamn** Skriv användarens förnamn, till exempel **Britta**.  
 
    c. I den **efternamn** Skriv användarens efternamn, till exempel **Simon**.
    
    d. I den **e-post** Skriv användarens e-postadress, till exempel Brittasimon@contoso.com.

4.  Under **kontoinformation**, gör du följande:

    ![Avsnittet ”kontoinformation”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "ny användare")
    
    a. I den **användarnamn** Skriv användarens e-postadress, till exempel Brittasimon@contoso.com.
    
    b. I den **lösenord** Skriv användarens lösenord.
    
    c. I den **säkerhetsroll** väljer **HR Professional**.
    
    d. Välj **OK**.

5. På den **enkel inloggning** fliken den **SAML 2.0 identifierare** väljer **Lägg till ny**.

    ![Knappen ”Lägg till ny”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "användare")

6. I den **identitetsleverantör** Välj identitetsprovider. I den **användar-ID** ange e-postadressen för test användaren Britta Simons konto.

    ![Rutorna ”identitetsleverantör” och ”User ID”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "användare")
    
7. Välj **Spara**.

    ![Knappen ”Spara”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "användare")

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera testanvändare Britta Simon att använda Azure SSO genom att bevilja åtkomst till Cezanne HR-programvara.

![Testa användaråtkomst][200] 

1. Öppna program och sedan gå till vyn katalog i Azure-portalen. Välj **företagsprogram** > **alla program**.

    ![Länken ”alla program”][201] 

2. Välj i listan med program **Cezanne HR programvara**.

    ![Listan med ”program”](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png) 

3. Välj på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Välj **Lägg till**. I den **Lägg uppdrag** dialogrutan **användare och grupper**.

    ![”Användare och grupper” länk][203]

5. I den **användare och grupper** i dialogrutan den **användare** väljer **Britta Simon**.

6. I den **användare och grupper** dialogrutan **Välj**.

7. I den **Lägg uppdrag** dialogrutan **tilldela**.
    
### <a name="test-sso"></a>Testa enkel inloggning

I det här avsnittet testa du konfigurationen av Azure AD SSO med hjälp av åtkomstpanelen.

När du väljer panelen Cezanne HR programvara på åtkomstpanelen kan logga du in automatiskt i tillämpningsprogrammet Cezanne HR-programvara.

## <a name="next-steps"></a>Nästa steg

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

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

