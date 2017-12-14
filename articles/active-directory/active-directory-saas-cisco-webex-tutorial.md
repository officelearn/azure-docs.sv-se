---
title: "Självstudier: Azure Active Directory-integrering med Cisco Webex | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco Webex."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory7
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 835b7c139fc466719489c6fd1986dcbf16de549f
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Självstudier: Azure Active Directory-integrering med Cisco Webex

I kursen får lära du att integrera Cisco Webex med Azure Active Directory (AD Azure).

Cisco Webex integrera med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Cisco Webex.
- Du kan aktivera användarna att automatiskt hämta loggat in på Cisco Webex (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Cisco Webex, behöver du följande:

- En Azure AD-prenumeration
- En Cisco-Webex enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Cisco Webex från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-cisco-webex-from-the-gallery"></a>Att lägga till Cisco Webex från galleriet
Du måste lägga till Cisco Webex från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Cisco Webex i Azure AD.

**Utför följande steg för att lägga till Cisco Webex från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Cisco Webex**väljer **Cisco Webex** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Cisco Webex i resultatlistan](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Cisco Webex baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Cisco Webex motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Cisco Webex upprättas.

I Cisco Webex, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Cisco Webex, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Cisco Webex](#create-a-cisco-webex-test-user)**  – du har en motsvarighet för Britta Simon i Cisco Webex som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Cisco Webex.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Cisco Webex:**

1. I Azure-portalen på den **Cisco Webex** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. På den **Cisco Webex domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Cisco Webex domän med enkel inloggning information](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<subdomain>.webex.com`

    b. I den **identifierare** textruta anger du URL:`http://www.webex.com`

    c. I den **Reply URL** textruta Skriv en URL med följande mönster:`https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska Reply URL och inloggnings-URL. Kontakta [Cisco Webex klient supportteamet](https://www.webex.co.in/support/support-overview.html) att hämta dessa värden. 

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_400.png)
    
6. På den **Cisco Webex Configuration** klickar du på **konfigurera Cisco Webex** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. I en annan webbläsarfönster loggar du in på webbplatsen Cisco Webex företag som administratör.

8. Klicka på menyn högst upp **Platsadministration**.

    ![Platsadministration](./media/active-directory-saas-cisco-webex-tutorial/ic777621.png "Platsadministration")

9. I den **Hantera plats** klickar du på **SSO Configuration**.
   
    ![Konfiguration av SSO](./media/active-directory-saas-cisco-webex-tutorial/ic777622.png "SSO-konfiguration")

10. I den **Federerad Web SSO-konfiguration** avsnittet, utför följande steg:
   
    ![Federerad enkel inloggning Configuration](./media/active-directory-saas-cisco-webex-tutorial/ic777623.png "federerad enkel inloggning konfiguration")  

    a. Från den **Federation-protokollet** väljer **SAML 2.0**.

    b. Som **SSO profil**väljer **SP Intiated**.

    c. Öppna din hämtat certifikat i anteckningar och kopiera innehållet i den.

    d. Klicka på **importera SAML Metadata**, och klistra in det kopierade innehållet i certifikatet.

    e. I den **utfärdaren för SAML (IdP-ID)** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen.

    f. I den **inloggnings-URL för kunden SSO-Service** textruta klistra in **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    g. Från den **NameID Format** väljer **e-postadress**.

    h. I den **AuthnContextClassRef** textruta typen **urn: oasis: namn: tc: SAML:2.0:ac:classes:Password**.

    Jag. I den **kunden URL för enkel inloggning logga ut** textruta klistra in **Sign-Out URL** som du har kopierat från Azure-portalen.
   
    j. Klicka på **uppdatering**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-cisco-webex-test-user"></a>Skapa en Cisco-Webex testanvändare

Om du vill aktivera Azure AD-användare kan logga in på Cisco Webex etableras de i Cisco Webex. Cisco Webex är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **Cisco Webex** klient.

2. Gå till **hantera användare \> lägga till användare**.
   
    ![Lägg till användare](./media/active-directory-saas-cisco-webex-tutorial/ic777625.png "lägga till användare")

3. Utför följande steg på avsnittet Lägg till användare:
   
    ![Lägg till användare](./media/active-directory-saas-cisco-webex-tutorial/ic777626.png "Lägg till användare")   

    a. Som **kontotyp**väljer **värden**.

    b. I den **Förnamn** textruta, ange först namnet på användaren som Britta.

    c. I den **efternamn** textruta anger efternamn för användaren som Simon.

    d. I den **användarnamn** textruta, ange den e-posten för användare som Brittasimon@contoso.com.

    e. I den **e-post** textruta typen e-postadressen för användaren som Brittasimon@contoso.com.

    f. I den **lösenord** textruta skriver du lösenordet för användaren.

    g. I den **Bekräfta** lösenordsrutan, ange lösenordet för användaren.

    h. Klicka på **Lägg till**.

>[!NOTE]
>Du kan använda något annat Cisco Webex användarens konto skapas verktyg eller API: er som tillhandahålls av Cisco Webex etablera AAD-användarkonton. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Cisco Webex.

![Tilldela rollen][200] 

**Om du vill tilldela Cisco Webex Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Cisco Webex**.

    ![Cisco Webex länken i listan med program](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Cisco Webex på åtkomstpanelen du bör få automatiskt loggat in på ditt Cisco Webex-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_203.png

