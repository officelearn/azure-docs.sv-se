---
title: 'Självstudier: Azure Active Directory-integrering med Ziflow | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175e678365016bafd3d18f590a5434c32ac9fadd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60798382"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Självstudier: Azure Active Directory-integrering med Ziflow

I den här självstudien får du lära dig hur du integrerar Ziflow med Azure Active Directory (AD Azure).

Integrera Ziflow med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Ziflow.
- Du kan aktivera användarna att automatiskt få loggat in på Ziflow (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Ziflow, behöver du följande objekt:

- En Azure AD-prenumeration
- En Ziflow enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Ziflow från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-ziflow-from-the-gallery"></a>Att lägga till Ziflow från galleriet
För att konfigurera integrering av Ziflow i Azure AD, som du behöver lägga till Ziflow från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Ziflow från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Ziflow**väljer **Ziflow** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Ziflow i resultatlistan](./media/ziflow-tutorial/tutorial_ziflow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Ziflow baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Ziflow är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Ziflow upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Ziflow, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare Ziflow](#create-a-ziflow-test-user)**  – du har en motsvarighet för Britta Simon i Ziflow som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Ziflow program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Ziflow:**

1. I Azure-portalen på den **Ziflow** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/ziflow-tutorial/tutorial_ziflow_samlbase.png)

3. På den **Ziflow domän och URL: er** avsnittet, utför följande steg:

    ![Ziflow domän och URL: er med enkel inloggning för information](./media/ziflow-tutorial/tutorial_ziflow_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Föregående värden är inte verkliga. Du uppdaterar det unika ID-värdet i identifierare och URL: en inloggning med faktiskt värde, som beskrivs senare i självstudien.

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/ziflow-tutorial/tutorial_ziflow_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/ziflow-tutorial/tutorial_general_400.png)

6. På den **Ziflow Configuration** klickar du på **konfigurera Ziflow** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Ziflow konfiguration](./media/ziflow-tutorial/tutorial_ziflow_configure.png) 

7. I ett annat webbläsarfönster, logga in på Ziflow som en administratör.

8. Klicka på Avatar i övre högra hörnet och klicka sedan på **Hantera konto**.

    ![Hantera Ziflow konfiguration](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

9. Längst upp till vänster klickar du på **enkel inloggning**.

    ![Ziflow Configuration inloggning](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

10. På sidan **Enkel inloggning** utför du följande steg:

    ![Ziflow Configuration enskild](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Välj **typ** som **SAML2.0**.

    b. I den **logga i URL: en** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    c. Ladda upp det Base64-kodade certifikatet som du har hämtat från Azure-portalen till den **X509 signeringscertifikat**.

    d. I den **Utloggning** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    e. Från den **konfigurationsinställningar för leverantören identifierare** avsnittet, kopiera det markerade unika ID-värdet och lägger till dem med identifierare och logga in på URL: en i den **Ziflow domän och URL: er avsnittet** på Azure-portalen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/ziflow-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/ziflow-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/ziflow-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/ziflow-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-ziflow-test-user"></a>Skapa en Ziflow testanvändare

Om du vill aktivera Azure AD-användare att logga in på Ziflow, måste de etableras i Ziflow. I Ziflow är etablering en manuell aktivitet.

Gör följande för att etablera ett användarkonto:

1. Logga in på Ziflow som en administratör.

2. Gå till **personer** längst upp.

    ![Ziflow Configuration personer](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Klicka på **Lägg till** och klicka sedan på **Lägg till användare**.

    ![Ziflow konfiguration att lägga till användaren](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. På den **lägga till en användare** popup-fönstret utför följande steg:

    ![Ziflow konfiguration att lägga till användaren](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. I **e-post** text, ange den e-postadressen för användaren som brittasimon@contoso.com.

    b. I **Förnamn** text, ange först namnet på användaren som Britta.

    c. I **efternamn** text anger efternamn för användaren som Simon.

    d. Välj din Ziflow roll.

    e. Klicka på **Lägg till 1 användare**.

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Ziflow.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Ziflow, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Ziflow**.

    ![Länken Ziflow i listan med program](./media/ziflow-tutorial/tutorial_ziflow_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Ziflow i åtkomstpanelen du bör få automatiskt loggat in på ditt Ziflow program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ziflow-tutorial/tutorial_general_01.png
[2]: ./media/ziflow-tutorial/tutorial_general_02.png
[3]: ./media/ziflow-tutorial/tutorial_general_03.png
[4]: ./media/ziflow-tutorial/tutorial_general_04.png

[100]: ./media/ziflow-tutorial/tutorial_general_100.png

[200]: ./media/ziflow-tutorial/tutorial_general_200.png
[201]: ./media/ziflow-tutorial/tutorial_general_201.png
[202]: ./media/ziflow-tutorial/tutorial_general_202.png
[203]: ./media/ziflow-tutorial/tutorial_general_203.png

