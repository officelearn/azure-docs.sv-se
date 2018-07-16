---
title: 'Självstudier: Azure Active Directory-integrering med Mimecast personliga Portal | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mimecast personliga Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: jeedes
ms.openlocfilehash: f1415a1ddc49f10539915ccf0ce8f95ce7daf321
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051878"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Självstudier: Azure Active Directory-integrering med Mimecast personliga Portal

I den här självstudien får du lära dig hur du integrerar Mimecast personliga Portal med Azure Active Directory (AD Azure).

Integrera Mimecast personliga Portal med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till personliga Mimecast-portalen.
- Du kan aktivera användarna att automatiskt få loggat in på Mimecast personliga Portal (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Mimecast personliga Portal, behöver du följande objekt:

- En Azure AD-prenumeration
- En Mimecast personliga Portal enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Mimecast personliga portalen från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Att lägga till Mimecast personliga portalen från galleriet
Om du vill konfigurera integreringen av Mimecast personliga Portal till Azure AD, som du behöver lägga till Mimecast personliga portalen från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Mimecast personliga portalen från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Mimecast personliga Portal**väljer **Mimecast personliga Portal** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Mimecast personliga Portal i resultatlistan](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Mimecast personliga Portal baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Mimecast personliga Portal är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Mimecast personliga Portal upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med personliga Mimecast-portalen, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Mimecast personliga Portal](#create-a-mimecast-personal-portal-test-user)**  – du har en motsvarighet för Britta Simon i Mimecast personliga Portal som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för personliga Mimecast-portalen.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med personliga Mimecast-portalen:**

1. I Azure-portalen på den **Mimecast personliga Portal** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_samlbase.png)

3. På den **Mimecast personliga Portal domän och URL: er** avsnittet, utför följande steg:

    ![Mimecast personliga Portal domän och URL: er med enkel inloggning för information](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL: 

    | Region  |  Värde | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | USA   | `https://us-api.mimecast.com/login/saml`|
    | Sydafrika    | `https://za-api.mimecast.com/login/saml`|
    | Australien       | `https://au-api.mimecast.com/login/saml`|
    | Öppet hav        | `https://jer-api.mimecast.com/login/saml`|

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster:

    | Region  |  Värde | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | USA   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Sydafrika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australien       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Öppet hav        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. I den **svars-URL** textrutan anger du ett URL: 

    | Region  |  Värde | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | USA   | `https://us-api.mimecast.com/login/saml`|
    | Sydafrika    | `https://za-api.mimecast.com/login/saml`|
    | Australien       | `https://au-api.mimecast.com/login/saml`|
    | Öppet hav        | `https://jer-api.mimecast.com/login/saml`|
    
    > [!NOTE] 
    > ID-värde är inte verkliga. Uppdatera värdet med det faktiska ID: T. Kontakta [Mimecast personliga Portal klienten supportteamet](http://www.mimecast.com/customer-success/technical-support/) att hämta värdet. 

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/mimecast-personal-portal-tutorial/tutorial_general_400.png)

6. På den **Mimecast personliga Portal Configuration** klickar du på **konfigurera Mimecast personliga Portal** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Mimecast personliga Portalkonfiguration](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_configure.png) 

7. Logga in på din personliga Mimecast-Portal som administratör i ett annat webbläsarfönster.

8. Gå till **Services \> program**.
   
    ![Program](./media/mimecast-personal-portal-tutorial/ic794998.png "program")

9. Klicka på **autentisering profiler**.
   
    ![Autentisering profiler](./media/mimecast-personal-portal-tutorial/ic794999.png "autentisering profiler")

10. Klicka på **ny autentisering profil**.
   
    ![Ny autentisering profil](./media/mimecast-personal-portal-tutorial/ic795000.png "ny autentisering-profil")

11. I den **autentisering profil** avsnittet, utför följande steg:
   
    ![Autentisering profil](./media/mimecast-personal-portal-tutorial/ic795001.png "autentisering profil")
   
    a. I den **beskrivning** textrutan anger du ett namn för din konfiguration.
   
    b. Välj **framtvinga SAML-autentisering för Mimecast personliga Portal**.
   
    c. Som **Provider**väljer **Azure Active Directory**.
   
    d. I **utfärdar-URL** textrutan klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure-portalen.
   
    e. I **inloggnings-URL** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.
   
    f. I **URL för utloggning** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    g. Öppna din **Base64-** kodade certifikatet i anteckningar som hämtats från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den till den **identitetscertifikat Provider (Metadata)** textrutan.

    h. Välj **tillåta enkel inloggning på**.
   
    i. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/mimecast-personal-portal-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/mimecast-personal-portal-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/mimecast-personal-portal-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/mimecast-personal-portal-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-mimecast-personal-portal-test-user"></a>Skapa en testanvändare Mimecast personliga Portal

För att aktivera Azure AD-användare att logga in på Mimecast personliga portalen, måste de etableras i Mimecast personliga Portal. När det gäller Mimecast personliga Portal är etablering en manuell aktivitet.

Du måste registrera en domän innan du kan skapa användare.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på din **Mimecast personliga Portal** som administratör.

2. Gå till **kataloger \> interna**.
   
    ![Kataloger](./media/mimecast-personal-portal-tutorial/ic795003.png "kataloger")

3. Klicka på **registrera ny domän**.
   
    ![Registrera ny domän](./media/mimecast-personal-portal-tutorial/ic795004.png "registrera ny domän")

4. När du har skapat den nya domänen, klickar du på **nya adressen**.
   
    ![Ny adress](./media/mimecast-personal-portal-tutorial/ic795005.png "ny adress")

5. I dialogrutan Ny adress utför du följande steg på en giltig Azure AD-konto som du vill etablera:
   
    ![Spara](./media/mimecast-personal-portal-tutorial/ic795006.png "spara")
   
    a. I den **e-postadress** textrutan typ **e-postadress** användaren **BrittaSimon@contoso.com**.
    
    b. I den **globalt namn** textrutan skriver den **användarnamn** som **BrittaSimon**.

    c. I den **lösenord**, och **Bekräfta lösenord** textrutor, skriver den **lösenord** för användaren.
   
    b. Klicka på **Spara**.

>[!NOTE]
>Du kan använda andra verktyg för Mimecast personliga Portal användare konto att skapa eller API: er som tillhandahålls av Mimecast personliga Portal för att etablera användarkonton i Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till personliga Mimecast-portalen.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Mimecast personliga Portal, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Mimecast personliga Portal**.

    ![Mimecast personliga Portal-länken i programlistan](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Mimecast personliga Portal i åtkomstpanelen du bör få automatiskt loggat in på ditt Mimecast personliga Portal-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/mimecast-personal-portal-tutorial/tutorial_general_01.png
[2]: ./media/mimecast-personal-portal-tutorial/tutorial_general_02.png
[3]: ./media/mimecast-personal-portal-tutorial/tutorial_general_03.png
[4]: ./media/mimecast-personal-portal-tutorial/tutorial_general_04.png

[100]: ./media/mimecast-personal-portal-tutorial/tutorial_general_100.png

[200]: ./media/mimecast-personal-portal-tutorial/tutorial_general_200.png
[201]: ./media/mimecast-personal-portal-tutorial/tutorial_general_201.png
[202]: ./media/mimecast-personal-portal-tutorial/tutorial_general_202.png
[203]: ./media/mimecast-personal-portal-tutorial/tutorial_general_203.png

