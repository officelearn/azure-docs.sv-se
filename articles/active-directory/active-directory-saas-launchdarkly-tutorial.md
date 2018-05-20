---
title: 'Självstudier: Azure Active Directory-integrering med LaunchDarkly | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LaunchDarkly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0e9cb37e-16bf-493b-bfc8-8d9840545a1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: f6611009ae10bf7a0d5c7f20f3ad26e9d41e456e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Självstudier: Azure Active Directory-integrering med LaunchDarkly

I kursen får lära du att integrera LaunchDarkly med Azure Active Directory (AD Azure).

Integrera LaunchDarkly med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till LaunchDarkly.
- Du kan aktivera användarna att automatiskt hämta loggat in på LaunchDarkly (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med LaunchDarkly, behöver du följande:

- En Azure AD-prenumeration
- En LaunchDarkly enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till LaunchDarkly från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-launchdarkly-from-the-gallery"></a>Att lägga till LaunchDarkly från galleriet
Du måste lägga till LaunchDarkly från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av LaunchDarkly i Azure AD.

**Utför följande steg för att lägga till LaunchDarkly från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **LaunchDarkly**väljer **LaunchDarkly** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![LaunchDarkly i resultatlistan](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med LaunchDarkly baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i LaunchDarkly motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i LaunchDarkly upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med LaunchDarkly, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare LaunchDarkly](#create-a-launchdarkly-test-user)**  – du har en motsvarighet för Britta Simon i LaunchDarkly som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt LaunchDarkly program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med LaunchDarkly:**

1. I Azure-portalen på den **LaunchDarkly** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning dialogrutan](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_samlbase.png)

3. På den **LaunchDarkly domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och LaunchDarkly domän med enkel inloggning information](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_url.png)

    a. I den **identifierare (enhets-ID)** textruta anger du URL: `app.launchdarkly.com`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`
    
    > [!NOTE]
    > Reply URL-värdet är inte verkliga. Du uppdaterar värdet med faktiska Reply-URL, vilket beskrivs senare i självstudierna.

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och LaunchDarkly domän med enkel inloggning information](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_url1.png)

    I den **inloggnings-URL** textruta anger du URL: `https://app.launchdarkly.com`

5. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_400.png)
    
7. På den **LaunchDarkly Configuration** klickar du på **konfigurera LaunchDarkly** att öppna **konfigurera inloggning** fönster. Kopiera den **inloggning tjänst-URL för enkel** från den **Snabbreferens avsnitt.**

    ![LaunchDarkly konfiguration](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_configure.png)

8. Logga in på webbplatsen LaunchDarkly företag som en administratör i en annan webbläsarfönster.

9. Välj **kontoinställningar** från den vänstra navigeringsfönstret.

    ![LaunchDarkly konfiguration](./media/active-directory-saas-launchdarkly-tutorial/configure1.png)

10. Klicka på **säkerhet** fliken.

    ![LaunchDarkly konfiguration](./media/active-directory-saas-launchdarkly-tutorial/configure2.png)

11. Klicka på **aktivera enkel inloggning** och sedan **redigera SAML-konfiguration**.

    ![LaunchDarkly konfiguration](./media/active-directory-saas-launchdarkly-tutorial/configure3.png)

12. På den **redigera konfigurationen SAML** avsnittet, utför följande steg:

    ![LaunchDarkly konfiguration](./media/active-directory-saas-launchdarkly-tutorial/configure4.png)

    a. Kopiera den **URL för SAML konsumenten** för din instans och klistra in den i svars-URL: en textruta i **LaunchDarkly domän och URL: er** avsnitt på Azure-portalen.

    b. I den **inloggnings-URL** textruta klistra in den **inloggning tjänst-URL för enkel** -värde som du har kopierat från Azure-portalen.

    c. Öppna hämtat certifikat från Azure-portalen i anteckningar, kopiera innehållet och klistrar in det i den **X.509-certifikat** rutan eller så kan du direkt överföra certifikatet genom att klicka på den **ladda upp en**.

    d. Klicka på **Spara**

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.

### <a name="create-a-launchdarkly-test-user"></a>Skapa en testanvändare LaunchDarkly

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i LaunchDarkly. LaunchDarkly stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt LaunchDarkly om den inte finns.
>[!Note]
>Om du behöver skapa en användare manuellt Kontakta [LaunchDarkly klienten supportteamet](mailto:support@launchdarkly.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LaunchDarkly.

![Tilldela rollen][200] 

**Om du vill tilldela LaunchDarkly Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **LaunchDarkly**.

    ![Länken LaunchDarkly i listan med program](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen LaunchDarkly på åtkomstpanelen du bör få automatiskt loggat in på ditt LaunchDarkly program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_203.png

