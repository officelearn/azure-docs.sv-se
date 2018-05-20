---
title: 'Självstudier: Azure Active Directory-integrering med PagerDuty | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 6ba7e1fc2f58fd8fb7a05935ce2c4b99709d642c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Självstudier: Azure Active Directory-integrering med PagerDuty

I kursen får lära du att integrera PagerDuty med Azure Active Directory (AD Azure).

Integrera PagerDuty med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till PagerDuty
- Du kan aktivera användarna att automatiskt hämta loggat in på PagerDuty (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med PagerDuty, behöver du följande:

- En Azure AD-prenumeration
- En PagerDuty enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till PagerDuty från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-pagerduty-from-the-gallery"></a>Att lägga till PagerDuty från galleriet
Du måste lägga till PagerDuty från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av PagerDuty i Azure AD.

**Utför följande steg för att lägga till PagerDuty från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **PagerDuty**väljer **PagerDuty** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med PagerDuty baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i PagerDuty motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i PagerDuty upprättas.

I PagerDuty, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med PagerDuty, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare PagerDuty](#create-a-pagerduty-test-user)**  – du har en motsvarighet för Britta Simon i PagerDuty som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt PagerDuty program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med PagerDuty:**

1. I Azure-portalen på den **PagerDuty** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_samlbase.png)

3. På den **PagerDuty domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och PagerDuty domän med enkel inloggning information](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<tenant-name>.pagerduty.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<tenant-name>.pagerduty.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [PagerDuty klienten supportteamet](https://www.pagerduty.com/support/) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-pagerduty-tutorial/tutorial_general_400.png)

6. På den **PagerDuty Configuration** klickar du på **konfigurera PagerDuty** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out Webbadressen och SAML enkel inloggning Service** från den **Snabbreferens avsnitt.**

    ![PagerDuty konfiguration](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_configure.png) 

7. Logga in på webbplatsen Pagerduty företag som en administratör i en annan webbläsarfönster.

8. Klicka på menyn högst upp **kontoinställningar**.
   
    ![Kontoinställningar](./media/active-directory-saas-pagerduty-tutorial/ic778535.png "kontoinställningar")

9. Klicka på **enkel inloggning**.
   
    ![Enkel inloggning](./media/active-directory-saas-pagerduty-tutorial/ic778536.png "enkel inloggning")

10. På den **aktivera enkel inloggning (SSO)** utför följande steg:
   
    ![Aktivera enkel inloggning](./media/active-directory-saas-pagerduty-tutorial/ic778537.png "aktivera enkel inloggning")
   
    a. Öppna din Base64-kodade certifikat hämtas från Azure-portalen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** textruta
  
    b. I den **inloggnings-URL** textruta klistra in **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.
  
    c. I den **logga ut URL** textruta klistra in **Sign-Out URL** som du har kopierat från Azure-portalen.
 
    d. Välj **aktivera enkel inloggning**.
 
    e. Klicka på **spara ändringar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Knappen Lägg till](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan användare](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-pagerduty-test-user"></a>Skapa en testanvändare PagerDuty

Om du vill aktivera Azure AD-användare kan logga in på PagerDuty etableras de i PagerDuty.  
När det gäller PagerDuty är etablering en manuell aktivitet.

>[!NOTE]
>Du kan använda något annat Pagerduty användarens konto skapas verktyg eller API: er som tillhandahålls av Pagerduty för att etablera Azure Active Directory användarkonton.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **Pagerduty** klient.

2. Klicka på menyn högst upp **användare**.

3. Klicka på **lägga till användare**.
   
    ![Lägg till användare](./media/active-directory-saas-pagerduty-tutorial/ic778539.png "lägga till användare")

4.  På den **bjuda in ditt team** dialogrutan, utför följande steg:
   
    ![Bjud in ditt team](./media/active-directory-saas-pagerduty-tutorial/ic778540.png "bjuda in ditt team")

    a. Typ av **första och sista namnet** för användare som **Britta Simon**. 
   
    b. Ange **e-post** -adressen för användaren som **brittasimon@contoso.com**.
   
    c. Klicka på **Lägg till**, och klicka sedan på **skicka inbjuder**.
   
    >[!NOTE]
    >Alla tillagda användare får en inbjudan för att skapa ett PagerDuty-konto.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till PagerDuty.

![Tilldela rollen][200]

**Om du vill tilldela PagerDuty Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **PagerDuty**.

    ![Länken PagerDuty i listan med program](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen PagerDuty i åtkomst Panelyou bör hämta automatiskt loggat in på ditt PagerDuty program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_203.png

