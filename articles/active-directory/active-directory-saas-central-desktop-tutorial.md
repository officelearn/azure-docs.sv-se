---
title: 'Självstudier: Azure Active Directory-integrering med Central Desktop | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och centrala skrivbordet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 92c24688cf3d9baefcedcf22c915752b2d29b53c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Självstudier: Azure Active Directory-integrering med Central Desktop

I kursen får lära du att integrera centrala skrivbordet med Azure Active Directory (AD Azure).

Integrera centrala skrivbordet med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till centrala skrivbordet.
- Du kan aktivera användarna att automatiskt hämta loggar in på centrala skrivbordet med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats--Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Central skrivbordet, behöver du följande:

- En Azure AD-prenumeration
- En Central Desktop enkel inloggning på aktiverat prenumeration

> [!NOTE]
> Vi rekommenderar inte använda en produktionsmiljö för att testa stegen i den här självstudiekursen.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte din produktionsmiljö om det är nödvändigt.
- Om du inte redan har en utvärderingsversion Azure AD-miljö [skaffa en kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till centrala skrivbordet från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-central-desktop-from-the-gallery"></a>Lägg till centrala skrivbordet från galleriet
Du måste lägga till centrala skrivbordet från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av centrala skrivbordet i Azure AD.

**Om du vill lägga till centrala skrivbord från galleriet gör du följande:**

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya program, Välj den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **centrala Desktop**. Välj **centrala Desktop** resultatrutan och välj sedan den **Lägg till** för att lägga till programmet.

    ![Central skrivbordet i resultatlistan](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Central skrivbord baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD att veta vilka motsvarande användaren i centrala Desktop är att en användare i Azure AD. Med andra ord måste du upprätta en koppling mellan en Azure AD-användare och relaterade användaren i centrala Desktop.

I centrala Desktop ge **användarnamn** samma värde som **användarnamn** i Azure AD. Du har nu skapat länken mellan de två användarna.

Om du vill konfigurera och testa Azure AD enkel inloggning med Central skrivbordet, måste du utföra följande byggblock:

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on) att användarna ska kunna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en Central Desktop testanvändare](#create-a-central-desktop-test-user) har en motsvarighet för Britta Simon centrala skrivbordet som är kopplad till Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#test-single-sign-on) att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i centrala Desktop-program.

**För att konfigurera Azure AD enkel inloggning med Central skrivbordet, gör du följande:**

1. I Azure-portalen på den **centrala Desktop** programmet integration anger **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. Aktivera enkel inloggning, i den **enkel inloggning** i dialogrutan den **läge** listrutan, Välj **SAML-baserade inloggning**.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

3. I den **centrala Desktop domän och URL: er** avsnittet, gör du följande:

    ![URL: er och centrala skrivbord domän med enkel inloggning information](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. I den **inloggnings-URL** Skriv en URL med följande mönster: `https://<companyname>.centraldesktop.com`

    b. I den **identifierare** Skriv en URL med följande mönster:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. I den **Reply URL** Skriv en URL med följande mönster: `https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren svara URL och inloggnings-URL. Kontakta den [centrala Desktop client supportteamet](https://imeetcentral.com/contact-us) att hämta dessa värden. 

4. I den **SAML-signeringscertifikat** väljer **certifikat**. Spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

5. Välj knappen **Spara**.

    ![Konfigurera den enkel inloggning spara knappen](./media/active-directory-saas-central-desktop-tutorial/tutorial_general_400.png)
    
6. I den **centrala konfiguration av fjärrskrivbord** väljer **konfigurera centrala Desktop** att öppna den **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens** avsnitt.

    ![Central konfiguration av fjärrskrivbord](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

7. Logga in på ditt **centrala Desktop** klient.

8. Gå till **inställningar**. Välj **Avancerat**, och välj sedan **enkel inloggning**.

    ![-Installation - avancerade](./media/active-directory-saas-central-desktop-tutorial/ic769563.png "-installation - avancerade")

9. På den **enkel inloggning på inställningar** utför följande steg:

    ![Enkel inloggning inställningar](./media/active-directory-saas-central-desktop-tutorial/ic769564.png "enkel inloggning på inställningar")
    
    a. Välj **aktivera SAML v2 för enkel inloggning**.
    
    b. I den **SSO URL** klistra in den **SAML enhets-ID** värde som du kopierade från Azure-portalen.
    
    c. I den **inloggnings-URL för SSO** klistra in den **SAML inloggning tjänst-URL för enkel** värde som du kopierade från Azure-portalen.
    
    d. I den **SSO logga ut URL** klistra in den **Sign-Out URL** värde som du kopierade från Azure-portalen.

10. I den **meddelandet signatur verifieringsmetod** avsnittet, gör du följande:

    ![Meddelandet signatur verifieringsmetod](./media/active-directory-saas-central-desktop-tutorial/ic769565.png "meddelandet signatur verifieringsmetod") en. Välj **Certifikat**.
    
    b. I den **SSO certifikat** väljer **RSH SHA256**.
    
    c. Öppna din hämtat certifikat i anteckningar. Kopiera innehållet i certifikatet och klistra in det i den **SSO certifikat** fältet.
        
    d. Välj **visas en länk till inloggningssidan för SAMLv2**.
    
    e. Välj **uppdatering**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) medan du installerar appen. När du lägger till den här appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och sedan använda den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentation på [Azure AD inbäddade dokumentationen]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Om du vill skapa en testanvändare i Azure AD, gör du följande:**

1. I Azure-portalen i den vänstra rutan, Välj den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**. Välj sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan rutan, gör du följande:

    ![Dialogrutan användare](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Välj **Skapa**.
 
### <a name="create-a-central-desktop-test-user"></a>Skapa en Central Desktop testanvändare

För Azure AD-användare för att kunna logga in, måste de vara etablerade i centrala Desktop-programmet. Det här avsnittet beskriver hur du skapar användarkonton i Azure AD i centrala Desktop.

> [!NOTE]
> Du kan använda andra centrala Desktop användare skapa verktyg eller API: er som tillhandahålls av centrala skrivbordet för att etablera Azure AD-användarkonton.

**Att etablera användarkonton till centrala skrivbordet:**

1. Logga in på din centrala Desktop-klient.

2. Gå till **personer** > **inre medlemmar**.

3. Välj **lägga till inre medlemmar**.

    ![Personer](./media/active-directory-saas-central-desktop-tutorial/ic781051.png "personer")
    
4. I den **e-postadressen för nya medlemmar** skriver en Azure AD-kontot som du vill etablera och välj sedan **nästa**.

    ![E-postadresser för nya medlemmar](./media/active-directory-saas-central-desktop-tutorial/ic781052.png "e-postadresser för nya medlemmar")

5. Välj **lägga till interna medlem(mar)**.

    ![Lägg till interna medlem](./media/active-directory-saas-central-desktop-tutorial/ic781053.png "Lägg till interna medlem")
   
   >[!NOTE]
   >Användarna som du lägger till får ett e-postmeddelande som innehåller en länk till bekräftelse för att aktivera sina konton.
   
### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du låta användare Britta Simon att använda Azure enkel inloggning genom att ge dem åtkomst till centrala skrivbordet.

![Tilldela rollen][200] 

**Om du vill tilldela centrala Desktop Britta Simon gör du följande:**

1. Öppna program i Azure-portalen. Gå till vyn katalog och gå sedan till **företagsprogram**.

2. Välj **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **centrala Desktop**.

    ![Central Desktop-länken i listan med program](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_app.png)  

3. Välj på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj den **Lägg till** knappen. Välj sedan **användare och grupper** i den **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** dialogrutan **Britta Simon** i den **användare** lista.

6. I den **användare och grupper** dialogrutan klickar du på den **Välj** knappen.

7. I den **Lägg uppdrag** dialogrutan markerar du den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet, testa Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

När du väljer centrala Desktop-panelen i åtkomstpanelen loggas du automatiskt hämta i tillämpningsprogrammet centrala skrivbordet.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_203.png

