---
title: 'Självstudier: Azure Active Directory-integrering med Clever | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Clever.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 06606b4dede242a01beea2136126e6d252f9a4a1
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211439"
---
# <a name="tutorial-azure-active-directory-integration-with-clever"></a>Självstudier: Azure Active Directory-integrering med Clever

I kursen får lära du att integrera Clever med Azure Active Directory (AD Azure).

Integrera Clever med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Clever.
- Du kan aktivera användarna att automatiskt hämta loggat in på Clever (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Clever, behöver du följande:

- En Azure AD-prenumeration
- En smarta enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Clever från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-clever-from-the-gallery"></a>Att lägga till Clever från galleriet
Du måste lägga till Clever från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Clever i Azure AD.

**Utför följande steg för att lägga till Clever från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Clever**väljer **Clever** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Smarta i resultatlistan](./media/clever-tutorial/tutorial_clever_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Clever baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Clever motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Clever upprättas.

I Clever, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Clever, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en smarta testanvändare](#create-a-clever-test-user)**  – du har en motsvarighet för Britta Simon i Clever som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i smarta programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Clever:**

1. I Azure-portalen på den **Clever** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning dialogrutan](./media/clever-tutorial/tutorial_clever_samlbase.png)

3. På den **smarta domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och smarta domän med enkel inloggning information](./media/clever-tutorial/tutorial_clever_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://clever.com/in/<companyname>`

    b. I den **identifierare** textruta anger du URL: `https://clever.com/oauth/saml/metadata.xml`

    > [!NOTE]
    > Inloggning URL-värdet är inte verkliga. Uppdatera det här värdet med det faktiska inloggnings-URL. Kontakta [smarta klienten supportteamet](https://clever.com/about/contact/) att hämta det här värdet.

4. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **webbadress Federation Metadata** och klistra in den i anteckningar.
    
    ![Konfigurera enkel inloggning](./media/clever-tutorial/tutorial_metadataurl.png)

5. Smarta programmet förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till attributmappningar till din **SAML-Token attribut** konfiguration.

    Följande skärmbild visar ett exempel för det här.

    ![Konfigurera enkel inloggning](./media/clever-tutorial/tutorial_clever_07.png)

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:
    
    | Attributnamn  | Attributvärde |
    | --------------- | -------------------- |
    | clever.Teacher.Credentials.district_username|User.userPrincipalName|
    | clever.student.Credentials.district_username| User.userPrincipalName |
    | Förnamn  | User.givenName |
    | Efternamn  | User.surname |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/clever-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurera enkel inloggning](./media/clever-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Lämna den **Namespace** textruta tomt.
    
    d. Klicka på **OK**.
    
7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/clever-tutorial/tutorial_general_400.png)

8. I en annan webbläsarfönster loggar du in på webbplatsen smarta företag som administratör.

9. I verktygsfältet klickar du på **Instant inloggningen**.

    ![Direkt inloggning](./media/clever-tutorial/ic798984.png "Instant inloggning")

    > [!NOTE]
    > Innan du kan testa enkel inloggning, du måste kontakta [smarta klienten supportteamet](https://clever.com/about/contact/) att aktivera Office 365 enkel inloggning i serverdelen.

10. På den **Instant inloggningen** utför följande steg:
    
      ![Direkt inloggning](./media/clever-tutorial/ic798985.png "Instant inloggning")
    
      a. Typ av **Inloggningswebbadressen**.
    
      >[!NOTE]
      >Den **Inloggningswebbadressen** är ett anpassat värde. Kontakta [smarta klienten supportteamet](https://clever.com/about/contact/) att hämta det här värdet.
    
      b. Som **identitetssystem**väljer **ADFS**.

      c. I den **URL för tjänstmetadata** textruta klistra in **webbadress Federation Metadata** värde som du har kopierat från Azure-portalen.
    
      d. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/clever-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/clever-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/clever-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/clever-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.

### <a name="create-a-clever-test-user"></a>Skapa en smarta testanvändare

Om du vill aktivera Azure AD-användare kan logga in på Clever etableras de i Clever.

Arbeta med vid Clever, [smarta klienten supportteamet](https://clever.com/about/contact/) att lägga till användare i smarta plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

>[!NOTE]
>Du kan använda något annat smarta användarens konto skapas verktyg eller API: er som tillhandahålls av Clever att etablera Azure AD-användarkonton.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Clever.

![Tilldela rollen][200]

**Om du vill tilldela Clever Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201]

2. Välj i listan med program **Clever**.

    ![Clever länken i listan med program](./media/clever-tutorial/tutorial_clever_app.png)

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen smarta åtkomst på panelen du ska hämta automatiskt loggat in på smarta programmet.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/clever-tutorial/tutorial_general_01.png
[2]: ./media/clever-tutorial/tutorial_general_02.png
[3]: ./media/clever-tutorial/tutorial_general_03.png
[4]: ./media/clever-tutorial/tutorial_general_04.png

[100]: ./media/clever-tutorial/tutorial_general_100.png

[200]: ./media/clever-tutorial/tutorial_general_200.png
[201]: ./media/clever-tutorial/tutorial_general_201.png
[202]: ./media/clever-tutorial/tutorial_general_202.png
[203]: ./media/clever-tutorial/tutorial_general_203.png

