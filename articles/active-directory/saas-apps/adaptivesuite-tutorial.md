---
title: 'Självstudier: Azure Active Directory-integrering med anpassningsbar insikter | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och anpassningsbar insikter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: jeedes
ms.openlocfilehash: 307c3cf258a74d1ddfb409f0d5b22d9e1fd6bf4b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213829"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Självstudier: Azure Active Directory-integrering med anpassningsbar insikter

I kursen får lära du att integrera anpassningsbar insikter med Azure Active Directory (AD Azure).

Integrera anpassningsbar insikter med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har tillgång till anpassningsbara insikter
- Du kan aktivera användarna att automatiskt hämta loggat in på anpassningsbar insikter (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med anpassningsbar insikter, behöver du följande:

- En Azure AD-prenumeration
- En anpassningsbar insikter enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till anpassad insikter från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-adaptive-insights-from-the-gallery"></a>Att lägga till anpassad insikter från galleriet
Du måste lägga till anpassningsbar insikter från galleriet i listan över hanterade SaaS-appar för att konfigurera anpassningsbar insikter om Azure AD-integrering.

**Utför följande steg för att lägga till anpassad insikter från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]

3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **anpassningsbar insikter**väljer **anpassningsbar insikter** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med anpassningsbar analyser baserade på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i anpassningsbar insikter motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i anpassningsbar insikter upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med anpassningsbar insikter, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en anpassningsbar insikter testanvändare](#creating-an-adaptive-insights-test-user)**  – du har en motsvarighet för Britta Simon i anpassningsbar insikter som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet anpassningsbar insikter.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med anpassningsbar insikter:**

1. I Azure-portalen på den **anpassningsbar insikter** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_samlbase.png)

3. På den **anpassningsbar insikter domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    a. I den **identifierare (enhets-ID)** textruta Skriv en URL med följande mönster: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > Du kan hämta identifierare (enhets-ID) och Reply URL-värden från anpassningsbar insikter **SAML SSO inställningar** sidan.

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/adaptivesuite-tutorial/tutorial_general_400.png)

6. På den **anpassningsbar Insights-konfigurationen** klickar du på **konfigurera anpassningsbar insikter** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_configure.png) 

7. I en annan webbläsarfönster loggar du in på webbplatsen anpassningsbar insikter företag som administratör.

8. Gå till **Admin**.

    ![Admin](./media/adaptivesuite-tutorial/IC805644.png "Admin")

9. I den **användare och roller** klickar du på **hantera inställningar för SAML SSO**.

    ![Hantera inställningar för SAML SSO](./media/adaptivesuite-tutorial/IC805645.png "hantera SAML SSO-inställningar")

10. På den **SAML SSO inställningar** utför följande steg:

    ![Inställningar för SAML SSO](./media/adaptivesuite-tutorial/IC805646.png "SAML SSO-inställningar")

    a. I den **identitet providernamn** textruta, ange ett namn för din konfiguration.

    b. Klistra in den **SAML enhets-ID** kopieras värdet från Azure-portalen i den **identitetsleverantör enhets-ID** textruta.

    c. Klistra in den **SAML inloggning tjänst-URL för enkel** kopieras värdet från Azure-portalen i den **identitetsleverantör SSO URL** textruta.

    d. Klistra in den **SAML enkel inloggning Tjänstwebbadress** kopieras värdet från Azure-portalen i den **anpassad logga ut URL** textruta.

    e. Om du vill överföra din hämtat certifikat klickar du på **Välj fil**.

    f. Välj följande, för:

    * **Användar-id för SAML**väljer **användarens användarnamn för anpassningsbar insikter**.

    * **Id för SAML Användarplats**väljer **användar-id i NameID ämne**.

    * **Format för SAML-NameID**väljer **e-postadress**.

    * **Aktivera SAML**väljer **Tillåt SAML SSO och anpassningsbar insikter direktinloggning**.

    g. Kopiera **anpassningsbar insikter SSO URL** och klistra in i den **identifierare (enhets-ID)** och **Reply URL** textrutor i den **anpassningsbar insikter domän och URL: er** avsnitt i Azure-portalen.

    h. Klicka på **Spara**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/adaptivesuite-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.

    ![Skapa en testanvändare i Azure AD](./media/adaptivesuite-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.

    ![Skapa en testanvändare i Azure AD](./media/adaptivesuite-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en testanvändare i Azure AD](./media/adaptivesuite-tutorial/create_aaduser_04.png)

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-an-adaptive-insights-test-user"></a>Skapa en anpassningsbar insikter testanvändare

Om du vill aktivera Azure AD-användare kan logga in på anpassningsbar insikter etableras de till anpassningsbar insikter. Anpassningsbar insikter är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användaretablering:** 

1. Logga in på ditt **anpassningsbar insikter** företagets webbplats som administratör.
2. Gå till **Admin**.

   ![Admin](./media/adaptivesuite-tutorial/IC805644.png "Admin")
3. I den **användare och roller** klickar du på **Lägg till användare**.

   ![Lägg till användare](./media/adaptivesuite-tutorial/IC805648.png "lägga till användare")
4. I den **ny användare** avsnittet, utför följande steg:

   ![Skicka](./media/adaptivesuite-tutorial/IC805649.png "skicka")

   a. Typ av **namn**, **inloggning**, **e-post**, **lösenord** av en giltig Azure Active Directory-användare som du vill etablera i relaterade textrutor.

   b. Välj en **rollen**.

   c. Klicka på **skicka**.

>[!NOTE]
>Du kan använda något annat anpassningsbar insikter användarens konto skapas verktyg eller API: er som tillhandahålls av anpassningsbar insikter etablera AAD-användarkonton.
>

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till anpassningsbar insikter.

![Tilldela användare][200]

**Om du vill tilldela anpassningsbar insikter Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201]

2. Välj i listan med program **anpassningsbar insikter**.

    ![Konfigurera enkel inloggning](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_app.png)

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Microsoft Azure AD Single Sign-On-konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen anpassningsbar insikter på åtkomstpanelen du ska hämta automatiskt loggat in på ditt program för anpassningsbar insikter.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/adaptivesuite-tutorial/tutorial_general_01.png
[2]: ./media/adaptivesuite-tutorial/tutorial_general_02.png
[3]: ./media/adaptivesuite-tutorial/tutorial_general_03.png
[4]: ./media/adaptivesuite-tutorial/tutorial_general_04.png

[100]: ./media/adaptivesuite-tutorial/tutorial_general_100.png

[200]: ./media/adaptivesuite-tutorial/tutorial_general_200.png
[201]: ./media/adaptivesuite-tutorial/tutorial_general_201.png
[202]: ./media/adaptivesuite-tutorial/tutorial_general_202.png
[203]: ./media/adaptivesuite-tutorial/tutorial_general_203.png
