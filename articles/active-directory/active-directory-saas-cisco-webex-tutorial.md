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
ms.openlocfilehash: 42632dcf8997ec5e987ac8a6615aae24e903399a
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Självstudier: Azure Active Directory-integrering med Cisco Webex

I kursen får lära du att integrera Cisco Webex med Azure Active Directory (AD Azure).

Cisco Webex integrera med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Cisco Webex.
- Du kan aktivera användarna att få loggas automatiskt Cisco Webex med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats--Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Cisco Webex, behöver du följande:

- En Azure AD-prenumeration
- En Cisco Webex enkel inloggning på aktiverat prenumeration

> [!NOTE]
> Vi rekommenderar inte använda en produktionsmiljö för att testa stegen i den här självstudiekursen.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte din produktionsmiljö om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [skaffa en kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Cisco Webex från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-cisco-webex-from-the-gallery"></a>Lägg till Cisco Webex från galleriet
Du måste lägga till Cisco Webex från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Cisco Webex i Azure AD.

**Om du vill lägga till Cisco Webex från galleriet, gör du följande:**

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till ett nytt program, Välj den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Cisco Webex**. 

5. Välj **Cisco Webex** från panelen resultat. Välj sedan den **Lägg till** för att lägga till programmet.

    ![Cisco Webex i resultatlistan](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Cisco Webex baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD att veta vilka motsvarande användaren i Cisco Webex är att en användare i Azure AD. Med andra ord måste du skapa en länk mellan en Azure AD-användare och en relaterad användare i Cisco Webex.

Ge värdet i Cisco Webex **användarnamn** samma värde som **användarnamn** i Azure AD. Du har nu skapat länken mellan de två användarna. 

Slutför följande byggblock för att konfigurera och testa Azure AD enkel inloggning med Cisco Webex:

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on) att användarna ska kunna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare Cisco Webex](#create-a-cisco-webex-test-user) har en motsvarighet för Britta Simon Cisco Webex som är kopplad till Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#test-single-sign-on) att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Cisco Webex.

**För att konfigurera Azure AD enkel inloggning med Cisco Webex, gör du följande:**

1. I Azure-portalen på den **Cisco Webex** programmet integration anger **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. Aktivera enkel inloggning, i den **enkel inloggning** i dialogrutan den **läge** listrutan, Välj **SAML-baserade inloggning**.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. I den **Cisco Webex domän och URL: er** avsnittet, gör du följande:

    ![URL: er och Cisco Webex domän med enkel inloggning information](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. I den **inloggnings-URL** Skriv en URL med följande mönster:`https://<subdomain>.webex.com`

    b. I den **identifierare** Skriv URL: en `http://www.webex.com`.

    c. I den **Reply URL** Skriv en URL med följande mönster:`https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska reply URL: en och URL: en inloggning. Kontakta [Cisco Webex klient supportteamet](https://www.webex.co.in/support/support-overview.html) att hämta dessa värden. 

5. På den **SAML-signeringscertifikat** väljer **XML-Metadata för**, och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. Välj **Spara**.

    ![Konfigurera den enkel inloggning spara knappen](./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_400.png)
    
6. I den **Cisco Webex Configuration** väljer **konfigurera Cisco Webex** att öppna den **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL**, **SAML enhets-ID**, och **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens** avsnitt.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. I en annan webbläsarfönster loggar du in på ditt Cisco Webex företagets webbplats som administratör.

8. På menyn högst upp väljer **Platsadministration**.

    ![Platsadministration](./media/active-directory-saas-cisco-webex-tutorial/ic777621.png "Platsadministration")

9. I den **Hantera plats** väljer **SSO Configuration**.
   
    ![Konfiguration av SSO](./media/active-directory-saas-cisco-webex-tutorial/ic777622.png "SSO-konfiguration")

10. I den **Federerad Web SSO-konfiguration** avsnittet, gör du följande:
   
    ![Federerad enkel inloggning Configuration](./media/active-directory-saas-cisco-webex-tutorial/ic777623.png "federerad enkel inloggning konfiguration")  

    a. I den **Federation-protokollet** väljer **SAML 2.0**.

    b. För **SSO profil**väljer **SP-initierad**.

    c. Öppna din hämtat certifikat i anteckningar och kopiera innehållet.

    d. Välj **importera SAML Metadata**, och klistra in det kopierade innehållet i certifikatet.

    e. I den **utfärdaren för SAML (IdP-ID)** klistra in värdet för den **SAML enhets-ID** som du kopierade från Azure-portalen.

    f. I den **inloggnings-URL för kunden SSO-Service** klistra in **SAML inloggning tjänst-URL för enkel**, som du kopierade från Azure-portalen.

    g. Från den **NameID Format** väljer **e-postadress**.

    h. I den **AuthnContextClassRef** skriver **urn: oasis: namn: tc: SAML:2.0:ac:classes:Password**.

    Jag. I den **kunden URL för enkel inloggning logga ut** klistra in **Sign-Out URL**, som du kopierade från Azure-portalen.
   
    j. Välj **uppdatering**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) medan du installerar appen. När du lägger till den här appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och sedan använda den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentation på [Azure AD inbäddade dokumentationen](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan, Välj den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och välj sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan rutan, gör du följande:

    ![Dialogrutan användare](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Välj **Skapa**.
 
### <a name="create-a-cisco-webex-test-user"></a>Skapa en Cisco-Webex testanvändare

Om du vill aktivera Azure AD-användare att logga in på Cisco Webex, måste de vara etablerade i Cisco Webex. Cisco Webex är etablering en manuell aktivitet.

**Om du vill konfigurera ett användarkonto, gör du följande:**

1. Logga in på ditt **Cisco Webex** klient.

2. Gå till **hantera användare** > **lägga till användare**.
   
    ![Lägg till användare](./media/active-directory-saas-cisco-webex-tutorial/ic777625.png "lägga till användare")

3. I den **Lägg till användare** avsnittet, gör du följande:
   
    ![Lägg till användare](./media/active-directory-saas-cisco-webex-tutorial/ic777626.png "Lägg till användare")   

    a. För **kontotyp**väljer **värden**.

    b. I den **Förnamn** Ange först namnet på användaren (i det här fallet **Britta**).

    c. I den **efternamn** skriver efternamn för användaren (i det här fallet **Simon**).

    d. I den **användarnamn** Skriv e-postadressen för användaren (i det här fallet  **Brittasimon@contoso.com** ).

    e. I den **e-post** Skriv e-postadressen för användaren (i det här fallet  **Brittasimon@contoso.com** ).

    f. I den **lösenord** Skriv användarens lösenord.

    g. I den **Bekräfta** lösenord och bekräfta användarens lösenord.

    h. Välj **Lägg till**.

>[!NOTE]
>Du kan använda andra Cisco Webex användare skapa verktyg eller API: er som tillhandahålls av Cisco Webex att etablera Azure AD-användarkonton. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du låta dina användare Britta Simon att använda Azure enkel inloggning genom att ge dem åtkomst till Cisco Webex.

![Tilldela rollen][200] 

**Om du vill tilldela Cisco Webex Britta Simon gör du följande:**

1. Öppna program i Azure-portalen. Nästa, gå till vyn directory och sedan till **företagsprogram**.  

2. Välj **alla program**.

    ![Tilldela användare][201] 

3. Välj i listan med program **Cisco Webex**.

    ![Cisco Webex länken i listan med program](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. Välj på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj den **Lägg till** knappen. Välj sedan **användare och grupper** i den **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** dialogrutan **Britta Simon** i den **användare** lista.

6. I den **användare och grupper** dialogrutan klickar du på den **Välj** knappen.

7. Välj den **tilldela** knappen i den **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

När du väljer Cisco Webex-panelen på åtkomstpanelen loggas du automatiskt hämta i tillämpningsprogrammet Cisco Webex.

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

