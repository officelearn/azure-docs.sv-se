---
title: 'Självstudier: Azure Active Directory-integrering med Dome9 båge | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Dome9 båge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: de164f6a439fd68b4746dc96c43ff5cb0740ffe8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Självstudier: Azure Active Directory-integrering med Dome9 båge

I kursen får lära du att integrera Dome9 båge med Azure Active Directory (AD Azure).

Integrera Dome9 båge med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Dome9 båge.
- Du kan aktivera användarna att automatiskt hämta loggat in på Dome9 båge (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Dome9 båge, behöver du följande:

- En Azure AD-prenumeration
- En Dome9 båge enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Dome9 båge från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-dome9-arc-from-the-gallery"></a>Att lägga till Dome9 båge från galleriet
Du måste lägga till Dome9 båge från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Dome9 båge i Azure AD.

**Utför följande steg för att lägga till Dome9 båge från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Dome9 båge**väljer **Dome9 båge** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Dome9 båge i resultatlistan](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Dome9 båge baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Dome9 båge motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Dome9 bågen upprättas.

I Dome9 Arc, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Dome9 båge, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Dome9 båge](#create-a-dome9-arc-test-user)**  – har en motsvarighet för Britta Simon Dome9 båge som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Dome9 båge.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Dome9 båge:**

1. I Azure-portalen på den **Dome9 båge** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_samlbase.png)

3. På den **Dome9 båge domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Dome9 båge domän med enkel inloggning information](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_url.png)

    a. I den **identifierare** textruta anger du URL: `https://secure.dome9.com/`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Du väljer din företagets namn-värde på administrationsportalen för dome9 som beskrivs senare i självstudierna.

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Dome9 båge domän med enkel inloggning information](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://secure.dome9.com/sso/saml/<yourcompanyname>`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska Reply URL och inloggnings-URL. Kontakta [Dome9 båge klienten supportteamet](https://dome9.com/about/contact-us/) att hämta dessa värden. 

5. Dome9 båge program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.

    ![Konfigurera enkel inloggning attb](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_attribute.png)

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:
    
    | Attributnamn  | Attributvärde | 
    | --------------- | --------------- | 
    | memberof | User.assignedroles | 
    
    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning lägga till attb](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9_04.png)

    ![Konfigurera enkel inloggning redigera attb](./media/active-directory-saas-dome9arc-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **OK**.

7. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_certificate.png) 

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-dome9arc-tutorial/tutorial_general_400.png)
    
9. På den **Dome9 båge Configuration** klickar du på **konfigurera Dome9 båge** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Dome9 båge konfiguration](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_configure.png) 

10. Logga in på webbplatsen Dome9 bågen företaget som en administratör i en annan webbläsarfönster.

11. Klicka på den **profilinställningar** i övre högra hörnet och klicka sedan på **kontoinställningar**. 

    ![Dome9 båge konfiguration](./media/active-directory-saas-dome9arc-tutorial/configure1.png)

12. Gå till **SSO** och klicka sedan på **aktivera**.

    ![Dome9 båge konfiguration](./media/active-directory-saas-dome9arc-tutorial/configure2.png)

13. Utför följande steg i konfigurationsavsnittet enkel inloggning:

    ![Dome9 båge konfiguration](./media/active-directory-saas-dome9arc-tutorial/configure3.png)

    a. Ange företagets namn i den **konto-ID** textruta. Det här värdet som ska användas i svaret url som anges i Azure portal URL-avsnittet.

    b. I den **utfärdaren** textruta klistra in värdet för **SAML enhets-ID**, som du har kopierat formuläret Azure-portalen.

    c. I den **Idp slutpunkts-url** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat formuläret Azure-portalen.

    d. Öppna din hämtade Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** textruta.

    e. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-dome9-arc-test-user"></a>Skapa en testanvändare Dome9 båge

Om du vill aktivera Azure AD-användare kan logga in till Dome9 båge etableras de i programmet. Dome9 båge stöder just-in-time-etablering men för att för att fungera korrekt, användare måste du markera viss **rollen** och tilldela samma till användaren.

   >[!Note] 
   >För **rollen** skapas och annan information Kontakta [Dome9 båge klienten supportteamet](https://dome9.com/about/contact-us/).

**Om du vill konfigurera ett användarkonto manuellt utför du följande steg:**

1. Logga in på webbplatsen Dome9 bågen företaget som administratör.

2. Klicka på den **användare och roller** och klicka sedan på **användare**.

    ![Lägga till medarbetare](./media/active-directory-saas-dome9arc-tutorial/user1.png)

3. Klicka på **Lägg till användare**.

    ![Lägga till medarbetare](./media/active-directory-saas-dome9arc-tutorial/user2.png)

4. I den **skapa användare** avsnittet, utför följande steg:
    
    ![Lägga till medarbetare](./media/active-directory-saas-dome9arc-tutorial/user3.png)

    a. I den **e-post** textruta, ange den e-posten för användare som Brittasimon@contoso.com.

    b. I den **Förnamn** textruta första typnamnet för användaren som Britta.

    c. I den **efternamn** textruta Skriv Efternamn för användaren som Simon.

    d. Se **SSO användaren** som **på**.

    e. Klicka på **skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Dome9 båge.

![Tilldela rollen][200] 

**Om du vill tilldela Dome9 båge Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Dome9 båge**.

    ![Länken Dome9 båge i listan med program](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Dome9 båge på åtkomstpanelen du ska hämta automatiskt loggat in på ditt Dome9 båge program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_203.png

