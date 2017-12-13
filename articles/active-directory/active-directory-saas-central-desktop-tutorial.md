---
title: "Självstudier: Azure Active Directory-integrering med Central Desktop | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och centrala skrivbordet."
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
ms.openlocfilehash: 62f1a1e2193d9693519bdea86196cf296d4b9780
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Självstudier: Azure Active Directory-integrering med Central Desktop

I kursen får lära du att integrera centrala skrivbordet med Azure Active Directory (AD Azure).

Integrera centrala skrivbordet med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till centrala skrivbordet.
- Du kan aktivera användarna att automatiskt hämta loggat in på centrala skrivbordet (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Central skrivbordet, behöver du följande:

- En Azure AD-prenumeration
- En Central Desktop enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till centrala skrivbordet från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-central-desktop-from-the-gallery"></a>Att lägga till centrala skrivbordet från galleriet
Du måste lägga till centrala skrivbordet från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av centrala skrivbordet i Azure AD.

**Utför följande steg för att lägga till centrala skrivbordet från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **centrala Desktop**väljer **centrala Desktop** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Central skrivbordet i resultatlistan](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med Central skrivbord baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i centrala Desktop motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i centrala Desktop upprättas.

I centrala Desktop tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Central skrivbordet, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en Central Desktop testanvändare](#create-a-central-desktop-test-user)**  – du har en motsvarighet för Britta Simon i centrala Desktop som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i centrala Desktop-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Central skrivbordet:**

1. I Azure-portalen på den **centrala Desktop** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

3. På den **centrala Desktop domän och URL: er** avsnittet, utför följande steg:

    ![Central Desktop domän och URL: er med enkel inloggning information](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<companyname>.centraldesktop.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. I den **Reply URL** textruta Skriv en URL med följande mönster:`https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Kontakta [centrala Desktop Client supportteamet](https://imeetcentral.com/contact-us) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **certifikat** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-central-desktop-tutorial/tutorial_general_400.png)
    
6. På den **centrala konfiguration av fjärrskrivbord** klickar du på **konfigurera centrala Desktop** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Central konfiguration av fjärrskrivbord](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

7. Logga in på ditt **centrala Desktop** klient.

8. Gå till **inställningar**, klickar du på **Avancerat**, och klicka sedan på **enkel inloggning**.

    ![-Installation - avancerade](./media/active-directory-saas-central-desktop-tutorial/ic769563.png "-installation - avancerade")

9. På den **enkel inloggning på inställningar** utför följande steg:

    ![Enkel inloggning på inställningar](./media/active-directory-saas-central-desktop-tutorial/ic769564.png "enkel inloggning på inställningar")
    
    a. Välj **aktivera SAML v2 för enkel inloggning**.
    
    b. I den **SSO URL** textruta klistra in den **SAML enhets-ID** värde som du har kopierat från Azure-portalen.
    
    c. I den **inloggnings-URL för SSO** textruta klistra in den **SAML inloggning tjänst-URL för enkel** värde som du har kopierat från Azure-portalen.
    
    d. I den **SSO logga ut URL** textruta klistra in den **Sign-Out URL** värde som du har kopierat från Azure-portalen.

10. I den **meddelandet signatur verifieringsmetod** avsnittet, utför följande steg:

    ![Meddelandet signatur verifieringsmetod](./media/active-directory-saas-central-desktop-tutorial/ic769565.png "meddelande signatur verifieringsmetod") en. Välj **certifikat**.
    
    b. Från den **SSO certifikat** väljer **RSH SHA256**.
    
    c. Öppna din hämtat certifikat i anteckningar, kopiera innehållet för certifikatet och klistra in det i den **SSO certifikat** fältet.
        
    d. Välj **visas en länk till inloggningssidan för SAMLv2**.
    
    e. Klicka på **uppdatering**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-central-desktop-test-user"></a>Skapa en Central Desktop testanvändare

För Azure AD-användare för att kunna logga in, måste de etableras till centrala Desktop-programmet. Det här avsnittet beskriver hur du skapar användarkonton i Azure AD i centrala Desktop.

> [!NOTE]
> Du kan använda andra centrala Desktop användare skapa verktyg eller API: er som tillhandahålls av centrala skrivbordet för att etablera Azure AD-användarkonton

**Att etablera användarkonton till centrala skrivbordet:**

1. Logga in på din centrala Desktop-klient.

2. Gå till **personer \> inre medlemmar**.

3. Klicka på **lägga till inre medlemmar**.

    ![Personer](./media/active-directory-saas-central-desktop-tutorial/ic781051.png "personer")
    
4. I den **e-postadressen för nya medlemmar** textruta skriver en Azure AD-kontot du vill etablera och klicka sedan på **nästa**.

    ![E-postadresser för nya medlemmar](./media/active-directory-saas-central-desktop-tutorial/ic781052.png "e-postadresser för nya medlemmar")

5. Klicka på **lägga till interna medlem(mar)**.

    ![Lägga till inre medlem](./media/active-directory-saas-central-desktop-tutorial/ic781053.png "lägga till inre medlem")
   
   >[!NOTE]
   >Du har lagt till användare får ett e-postmeddelande som innehåller en bekräftelse-länk som de behöver klicka om du vill aktivera kontot.
   
### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till centrala skrivbordet.

![Tilldela rollen][200] 

**Om du vill tilldela centrala Desktop Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **centrala Desktop**.

    ![Central Desktop-länken i listan med program](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på ikonen Central skrivbordet på åtkomstpanelen du ska hämta automatiskt loggat in på ditt centrala Desktop-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

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

