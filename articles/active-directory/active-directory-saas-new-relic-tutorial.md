---
title: "Självstudier: Azure Active Directory-integrering med New Relic | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och New Relic."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: 5c5d6eb8333083cf87e35a667159ecc71b944e9a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Självstudier: Azure Active Directory-integrering med New Relic

I kursen får lära du att integrera New Relic med Azure Active Directory (AD Azure).

Integrera New Relic med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till New Relic.
- Du kan aktivera användarna att automatiskt hämta loggat in på New Relic (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med New Relic behöver du följande:

- En Azure AD-prenumeration
- Ett New Relic enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till New Relic från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-new-relic-from-the-gallery"></a>Att lägga till New Relic från galleriet
Du måste lägga till New Relic från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av New Relic i Azure AD.

**Utför följande steg för att lägga till New Relic från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **New Relic**väljer **New Relic** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![New Relic i resultatlistan](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med New Relic baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i New Relic motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i New Relic upprättas.

I New Relic tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med New Relic, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare New Relic](#create-a-new-relic-test-user)**  – du har en motsvarighet för Britta Simon i New Relic som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i New Relic-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med New Relic:**

1. I Azure-portalen på den **New Relic** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_samlbase.png)

3. På den **ny Relic domän och URL: er** avsnittet, utför följande steg:

    ![Nya Relic domän och URL: er med enkel inloggning information](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` – se till att ersätta dina egna nya Relic-konto-ID.

    b. I den **identifierare** textruta Skriv värdet:`rpm.newrelic.com`

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-new-relic-tutorial/tutorial_general_400.png)

6. På den **nya Relic konfigurationen** klickar du på **konfigurera New Relic** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Ny Relic-konfiguration](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_configure.png) 

7. I en annan webbläsarfönstret, logga in på ditt **New Relic** företagets webbplats som administratör.

8. Klicka på menyn högst upp **kontoinställningar**.
   
    ![Kontoinställningar](./media/active-directory-saas-new-relic-tutorial/ic797036.png "kontoinställningar")

9. Klicka på den **säkerhet och autentisering** fliken och klicka sedan på den **enkel inloggning** fliken.
   
    ![Enkel inloggning](./media/active-directory-saas-new-relic-tutorial/ic797037.png "enkel inloggning")

10. På sidan SAML dialogrutan utför du följande steg:
   
    ![SAML](./media/active-directory-saas-new-relic-tutorial/ic797038.png "SAML")
   
   a. Klicka på **Välj fil** överföra ditt hämtade Azure Active Directory-certifikat.

   b. I den **fjärrinloggning URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.
   
   c. I den **logga ut hamnar URL** textruta klistra in värdet för **Sign-Out URL**, som du har kopierat från Azure-portalen.

   d. Klicka på **spara mina ändringar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-new-relic-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-new-relic-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-new-relic-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-new-relic-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-new-relic-test-user"></a>Skapa en New Relic testanvändare

För att aktivera Azure Active Directory-användare kan logga in till New Relic etableras de till New Relic. New Relic är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto till New Relic:**

1. Logga in på ditt **New Relic** företagets webbplats som administratör.

2. Klicka på menyn högst upp **kontoinställningar**.
   
    ![Kontoinställningar](./media/active-directory-saas-new-relic-tutorial/ic797040.png "kontoinställningar")

3. I den **konto** vänster, klicka på **sammanfattning**, och klicka sedan på **Lägg till användare**.
   
    ![Kontoinställningar](./media/active-directory-saas-new-relic-tutorial/ic797041.png "kontoinställningar")

4. På den **aktiva användare** dialogrutan, utför följande steg:
   
    ![Aktiva användare](./media/active-directory-saas-new-relic-tutorial/ic797042.png "aktiva användare")
   
    a. I den **e-post** textruta, ange en giltig Azure Active Directory-användare som du vill etablera e-postadress.

    b. Som **rollen** Välj **användaren**.

    c. Klicka på **Lägg till användaren**.

>[!NOTE]
>Du kan använda något annat New Relic användarens konto skapas verktyg eller API: er som tillhandahålls av New Relic etablera AAD-användarkonton.
> 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till New Relic.

![Tilldela rollen][200] 

**Om du vill tilldela New Relic Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **New Relic**.

    ![New Relic-länken i listan med program](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen New Relic på åtkomstpanelen du bör få automatiskt loggat in på ditt New Relic-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_203.png

