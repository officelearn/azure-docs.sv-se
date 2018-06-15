---
title: 'Självstudier: Azure Active Directory-integrering med uppfattning USA (icke-UltiPro) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och uppfattning USA (icke-UltiPro).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: da0529897bb02745a2346f6a0282be86923468ba
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350125"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Självstudier: Azure Active Directory-integrering med uppfattning USA (icke-UltiPro)

I kursen får lära du att integrera uppfattning USA (icke-UltiPro) med Azure Active Directory (AD Azure).

Integrera uppfattning USA (icke-UltiPro) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till uppfattning USA (icke-UltiPro).
- Du kan aktivera användarna att automatiskt hämta inloggade till uppfattning USA (icke-UltiPro) (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med uppfattning USA (icke-UltiPro) behöver du följande:

- En Azure AD-prenumeration
- En uppfattning USA (icke-UltiPro) enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till uppfattning USA (icke-UltiPro) från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Att lägga till uppfattning USA (icke-UltiPro) från galleriet
För att konfigurera integrering av uppfattning USA (icke-UltiPro) till Azure AD, måste du lägga till uppfattning USA (icke-UltiPro) från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till uppfattning USA (icke-UltiPro) från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **uppfattning USA (icke-UltiPro)** väljer **uppfattning USA (icke-UltiPro)** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Uppfattning USA (icke-UltiPro) i resultatlistan](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med uppfattning USA (icke-UltiPro) baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i uppfattning USA (icke-UltiPro) till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i uppfattning USA (icke-UltiPro) upprättas.

I uppfattning USA (icke-UltiPro), tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med uppfattning USA (icke-UltiPro), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare uppfattning USA (icke-UltiPro)](#create-a-perception-united-states-non-ultipro-test-user)**  – du har en motsvarighet för Britta Simon i uppfattning USA (icke-UltiPro) som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet uppfattning USA (icke-UltiPro).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med uppfattning USA (icke-UltiPro):**

1. I Azure-portalen på den **uppfattning USA (icke-UltiPro)** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_samlbase.png)

3. På den **domänen uppfattning USA (icke-UltiPro) och URL: er** avsnittet, utför följande steg:

    ![URL: er och uppfattning USA (icke-UltiPro)-domän med enkel inloggning information](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_url.png)

    a. I den **identifierare** textruta anger du URL: `https://perception.kanjoya.com/sp`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    > [!NOTE] 
    > Värdet är inte verkliga. Du uppdaterar värdet med faktiska Reply-URL, vilket beskrivs senare i självstudierna.
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_400.png)

6. På den **uppfattning USA (icke-UltiPro) konfiguration** klickar du på **konfigurera uppfattning USA (icke-UltiPro)** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID** från den **Snabbreferens avsnitt.**

    a. Den **uppfattning USA (icke-UltiPro)** programmet kräver den **SAML enhets-ID** -värde som du har kopierat till att uri-kodad. Om du vill hämta värdet för uri-kodad, använder du följande länk:**http://www.url-encode-decode.com/**.

    b. När du har skaffat uri kodat värde kombinera det med den **Reply URL** som anges nedan -

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    c. Klistra in ovanstående värde i den **Reply URL** TextBox-kontroll i **domänen uppfattning USA (icke-UltiPro) och URL: er** avsnitt.

    ![Uppfattning USA (icke-UltiPro) konfiguration](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_configure.png) 

7. I ett nytt webbläsarfönster inloggning till webbplatsen uppfattning USA (icke-UltiPro) företag som administratör.

8. I verktygsfältet klickar du på **kontoinställningar**.

    ![Uppfattning USA (icke-UltiPro) användare](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

9. På den **kontoinställningar** utför följande steg:

    ![Uppfattning USA (icke-UltiPro) användare](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. I den **företagsnamn** textruta skriver du namnet på den **företagets**.
    
    b. I den **kontonamn** textruta skriver du namnet på den **konto**.

    c. I **standard svar till e-post** text skriver det giltiga **e-post**.

    d. Välj **SSO identitetsleverantör** som **SAML 2.0**.

10. På den **SSO Configuration** utför följande steg:

    ![Uppfattning USA (icke-UltiPro) SSOConfig](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Välj **SAML NameID typen** som **e-post**.

    b. I den **SSO Konfigurationsnamnet** textruta skriver du namnet på din **Configuration**.
    
    c. I **identitet providernamn** textruta klistra in värdet för **SAML enhets-ID**, som du har kopierat från Azure-portalen. 

    d. I **SAML domän textruta**, ange domänen som **@contoso.com**.

    e. Klicka på **överför igen** att överföra den **XML-Metadata för** fil.

    f. Klicka på **uppdatering**.


> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-a-perception-united-states-non-ultipro-test-user"></a>Skapa en testanvändare uppfattning USA (icke-UltiPro)

I det här avsnittet skapar du en användare som kallas Britta Simon uppfattning United States (icke-UltiPro). Arbeta med [uppfattning USA (icke-UltiPro) supportteamet](http://www.ultimatesoftware.com/Contact/ContactUs) att lägga till användare i USA för uppfattning (icke-UltiPro)-plattformen.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till uppfattning USA (icke-UltiPro).

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon till uppfattning USA (icke-UltiPro), utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **uppfattning USA (icke-UltiPro)**.

    ![Länken uppfattning USA (icke-UltiPro) i listan med program](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen uppfattning USA (icke-UltiPro) på åtkomstpanelen du bör få automatiskt loggat in i tillämpningsprogrammet uppfattning USA (icke-UltiPro).
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_203.png

