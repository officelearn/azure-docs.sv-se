---
title: 'Självstudier: Azure Active Directory-integrering med en Zoho | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och en Zoho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 89f8df4e14bb93b1594cbb492a29c16b58d18785
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35963952"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Självstudier: Azure Active Directory-integrering med en Zoho

Lär dig hur du integrerar en Zoho med Azure Active Directory (AD Azure) i den här självstudiekursen.

Integrera en Zoho med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till en Zoho.
- Du kan aktivera användarna att automatiskt hämta loggat in på en Zoho (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med en Zoho, behöver du följande:

- En Azure AD-prenumeration
- En Zoho en enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till en Zoho från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-zoho-one-from-the-gallery"></a>Att lägga till en Zoho från galleriet
Du måste lägga till en Zoho från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av en Zoho i Azure AD.

**Om du vill lägga till en Zoho från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Zoho en**väljer **Zoho en** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![En Zoho i resultatlistan](./media/zohoone-tutorial/tutorial_zohoone_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Zoho en baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Zoho en motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i en Zoho upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med en Zoho, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en Zoho en testanvändare](#create-a-zoho-one-test-user)**  – du har en motsvarighet för Britta Simon i Zoho en som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Zoho ett program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med en Zoho:**

1. I Azure-portalen på den **Zoho en** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/zohoone-tutorial/tutorial_zohoone_samlbase.png)

3. På den **Zoho en domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Zoho en domän med enkel inloggning information](./media/zohoone-tutorial/tutorial_zohoone_url.png)

    a. I den **identifierare (enhets-ID)** textruta, ange ett URL-Adressen: `one.zoho.com`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    c. Kontrollera **visa avancerade inställningar för URL: en**.

    d. I den **Relay tillstånd** textruta, ange ett URL-Adressen:`https://one.zoho.com`

4. Om du vill konfigurera programmet i **SP** initierade läge utföra följande steg:

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com`
     
    > [!NOTE] 
    > Den föregående **Reply URL** och **inloggnings-URL** värdet är inte verkliga. Du uppdaterar värdet med det faktiska Reply URL och inloggnings-URL som beskrivs senare i självstudierna. 

5. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/zohoone-tutorial/tutorial_zohoone_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/zohoone-tutorial/tutorial_general_400.png)
    
7. På den **Zoho en konfiguration** klickar du på **konfigurera Zoho en** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out Webbadressen och SAML enkel inloggning Service** från den **Snabbreferens avsnitt.**

    ![Zoho en konfiguration](./media/zohoone-tutorial/tutorial_zohoone_configure.png) 

8. I en annan webbläsarfönster loggar du in på webbplatsen Zoho ett företag som administratör.

9. På den **organisation** flik, klicka på **installationsprogrammet** under **SAML-autentisering**.

    ![Zoho en organisations](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

10. Utför följande steg på popup-sidan:

    ![En Zoho sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. I den **inloggning URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.

    b. I den **Sign-out URL** textruta klistra in värdet för **Sign-Out URL**, som du har kopierat från Azure-portalen.

    c. Klicka på **Bläddra** att överföra den **certifikat (Base64)** som du har hämtat från Azure-portalen.

    d. Klicka på **Spara**.

11. När du har sparat inställningarna för SAML-autentisering, kopiera den **SAML-Identfier** värde och använda det här värdet i den **Reply URL** i Azure-portalen under **Zoho en domän och URL: er** avsnittet.

    ![En Zoho saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

12. Gå till den **domäner** och klicka sedan på **Lägg till domän**.

    ![Zoho en domän](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

13. På den **Lägg till domän** utför följande steg:

    ![En Zoho Lägg till domän](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. I den **domännamn** textruta Ange domän som **contoso.com**.

    b. Klicka på **Lägg till**.

    >[!Note]
    >När du lägger till domänen följer [dessa](https://www.zoho.com/one/help/admin-guide/domain-verification.html) steg för att verifiera din domän. När domänen verfified kan använda ditt domännamn i **inloggnings-URL** i **Zoho en domän och URL: er** avsnitt i Azure-portalen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/zohoone-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/zohoone-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/zohoone-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/zohoone-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-zoho-one-test-user"></a>Skapa en Zoho en testanvändare

Om du vill aktivera Azure AD-användare kan logga in på en Zoho, måste de etableras i en Zoho. I en Zoho är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på Zoho en som en administratör.

2. På den **användare** fliken, klicka på **användaren logotypen**.

    ![Zoho en användare](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. På den **Lägg till användare** utför följande steg:

    ![En Zoho lägga till användare](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. I **namn** text anger du namnet på användaren som **Britta simon**.
    
    b. I **e-postadress** text Ange e-postadressen för användaren som **brittasimon@contoso.com**.

    >[!Note]
    >Välj den verifierade domänen i domänlistan.

    c. Klicka på **Lägg till**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till en Zoho.

![Tilldela rollen][200] 

**Om du vill tilldela en Zoho Britta Simon, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Zoho en**.

    ![Zoho en länk i listan med program](./media/zohoone-tutorial/tutorial_zohoone_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på en Zoho panelen på åtkomstpanelen du bör få automatiskt loggat in på ditt Zoho ett program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zohoone-tutorial/tutorial_general_01.png
[2]: ./media/zohoone-tutorial/tutorial_general_02.png
[3]: ./media/zohoone-tutorial/tutorial_general_03.png
[4]: ./media/zohoone-tutorial/tutorial_general_04.png

[100]: ./media/zohoone-tutorial/tutorial_general_100.png

[200]: ./media/zohoone-tutorial/tutorial_general_200.png
[201]: ./media/zohoone-tutorial/tutorial_general_201.png
[202]: ./media/zohoone-tutorial/tutorial_general_202.png
[203]: ./media/zohoone-tutorial/tutorial_general_203.png

