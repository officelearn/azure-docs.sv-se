---
title: 'Självstudier: Azure Active Directory-integrering med UserVoice | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och UserVoice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: a2b8faaf3ee7d8f5dfca35f860dbd003f9e6072b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194346"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Självstudier: Azure Active Directory-integrering med UserVoice

I den här självstudien får du lära dig hur du integrerar UserVoice med Azure Active Directory (AD Azure).

Integrera UserVoice med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till UserVoice.
- Du kan aktivera användarna att automatiskt få loggat in på UserVoice (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med UserVoice, behöver du följande objekt:

- En Azure AD-prenumeration
- En UserVoice enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till UserVoice från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-uservoice-from-the-gallery"></a>Att lägga till UserVoice från galleriet
För att konfigurera integrering av UserVoice i Azure AD, som du behöver lägga till UserVoice från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till UserVoice från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **UserVoice**väljer **UserVoice** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![UserVoice i resultatlistan](./media/uservoice-tutorial/tutorial_uservoice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med UserVoice baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i UserVoice är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i UserVoice upprättas.

I UserVoice, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med UserVoice, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare UserVoice](#create-a-uservoice-test-user)**  – du har en motsvarighet för Britta Simon i UserVoice som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt UserVoice-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med UserVoice:**

1. I Azure-portalen på den **UserVoice** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/uservoice-tutorial/tutorial_uservoice_samlbase.png)

1. På den **UserVoice-domän och URL: er** avsnittet, utför följande steg:

    ![UserVoice-domän och URL: er med enkel inloggning för information](./media/uservoice-tutorial/tutorial_uservoice_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<tenantname>.UserVoice.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<tenantname>.UserVoice.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [UserVoice klienten supportteamet](https://www.uservoice.com/) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCK** värdet för certifikatet.

    ![Länk för nedladdning av certifikatet](./media/uservoice-tutorial/tutorial_uservoice_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/uservoice-tutorial/tutorial_general_400.png)

1. På den **UserVoice Configuration** klickar du på **konfigurera UserVoice** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![UserVoice-konfiguration](./media/uservoice-tutorial/tutorial_uservoice_configure.png) 

1. I ett annat webbläsarfönster logga du in på ditt UserVoice-webbplatsen för företaget som administratör.

1. I verktygsfältet högst upp, klickar du på **inställningar**, och välj sedan **webbportalen** på menyn.
   
    ![Inställningar på App-sida](./media/uservoice-tutorial/ic777519.png "inställningar")

1. På den **webbportalen** fliken den **användarautentisering** klickar du på **redigera** att öppna den **redigera användarautentisering** dialogrutan sidan.
   
    ![Webbportalen fliken](./media/uservoice-tutorial/ic777520.png "webbportalen")

1. På den **redigera användarautentisering** dialogrutan utför följande steg:
   
    ![Redigera användarautentisering](./media/uservoice-tutorial/ic777521.png "redigera användarautentisering")
   
    a. Klicka på **enkel inloggning (SSO)**.
 
    b. Klistra in den **SAML enkel inloggning för tjänst-URL** värde, som du har kopierat från Azure-portalen till den **SSO Remote inloggning** textrutan.

    c. Klistra in den **URL: en för utloggning** värde, som du har kopierat från Azure-portalen till den **SSO Remote utloggning textrutan**.
 
    d. Klistra in den **tumavtryck** värde, som du har kopierat från Azure-portalen till den **aktuella certifikat SHA1-fingeravtryck** textrutan.
    
    e. Klicka på **spara autentiseringsinställningar**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/uservoice-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/uservoice-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/uservoice-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/uservoice-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-uservoice-test-user"></a>Skapa en testanvändare i UserVoice

Om du vill aktivera Azure AD-användare att logga in till UserVoice, måste de etableras i UserVoice. När det gäller UserVoice är etablering en manuell aktivitet.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Gör följande för att etablera ett användarkonto:
1. Logga in på din **UserVoice** klient.

1. Gå till **inställningar**.
   
    ![Inställningar](./media/uservoice-tutorial/ic777811.png "Inställningar")

1. Klicka på **Allmänt**.

1. Klicka på **agenter och behörigheter**.
   
    ![Agenter och behörigheter](./media/uservoice-tutorial/ic777812.png "agenter och behörigheter")

1. Klicka på **lägga till administratörer**.
   
    ![Lägga till administratörer](./media/uservoice-tutorial/ic777813.png "lägga till administratörer")

1. På den **bjuda in administratörer** dialogrutan utför följande steg:
   
    ![Bjud in administratörer](./media/uservoice-tutorial/ic777814.png "bjuda in administratörer")
   
    a. I textrutan för e-postmeddelanden, skriver du e-postadressen för det konto som du vill etablera och klicka sedan på **Lägg till**.
   
    b. Klicka på **bjuda in**.

> [!NOTE]
> Du kan använda alla andra UserVoice användare konto verktyg för att skapa eller API: er som tillhandahålls av UserVoice att etablera AAD-användarkonton.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till UserVoice.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon UserVoice, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **UserVoice**.

    ![UserVoice-länk i listan med program](./media/uservoice-tutorial/tutorial_uservoice_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen UserVoice i åtkomstpanelen du bör få automatiskt loggat in på ditt UserVoice-program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

