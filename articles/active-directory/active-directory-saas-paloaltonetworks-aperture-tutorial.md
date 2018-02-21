---
title: "Självstudier: Azure Active Directory-integrering med Palo Alto nätverk - öppning | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Palo Alto nätverk - öppning."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 75633cbf13756b4b2be3e4be055b12021cc396d2
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Självstudier: Azure Active Directory-integrering med Palo Alto nätverk - öppning

I kursen får du lära dig hur du integrerar Palo Alto - öppning med Azure Active Directory (AD Azure).

Integrera Palo Alto nätverk - öppning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Palo Alto nätverk - öppning.
- Du kan aktivera användarna att automatiskt hämta loggat in på Palo Alto nätverk - öppning (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Palo Alto nätverk - öppning, behöver du följande:

- En Azure AD-prenumeration
- En Palo Alto nätverk – öppning enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till nätverk Palo Alto - öppning från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Lägga till nätverk Palo Alto - öppning från galleriet
Du måste lägga till Palo Alto nätverk - öppning från galleriet i listan över hanterade SaaS-appar för att konfigurera nätverk Palo Alto - öppning till Azure AD-integrering.

**Utför följande steg för att lägga till Palo Alto nätverk - öppning från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Palo Alto nätverk - öppning**väljer **Palo Alto nätverk - öppning** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Palo Alto nätverk - öppning i resultatlistan](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Palo Alto nätverk - öppning baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Palo Alto nätverk - öppning motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Palo Alto nätverk - öppning måste upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Palo Alto nätverk - öppning, måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa ett nätverk för Palo Alto - öppning testanvändare](#create-a-palo-alto-networks---aperture-test-user)**  – du har en motsvarighet för Britta Simon i Palo Alto nätverk - öppning som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i nätverket Palo Alto - öppning program.

**Om du vill konfigurera Azure AD utför enkel inloggning med Palo Alto nätverk - öppning, du följande steg:**

1. I Azure-portalen på den **Palo Alto nätverk - öppning** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_samlbase.png)

3. På den **Palo Alto nätverk - URL: er och öppning domän** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Palo Alto nätverk - URL: er och öppning domän med enkel inloggning information](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Palo Alto nätverk - URL: er och öppning domän med enkel inloggning information](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Kontakta [Palo Alto nätverk - öppning klienten supportteamet](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) att hämta dessa värden. 

5. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_400.png)


7. På den **Palo Alto nätverk - öppning Configuration** klickar du på **konfigurera Palo Alto nätverk - öppning** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigureringslänken](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_configure.png)

8. I en annan webbläsarfönstret, inloggning till Palo Alto nätverk - öppning som administratör.

9. Klicka på översta verktygsfältet och **inställningar**.

    ![Inställningsfliken](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

10. Gå till **programmet** Klicka på avsnittet **autentisering** formuläret till vänster på menyn.

    ![Fliken Auth](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
11. På den **autentisering** sidan utför följande steg:
    
    ![Fliken autentisering](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Kontrollera den **aktivera enkel inloggning-On(Supported SSP Providers are Okta, Onelogin)** från **enkel inloggning** fältet.

    b. I den **identitet Provider-ID** textruta klistra in värdet för **SAML enhets-ID**, som du har kopierat från Azure-portalen.

    c. Klicka på **Välj fil** att ladda upp det hämtade certifikatet från Azure AD i den **providern identitetscertifikat** fältet.

    d. I den **identitet providern SSO URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.

    e. Granska IdP-informationen från **öppning Info** avsnittet och hämta certifikatet från **öppning nyckeln** fältet.

    f. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-palo-alto-networks---aperture-test-user"></a>Skapa ett nätverk för Palo Alto - öppning testanvändare

I det här avsnittet kan du skapa en användare som kallas Britta Simon i Palo Alto nätverk - öppning. Arbeta med [Palo Alto nätverk - öppning klienten supportteamet](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) att lägga till användare i Palo Alto-nätverk – öppning plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Palo Alto nätverk - öppning.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Palo Alto nätverk - öppning, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Palo Alto nätverk - öppning**.

    ![Palo Alto-nätverk – öppning länken i listan med program](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på Palo Alto-nätverk – öppning panelen på panelen åtkomst du ska hämta automatiskt loggat in på nätverket Palo Alto - öppning program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_203.png

