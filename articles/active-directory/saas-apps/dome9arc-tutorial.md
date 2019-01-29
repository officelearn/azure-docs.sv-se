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
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2018
ms.author: jeedes
ms.openlocfilehash: a313acecf0660e527508f28e1ea86485996cc4f9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191405"
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Självstudier: Azure Active Directory-integrering med Dome9 båge

I den här självstudien får du lära dig hur du integrerar Dome9 båge med Azure Active Directory (AD Azure).

Integrera Dome9 båge med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Dome9 båge.
- Du kan aktivera användarna att automatiskt få loggat in på Dome9 båge (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Dome9 båge, behöver du följande objekt:

- En Azure AD-prenumeration
- En Dome9 båge enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Dome9 båge från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-dome9-arc-from-the-gallery"></a>Att lägga till Dome9 båge från galleriet

Om du vill konfigurera integreringen av Dome9 bågen till Azure AD, som du behöver lägga till Dome9 båge från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Dome9 båge från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Dome9 båge**väljer **Dome9 båge** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Dome9 båge i resultatlistan](./media/dome9arc-tutorial/tutorial_dome9arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Dome9 båge baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Dome9 bågen till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Dome9 bågen upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Dome9 båge, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare Dome9 båge](#create-a-dome9-arc-test-user)**  – du har en motsvarighet för Britta Simon i Dome9 båge som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Dome9 båge.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Dome9 båge:**

1. I Azure-portalen på den **Dome9 båge** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/dome9arc-tutorial/tutorial_dome9arc_samlbase.png)

3. På den **Dome9 båge domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Dome9 båge domän och URL: er med enkel inloggning för information](./media/dome9arc-tutorial/tutorial_dome9arc_url.png)

    a. I den **identifierare** textrutan anger du URL: `https://secure.dome9.com/`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Du väljer ditt företag namn-värde i administratörsportalen dome9, som beskrivs senare i självstudien.

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Dome9 båge domän och URL: er med enkel inloggning för information](./media/dome9arc-tutorial/tutorial_dome9arc_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://secure.dome9.com/sso/saml/<yourcompanyname>`
 
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Kontakta [Dome9 båge klienten supportteamet](https://dome9.com/about/contact-us/) att hämta dessa värden. 

5. Dome9 båge programvara förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Följande skärmbild visar ett exempel på detta.

    ![Konfigurera enkel inloggning attb](./media/dome9arc-tutorial/tutorial_dome9arc_attribute.png)

6. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn  | Attributvärde | 
    | --------------- | --------------- | 
    | memberof | user.assignedroles |
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning för Lägg till attb](./media/dome9arc-tutorial/tutorial_dome9_04.png)

    ![Konfigurera enkel inloggning redigera attb](./media/dome9arc-tutorial/tutorial_attribute_05.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Klicka på **OK**.
    
    > [!NOTE]
    > Se detta [länk](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) på hur du konfigurerar och konfigurera roller för programmet.

7. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/dome9arc-tutorial/tutorial_dome9arc_certificate.png) 

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/dome9arc-tutorial/tutorial_general_400.png)

9. På den **Dome9 båge Configuration** klickar du på **konfigurera Dome9 båge** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Dome9 båge konfiguration](./media/dome9arc-tutorial/tutorial_dome9arc_configure.png) 

10. Logga in på webbplatsen Dome9 båge företag som en administratör i ett annat webbläsarfönster.

11. Klicka på den **profilinställningar** i övre högra hörnet och sedan på **kontoinställningar**. 

    ![Dome9 båge konfiguration](./media/dome9arc-tutorial/configure1.png)

12. Gå till **SSO** och klicka sedan på **aktivera**.

    ![Dome9 båge konfiguration](./media/dome9arc-tutorial/configure2.png)

13. Utför följande steg i konfigurationsavsnittet för enkel inloggning:

    ![Dome9 båge konfiguration](./media/dome9arc-tutorial/configure3.png)

    a. Ange företagets namn i den **konto-ID** textrutan. Det här värdet som ska användas i svaret från URL: en som nämns i Azure portal URL-avsnittet.

    b. I den **utfärdare** textrutan klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure portal.

    c. I den **slutpunkts-url för IDP: N** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure portal.

    d. Öppna din hämtade Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** textrutan.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/dome9arc-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/dome9arc-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/dome9arc-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/dome9arc-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-dome9-arc-test-user"></a>Skapa en testanvändare Dome9 båge

Om du vill aktivera Azure AD-användare att logga in till Dome9 båge, måste de etableras i program. Dome9 båge stöder just-in-time-etablering men för att det ska fungera korrekt, användaren måste välja specifika **rollen** och tilldela den till användaren.

   >[!Note]
   >För **rollen** skapande och annan information Kontakta [Dome9 båge klienten supportteamet](https://dome9.com/about/contact-us/).

**Om du vill konfigurera ett användarkonto manuellt utför du följande steg:**

1. Logga in på webbplatsen Dome9 båge företagets som administratör.

2. Klicka på den **användare och roller** och klicka sedan på **användare**.

    ![Lägga till medarbetare](./media/dome9arc-tutorial/user1.png)

3. Klicka på **LÄGG TILL ANVÄNDARE**.

    ![Lägga till medarbetare](./media/dome9arc-tutorial/user2.png)

4. I den **Create User** avsnittet, utför följande steg:

    ![Lägga till medarbetare](./media/dome9arc-tutorial/user3.png)

    a. I den **e-post** textrutan typ e-postmeddelandet av användare som Brittasimon@contoso.com.

    b. I den **Förnamn** textrutan Ange först namnet på användaren som Britta.

    c. I den **efternamn** textrutan Skriv Efternamn för användaren som Simon.

    d. Kontrollera **SSO användaren** som **på**.

    e. Klicka på **SKAPA**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Dome9 båge.

![Tilldela rollen][200] 

**Om du vill tilldela Dome9 båge Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Dome9 båge**.

    ![Länken Dome9 båge i listan med program](./media/dome9arc-tutorial/tutorial_dome9arc_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Dome9 båge i åtkomstpanelen du bör få automatiskt loggat in på ditt Dome9 båge-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/dome9arc-tutorial/tutorial_general_01.png
[2]: ./media/dome9arc-tutorial/tutorial_general_02.png
[3]: ./media/dome9arc-tutorial/tutorial_general_03.png
[4]: ./media/dome9arc-tutorial/tutorial_general_04.png

[100]: ./media/dome9arc-tutorial/tutorial_general_100.png

[200]: ./media/dome9arc-tutorial/tutorial_general_200.png
[201]: ./media/dome9arc-tutorial/tutorial_general_201.png
[202]: ./media/dome9arc-tutorial/tutorial_general_202.png
[203]: ./media/dome9arc-tutorial/tutorial_general_203.png

