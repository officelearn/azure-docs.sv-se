---
title: 'Självstudier: Azure Active Directory-integrering med Proxyclick | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: jeedes
ms.openlocfilehash: 3fb6c034df38486dfefd0ffccc6e05db32d9087d
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35964239"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Självstudier: Azure Active Directory-integrering med Proxyclick

I kursen får lära du att integrera Proxyclick med Azure Active Directory (AD Azure).

Integrera Proxyclick med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Proxyclick.
- Du kan aktivera användarna att automatiskt hämta loggat in på Proxyclick (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Proxyclick, behöver du följande:

- En Azure AD-prenumeration
- En Proxyclick enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Proxyclick från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-proxyclick-from-the-gallery"></a>Att lägga till Proxyclick från galleriet
Du måste lägga till Proxyclick från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Proxyclick i Azure AD.

**Utför följande steg för att lägga till Proxyclick från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Proxyclick**väljer **Proxyclick** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Proxyclick i resultatlistan](./media/proxyclick-tutorial/tutorial_proxyclick_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Proxyclick baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Proxyclick motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Proxyclick upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Proxyclick, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Proxyclick](#create-a-proxyclick-test-user)**  – du har en motsvarighet för Britta Simon i Proxyclick som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Proxyclick program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Proxyclick:**

1. I Azure-portalen på den **Proxyclick** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning dialogrutan](./media/proxyclick-tutorial/tutorial_proxyclick_samlbase.png)

3. På den **Proxyclick domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Proxyclick domän med enkel inloggning information](./media/proxyclick-tutorial/tutorial_proxyclick_url2.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://saml.proxyclick.com/init/<companyId>`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://saml.proxyclick.com/consume/<companyId>`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Proxyclick domän med enkel inloggning information](./media/proxyclick-tutorial/tutorial_proxyclick_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Dessa värden uppdateras med faktiska identifierare, Reply URL och inloggnings-URL, som beskrivs senare i självstudierna.

5. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/proxyclick-tutorial/tutorial_proxyclick_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/proxyclick-tutorial/tutorial_general_400.png)

7. På den **Proxyclick Configuration** klickar du på **konfigurera Proxyclick** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Proxyclick konfiguration](./media/proxyclick-tutorial/tutorial_proxyclick_configure.png)

8. I en annan webbläsarfönster loggar du in på webbplatsen Proxyclick företag som administratör.

9. Välj **konto & inställningar**.

    ![Proxyclick konfiguration](./media/proxyclick-tutorial/configure1.png)

10. Rulla ned till den **INTEGRERINGAR** och välj **SAML**.

    ![Proxyclick konfiguration](./media/proxyclick-tutorial/configure2.png)

11. I den **SAML** avsnittet, utför följande steg:

    ![Proxyclick konfiguration](./media/proxyclick-tutorial/configure3.png)

    a. Kopiera **konsument-URL för SAML** värdet och klistrar in det i **Reply URL** TextBox-kontroll i **Proxyclick domän och URL: er** avsnitt på Azure-portalen.

    b. Kopiera **SAML SSO omdirigerings-URL** värdet och klistrar in det i **inloggning URL** och **identifierare** textrutor i **Proxyclick domän och URL: er** avsnitt på Azure-portalen.

    c. Välj **metod för SAML-begäran** som **HTTP-omdirigering**.

    d. I den **utfärdaren** textruta klistra in värdet för **SAML enhets-ID** -värde som du har kopierat från Azure-portalen.

    e. I den **slutpunkts-URL för SAML 2.0** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** kopieras från Azure-portalen.

    f. Öppna filen hämtat certifikat från Azure-portalen i anteckningar och klistra in det i den **certifikat** textruta.

    g. Klicka på **spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/proxyclick-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/proxyclick-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/proxyclick-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/proxyclick-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.

### <a name="create-a-proxyclick-test-user"></a>Skapa en testanvändare Proxyclick

Om du vill aktivera Azure AD-användare kan logga in på Proxyclick etableras de i Proxyclick. När det gäller Proxyclick är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på webbplatsen Proxyclick företag som administratör.

2. Klicka på **kollegor** från det övre navigeringsfältet.

    ![Lägga till medarbetare](./media/proxyclick-tutorial/user1.png)

3. Klicka på **lägga till en kollega**

    ![Lägga till medarbetare](./media/proxyclick-tutorial/user2.png)

4. I den **lägga till en kollega** avsnittet, utför följande steg:

    ![Lägga till medarbetare](./media/proxyclick-tutorial/user3.png)

    a. I den **e-post** textruta typen e-postadressen för användaren som **brittasimon@contoso.com**.

    b. I den **Förnamn** textruta, ange först namnet på användaren som Britta.

    c. I den **efternamn** textruta anger efternamn för användaren som Simon.

    d. Klicka på **lägga till användare**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Proxyclick.

![Tilldela rollen][200]

**Om du vill tilldela Proxyclick Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Proxyclick**.

    ![Länken Proxyclick i listan med program](./media/proxyclick-tutorial/tutorial_proxyclick_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Proxyclick på åtkomstpanelen du bör få automatiskt loggat in på ditt Proxyclick program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/proxyclick-tutorial/tutorial_general_01.png
[2]: ./media/proxyclick-tutorial/tutorial_general_02.png
[3]: ./media/proxyclick-tutorial/tutorial_general_03.png
[4]: ./media/proxyclick-tutorial/tutorial_general_04.png

[100]: ./media/proxyclick-tutorial/tutorial_general_100.png

[200]: ./media/proxyclick-tutorial/tutorial_general_200.png
[201]: ./media/proxyclick-tutorial/tutorial_general_201.png
[202]: ./media/proxyclick-tutorial/tutorial_general_202.png
[203]: ./media/proxyclick-tutorial/tutorial_general_203.png

