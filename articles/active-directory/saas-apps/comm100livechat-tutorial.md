---
title: 'Självstudier: Azure Active Directory-integration med Comm100 Live-chatt | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Comm100 Live-chatt.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: jeedes
ms.openlocfilehash: b85162c8392e8ecdb08a3ed04ff5b9de835808a1
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "42059191"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Självstudier: Azure Active Directory-integration med Comm100 Live-chatt

I den här självstudien får du lära dig hur du integrerar Comm100 Live-chatt med Azure Active Directory (AD Azure).

Integrera Comm100 Live-chatt med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Comm100 Live-chatt.
- Du kan aktivera användarna att automatiskt få loggat in på Comm100 Live-chatt (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Comm100 Live-chatt, behöver du följande objekt:

- En Azure AD-prenumeration
- En Comm100 Live-chatt enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Comm100 Live-chatt från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Att lägga till Comm100 Live-chatt från galleriet
För att konfigurera integrering av Comm100 chatta Live i Azure AD, som du behöver lägga till Comm100 Live-chatt från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Comm100 Live-chatt från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Comm100 Live-chatt**väljer **Comm100 Live-chatt** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Comm100 Live-chatt i resultatlistan](./media/comm100livechat-tutorial/tutorial_comm100livechat_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Comm100 Live-chatt baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Comm100 Live-chatt är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Comm100 Live-chatt upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Comm100 Live-chatt, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Comm100 Live-chatt](#create-a-comm100-live-chat-test-user)**  – du har en motsvarighet för Britta Simon i Comm100 Live-chatt som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Comm100 Live-chatt.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Comm100 Live-chatt:**

1. I Azure-portalen på den **Comm100 Live-chatt** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/comm100livechat-tutorial/tutorial_comm100livechat_samlbase.png)

3. På den **Comm100 Live-chatt domän och URL: er** avsnittet, utför följande steg:

    ![Comm100 Live-chatt domän och URL: er med enkel inloggning för information](./media/comm100livechat-tutorial/tutorial_comm100livechat_url.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`
    
    > [!NOTE] 
    > Inloggnings-URL-värdet är inte verkliga. Du uppdaterar inloggnings-URL-värdet med det faktiska inloggnings-URL, vilket beskrivs senare i självstudien.

4. Comm100 Live chattprogram förväntar sig SAML-intyg innehålla specifika attribut. Konfigurera följande attribut för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för detta.

    ![Konfigurera enkel inloggning](./media/comm100livechat-tutorial/tutorial_attribute_03.png)
    
5. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    |  Attributnamn  | Attributvärde |
    | --------------- | -------------------- |    
    |   e-post    | User.Mail |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/comm100livechat-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/comm100livechat-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna den **Namespace** tom.
    
    e. Klicka på **OK**.

6. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/comm100livechat-tutorial/tutorial_comm100livechat_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/comm100livechat-tutorial/tutorial_general_400.png)

8. På den **Comm100 Live-chatt Configuration** klickar du på **konfigurera Comm100 Live-chatt** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Comm100 Live-chatt-konfiguration](./media/comm100livechat-tutorial/tutorial_comm100livechat_configure.png)

9. I ett annat webbläsarfönster, logga in på Comm100 Live-chatt som en administratör.

10. På upp till höger på sidan, klickar du på **mitt konto**.

    ![Comm100 Live-chatt myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

11. Till vänster i menyn klickar du på **Security** och klicka sedan på **agenten Single Sign-On**.

    ![Comm100 Live-chatt säkerhet](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

12. På den **agenten Single Sign-On** utför följande steg:

    ![Comm100 Live-chatt säkerhet](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

    a. Kopiera den första markerade länken och klistra in den i **inloggnings-URL** -textrutan i **Comm100 Live-chatt domän och URL: er** avsnittet på Azure-portalen.

    b. I den **URL för SAML SSO** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    c. I den **Remote URL för utloggning** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    d. Klicka på **Välj en fil** att ladda upp Base64-kodat certifikat som du har hämtat från Azure-portalen i den **certifikat**.

    e. Klicka på **spara ändringar**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/comm100livechat-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/comm100livechat-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/comm100livechat-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/comm100livechat-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-comm100-live-chat-test-user"></a>Skapa en testanvändare Comm100 Live-chatt

Om du vill aktivera Azure AD-användare att logga in på Comm100 Live-chatt, måste de etableras i Comm100 Live-chatt. I Comm100 Live Chat är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Comm100 Live-chatt som en administratör.

2. På upp till höger på sidan, klickar du på **mitt konto**.

    ![Comm100 Live-chatt myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. Till vänster i menyn klickar du på **agenter** och klicka sedan på **ny Agent**.

    ![Comm100 Live-chatt-agenten](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. På den **ny Agent** utför följande steg:

    ![Ny Comm100 Live-chatt-agent](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. I **e-post** text, ange den e-postadressen för användaren som **Brittasimon@contoso.com**.

    b. I **Förnamn** text, ange först namnet på användaren som **Britta**.

    c. I **efternamn** text anger efternamn för användaren som **simon**.

    d. I den **visningsnamn** textrutan Ange visningsnamnet för användare som **Britta simon**

    e. I den **lösenord** textrutan, ange ditt lösenord.

    f. Klicka på **Spara**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Comm100 Live-chatt.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Comm100 Live-chatt, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Comm100 Live-chatt**.

    ![Länken Comm100 Live-chatt i listan med program](./media/comm100livechat-tutorial/tutorial_comm100livechat_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Comm100 Live-chatt i åtkomstpanelen du bör få automatiskt loggat in på programmets Comm100 Live-chatt.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/comm100livechat-tutorial/tutorial_general_01.png
[2]: ./media/comm100livechat-tutorial/tutorial_general_02.png
[3]: ./media/comm100livechat-tutorial/tutorial_general_03.png
[4]: ./media/comm100livechat-tutorial/tutorial_general_04.png

[100]: ./media/comm100livechat-tutorial/tutorial_general_100.png

[200]: ./media/comm100livechat-tutorial/tutorial_general_200.png
[201]: ./media/comm100livechat-tutorial/tutorial_general_201.png
[202]: ./media/comm100livechat-tutorial/tutorial_general_202.png
[203]: ./media/comm100livechat-tutorial/tutorial_general_203.png

