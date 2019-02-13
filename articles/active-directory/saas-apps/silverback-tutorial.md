---
title: 'Självstudier: Azure Active Directory-integrering med Silverback | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Silverback.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5614c061586c39e44f04f3542285e55e07f14d9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172725"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Självstudier: Azure Active Directory-integrering med Silverback

I den här självstudien får du lära dig hur du integrerar Silverback med Azure Active Directory (AD Azure).

Integrera Silverback med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Silverback.
- Du kan aktivera användarna att automatiskt få loggat in på Silverback (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Silverback, behöver du följande objekt:

- En Azure AD-prenumeration
- En aktiv prenumeration på Silverback

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Silverback från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-silverback-from-the-gallery"></a>Att lägga till Silverback från galleriet
För att konfigurera integrering av Silverback i Azure AD, som du behöver lägga till Silverback från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Silverback från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Silverback**väljer **Silverback** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Silverback i resultatlistan](./media/silverback-tutorial/tutorial_silverback_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Silverback baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Silverback är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Silverback upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Silverback, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare Silverback](#create-a-silverback-test-user)**  – du har en motsvarighet för Britta Simon i Silverback som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Silverback-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Silverback:**

1. I Azure-portalen på den **Silverback** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/silverback-tutorial/tutorial_silverback_samlbase.png)

3. På den **Silverback domän och URL: er** avsnittet, utför följande steg:

    ![Silverback domän och URL: er med enkel inloggning för information](./media/silverback-tutorial/tutorial_silverback_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<YOURSILVERBACKURL>.com/ssp`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `<YOURSILVERBACKURL>.com`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Silverback klienten supportteamet](mailto:helpdesk@matrix42.com) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Länk för nedladdning av certifikatet](./media/silverback-tutorial/tutorial_silverback_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/silverback-tutorial/tutorial_general_400.png)

6.  Logga in på Silverback servern som administratör och utför följande steg:

    a.  Gå till **Admin** > **autentiseringsprovider**.

    b. På den **autentiseringsinställningar för providern** utför följande steg:

    ![Administratören ](./media/silverback-tutorial/tutorial_silverback_admin.png)

    c.  Klicka på **importera från URL: en**.
    
    d.  Klistra in den kopierade Metadata-URL och klicka på **OK**.
    
    e.  Bekräfta med **OK** och sedan värden fylls i automatiskt.
    
    f.  Aktivera **visas på inloggningssidan**.
    
    g.  Aktivera **dynamisk Användargenereringen** om du vill lägga till av Azure AD som är auktoriserade användare automatiskt (valfritt).
    
    h.  Skapa en **rubrik** för knappen på självbetjäningsportalen.

    i.  Ladda upp en **ikonen** genom att klicka på **Välj fil**.
    
    j.  Välj bakgrunden **färg** för knappen.
    
    k.  Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/silverback-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/silverback-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/silverback-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/silverback-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-silverback-test-user"></a>Skapa en Silverback testanvändare

Om du vill aktivera Azure AD-användare att logga in på Silverback, måste de etableras i Silverback. I Silverback är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Silverback servern som administratör.

2. Gå till **användare** och **lägga till en ny enhet användare**.

3. På den **grundläggande** utför följande steg:

    ![Användaren ](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. I **användarnamn** text, ange namnet på användaren som **Britta**.

    b. I textrutan **Förnamn** anger du förnamnet på användaren som **Britta**.

    c. I **efternamn** text anger efternamn för användaren som **Simon**.

    d. I **e-postadress** text, ange den e-postadressen för användaren som **Brittasimon@contoso.com**.

    e. I den **lösenord** text, ange ditt lösenord.
    
    f. I den **Bekräfta lösenord** textrutan, skriva in lösenordet igen och bekräfta.

    g. Klicka på **Spara**.

>[!NOTE]
>Om du inte vill skapa varje användare manuellt aktivera den **dynamisk Användargenereringen** kryssrutan under **Admin** > **autentiseringsprovider**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Silverback.

![Tilldela rollen][200] 

**Om du vill tilldela Silverback Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Silverback**.

    ![Länken Silverback i listan med program](./media/silverback-tutorial/tutorial_silverback_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Silverback i åtkomstpanelen du bör få automatiskt loggat in på ditt Silverback-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silverback-tutorial/tutorial_general_01.png
[2]: ./media/silverback-tutorial/tutorial_general_02.png
[3]: ./media/silverback-tutorial/tutorial_general_03.png
[4]: ./media/silverback-tutorial/tutorial_general_04.png

[100]: ./media/silverback-tutorial/tutorial_general_100.png

[200]: ./media/silverback-tutorial/tutorial_general_200.png
[201]: ./media/silverback-tutorial/tutorial_general_201.png
[202]: ./media/silverback-tutorial/tutorial_general_202.png
[203]: ./media/silverback-tutorial/tutorial_general_203.png

