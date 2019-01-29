---
title: 'Självstudier: Azure Active Directory-integrering med Elium | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: jeedes
ms.openlocfilehash: ac88d3accad8aa9ec523ecce5eb11b6c10eb913c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181494"
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>Självstudier: Azure Active Directory-integrering med Elium

I den här självstudien får du lära dig hur du integrerar Elium med Azure Active Directory (AD Azure).

Integrera Elium med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Elium.
- Du kan aktivera användarna att automatiskt få loggat in på Elium (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Elium, behöver du följande objekt:

- En Azure AD-prenumeration
- En Elium enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Elium från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-elium-from-the-gallery"></a>Att lägga till Elium från galleriet
För att konfigurera integrering av Elium i Azure AD, som du behöver lägga till Elium från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Elium från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Elium**väljer **Elium** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Elium i resultatlistan](./media/elium-tutorial/tutorial_elium_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Elium baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Elium är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Elium upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Elium, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Elium](#create-an-elium-test-user)**  – du har en motsvarighet för Britta Simon i Elium som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Elium program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Elium:**

1. I Azure-portalen på den **Elium** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/elium-tutorial/tutorial_elium_samlbase.png)

1. På den **Elium domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Elium domän och URL: er med enkel inloggning för information](./media/elium-tutorial/tutorial_elium_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Elium domän och URL: er med enkel inloggning för information](./media/elium-tutorial/tutorial_elium_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: ` https://<platform-domain>.elium.com/login/saml2/login`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Du får dessa värden från den **SP metadatafil** laddas ner `https://<platform-domain>.elium.com/login/saml2/metadata`, som beskrivs senare i den här självstudien.

1. Elium programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program.

    ![Konfigurera enkel inloggning](./media/elium-tutorial/tutorial_attribute.png)

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i föregående bild och utför följande steg:
           
    | Attributnamn | Attributvärde |   
    | ---------------| ----------------|
    | e-post   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| User.jobtitle|
    | Företagets| user.companyname|
    
    > [!NOTE]
    > Det här är standard-anspråk. **Endast e-anspråk krävs**. Anspråk är obligatoriskt för JIT etablering också bara e-post. Andra anpassade anspråk kan variera från plattform som en kund till en annan kund-plattform.

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/elium-tutorial/tutorial_attribute_04.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    ![Konfigurera enkel inloggning](./media/elium-tutorial/tutorial_attribute_05.png)

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna namnområde som tomt.
    
    e. Klicka på **OK**. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/elium-tutorial/tutorial_elium_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/elium-tutorial/tutorial_general_400.png)
    
1. I ett annat webbläsarfönster logga du in på webbplatsen Elium företag som administratör.

1. Klicka på den **användarprofil** övre högra hörnet och välj sedan **Administration**.

    ![Konfigurera enkel inloggning](./media/elium-tutorial/user1.png)

1. Välj fliken **Säkerhet**.

    ![Konfigurera enkel inloggning](./media/elium-tutorial/user2.png)

1. Rulla ned till den **enkel inloggning (SSO)** avsnittet och utför följande steg:

    ![Konfigurera enkel inloggning](./media/elium-tutorial/user3.png)

    a. Kopiera värdet för **Kontrollera SAML2 autentiseringen fungerar med ditt konto** och klistra in den i den **inloggnings-URL** textrutan på den **Elium domän och URL: er** avsnitt i Azure portalen.

    > [!NOTE]
    > När du konfigurerar enkel inloggning kan visa du alltid standardsidan för fjärrinloggning på följande URL: `https://<platform_domain>/login/regular/login` 

    b. Välj **aktivera SAML2 federation** kryssrutan.

    c. Välj **JIT etablering** kryssrutan.

    d. Öppna den **SP Metadata** genom att klicka på den **hämta** knappen.

    e. Sök efter den **entityID** i den **SP Metadata** fil, kopiera den **entityID** och klistra in den i den **identifierare** textrutan på  **Elium domän och URL: er** avsnitt i Azure-portalen. 

    ![Konfigurera enkel inloggning](./media/elium-tutorial/user4.png)

    f. Sök efter den **AssertionConsumerService** i den **SP Metadata** fil, kopiera den **plats** och klistra in den i den **svars-URL** textrutan på den **Elium domän och URL: er** avsnitt i Azure-portalen.

    ![Konfigurera enkel inloggning](./media/elium-tutorial/user5.png)

    g. Öppna metadatafilen hämtade från Azure-portalen i anteckningar, kopiera innehållet och klistra in den i den **IdP Metadata** textrutan.

    h. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/elium-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/elium-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/elium-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/elium-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-elium-test-user"></a>Skapa en Elium testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Elium. Elium stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Elium om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Elium supportteamet](mailto:support@elium.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Elium.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Elium, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Elium**.

    ![Länken Elium i listan med program](./media/elium-tutorial/tutorial_elium_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Elium i åtkomstpanelen du bör få automatiskt loggat in på ditt Elium program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/elium-tutorial/tutorial_general_01.png
[2]: ./media/elium-tutorial/tutorial_general_02.png
[3]: ./media/elium-tutorial/tutorial_general_03.png
[4]: ./media/elium-tutorial/tutorial_general_04.png

[100]: ./media/elium-tutorial/tutorial_general_100.png

[200]: ./media/elium-tutorial/tutorial_general_200.png
[201]: ./media/elium-tutorial/tutorial_general_201.png
[202]: ./media/elium-tutorial/tutorial_general_202.png
[203]: ./media/elium-tutorial/tutorial_general_203.png

