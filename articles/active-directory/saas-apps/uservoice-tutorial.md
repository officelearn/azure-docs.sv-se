---
title: 'Självstudier: Azure Active Directory-integrering med UserVoice | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och UserVoice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: 12f5d9a01de0fc452006190f00cf9133f0e9a407
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35968206"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Självstudier: Azure Active Directory-integrering med UserVoice

I kursen får lära du att integrera UserVoice med Azure Active Directory (AD Azure).

Integrera UserVoice med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till UserVoice.
- Du kan aktivera användarna att automatiskt hämta loggat in på UserVoice (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med UserVoice, behöver du följande:

- En Azure AD-prenumeration
- En UserVoice enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till UserVoice från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-uservoice-from-the-gallery"></a>Att lägga till UserVoice från galleriet
Du måste lägga till UserVoice från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av UserVoice i Azure AD.

**Utför följande steg för att lägga till UserVoice från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **UserVoice**väljer **UserVoice** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![UserVoice i resultatlistan](./media/uservoice-tutorial/tutorial_uservoice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med UserVoice baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i UserVoice motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i UserVoice upprättas.

I UserVoice, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med UserVoice, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare UserVoice](#create-a-uservoice-test-user)**  – du har en motsvarighet för Britta Simon i UserVoice som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt UserVoice-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med UserVoice:**

1. I Azure-portalen på den **UserVoice** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/uservoice-tutorial/tutorial_uservoice_samlbase.png)

3. På den **UserVoice domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och UserVoice domän med enkel inloggning information](./media/uservoice-tutorial/tutorial_uservoice_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<tenantname>.UserVoice.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<tenantname>.UserVoice.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [UserVoice klienten supportteamet](https://www.uservoice.com/) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCKET** värdet för certifikatet.

    ![Länken hämta certifikatet](./media/uservoice-tutorial/tutorial_uservoice_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/uservoice-tutorial/tutorial_general_400.png)

6. På den **UserVoice Configuration** klickar du på **konfigurera UserVoice** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![UserVoice-konfiguration](./media/uservoice-tutorial/tutorial_uservoice_configure.png) 

7. I en annan webbläsarfönster logga du in på ditt UserVoice-webbplatsen för företag som administratör.

8. Klicka på i verktygsfältet högst upp **inställningar**, och välj sedan **webbportalen** på menyn.
   
    ![Inställningar på sidan App](./media/uservoice-tutorial/ic777519.png "inställningar")

9. På den **webbportalen** fliken den **användarautentisering** klickar du på **redigera** att öppna den **redigera användarautentisering** dialogrutan sidan.
   
    ![Webbportalen fliken](./media/uservoice-tutorial/ic777520.png "Web-portalen")

10. På den **redigera användarautentisering** dialogrutan utför följande steg:
   
    ![Redigera användarautentisering](./media/uservoice-tutorial/ic777521.png "redigera användarautentisering")
   
    a. Klicka på **enkel inloggning (SSO)**.
 
    b. Klistra in den **SAML inloggning tjänst-URL för enkel** -värde som du har kopierat från Azure-portalen i den **SSO Remote inloggning** textruta.

    c. Klistra in den **Sign-Out URL** -värde som du har kopierat från Azure-portalen i den **SSO Remote Sign-Out textruta**.
 
    d. Klistra in den **tumavtrycket** -värde som du har kopierat från Azure-portalen i den **aktuella certifikat SHA1 fingeravtryck** textruta.
    
    e. Klicka på **spara autentiseringsinställningarna**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/uservoice-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/uservoice-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/uservoice-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/uservoice-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-uservoice-test-user"></a>Skapa en testanvändare UserVoice

Om du vill aktivera Azure AD-användare kan logga in på UserVoice etableras de till UserVoice. När det gäller UserVoice är etablering en manuell aktivitet.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Utför följande steg om du vill konfigurera ett användarkonto:
1. Logga in på ditt **UserVoice** klient.

2. Gå till **inställningar**.
   
    ![Inställningar för](./media/uservoice-tutorial/ic777811.png "inställningar")

3. Klicka på **allmänna**.

4. Klicka på **agenter och behörigheter**.
   
    ![Agenter och behörigheter](./media/uservoice-tutorial/ic777812.png "agenter och behörigheter")

5. Klicka på **lägger till administratörer**.
   
    ![Lägg till administratörer](./media/uservoice-tutorial/ic777813.png "lägger till administratörer")

6. På den **bjuda in administratörer** dialogrutan, utför följande steg:
   
    ![Bjud in administratörer](./media/uservoice-tutorial/ic777814.png "bjuda in administratörer")
   
    a. Skriv e-postadressen för det konto som du vill etablera och klicka sedan på i textrutan e-postmeddelanden **Lägg till**.
   
    b. Klicka på **bjuda in**.

> [!NOTE]
> Du kan använda något annat UserVoice användarens konto skapas verktyg eller API: er som tillhandahålls av UserVoice att etablera AAD-användarkonton.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till UserVoice.

![Tilldela rollen][200] 

**Om du vill tilldela UserVoice Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **UserVoice**.

    ![UserVoice-länken i listan med program](./media/uservoice-tutorial/tutorial_uservoice_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen UserVoice på åtkomstpanelen du bör få automatiskt loggat in på ditt UserVoice-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/uservoice-tutorial/tutorial_general_01.png
[2]: ./media/uservoice-tutorial/tutorial_general_02.png
[3]: ./media/uservoice-tutorial/tutorial_general_03.png
[4]: ./media/uservoice-tutorial/tutorial_general_04.png

[100]: ./media/uservoice-tutorial/tutorial_general_100.png

[200]: ./media/uservoice-tutorial/tutorial_general_200.png
[201]: ./media/uservoice-tutorial/tutorial_general_201.png
[202]: ./media/uservoice-tutorial/tutorial_general_202.png
[203]: ./media/uservoice-tutorial/tutorial_general_203.png

