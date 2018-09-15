---
title: 'Självstudier: Azure Active Directory-integration med dmarcian | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2018
ms.author: jeedes
ms.openlocfilehash: 677c40932a557b8a15a51b947794b4281801f65a
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45637655"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Självstudier: Azure Active Directory-integration med dmarcian

I den här självstudien får du lära dig hur du integrerar dmarcian med Azure Active Directory (AD Azure).

Integrera dmarcian med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till dmarcian.
- Du kan aktivera användarna att automatiskt få loggat in på dmarcian (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med dmarcian, behöver du följande objekt:

- En Azure AD-prenumeration
- En dmarcian enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till dmarcian från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-dmarcian-from-the-gallery"></a>Att lägga till dmarcian från galleriet
För att konfigurera integrering av dmarcian i Azure AD, som du behöver lägga till dmarcian från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till dmarcian från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **dmarcian**väljer **dmarcian** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![dmarcian i resultatlistan](./media/dmarcian-tutorial/tutorial_dmarcian_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med dmarcian baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i dmarcian är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i dmarcian upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med dmarcian, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare dmarcian](#create-a-dmarcian-test-user)**  – du har en motsvarighet för Britta Simon i dmarcian som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt dmarcian program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med dmarcian:**

1. I Azure-portalen på den **dmarcian** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/dmarcian-tutorial/tutorial_dmarcian_samlbase.png)

3. På den **dmarcian domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![dmarcian domän och URL: er enkel inloggning för information](./media/dmarcian-tutorial/tutorial_dmarcian_url.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster:
    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian.com-eu/sso/saml/<ACCOUNT_ID>/sp.xml ` |
    | `https://dmarcian.com-ap/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian.com-eu/login/<ACCOUNT_ID>/handle/ `|
    | `https://dmarcian.com-ap/login/<ACCOUNT_ID>/handle/`|

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![dmarcian domän och URL: er enkel inloggning för information](./media/dmarcian-tutorial/tutorial_dmarcian_url1.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian.com-eu/login/<ACCOUNT_ID>` |
    | `https://dmarcian.com-ap/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Dessa värden uppdateras med faktiska identifierare, svars-URL och inloggnings-URL som beskrivs senare i självstudien. 

5. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Länk för hämtning av certifikat](./media/dmarcian-tutorial/tutorial_dmarcian_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/dmarcian-tutorial/tutorial_general_400.png)
    
7. I ett annat webbläsarfönster, loggar du in dmarcian som en administratör.

8. Klicka på **profil** på övre högra hörnet och gå till **inställningar**.

    ![Inställningar ](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

9. Rulla nedåt och klicka på **enkel inloggning** och sedan klicka på **konfigurera**.

    ![Enda ](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

10. På den **SAML enkel inloggning** sidan den **Status** som **aktiverad** och utför följande steg:

    ![Autentiseringen ](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * Under **lägga till dmarcian i din identitetsprovider** klickar du på **kopia** att kopiera den **URL för Konsumenttjänst för försäkran** för din instans och klistra in den i  **Svars-URL** -textrutan i **dmarcian domän och URL: er avsnittet** på Azure-portalen.

    * Under **lägga till dmarcian i din identitetsprovider** klickar du på **kopia** att kopiera den **entitets-ID** för din instans och klistra in den i **identifierare**-textrutan i **dmarcian domän och URL: er avsnittet** på Azure-portalen.

    * Under **konfigurera autentisering** avsnittet i den **identitet providern Metadata** textrutan klistra in den **Appfederationsmetadata**, som du har kopierat från Azure-portalen.

    * Under **konfigurera autentisering** avsnittet i den **attribut** textrutan klistra in webbadressen `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    * Under **konfigurera inloggnings-URL** avsnittet, kopiera den **inloggnings-URL** för din instans och klistra in den i **inloggnings-URL** -textrutan i **dmarcian domän och URL: er avsnittet** på Azure-portalen.

        > [!Note]
        > Du kan ändra den **inloggnings-URL** enligt din organisation.

    * Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/dmarcian-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/dmarcian-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/dmarcian-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/dmarcian-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-dmarcian-test-user"></a>Skapa en dmarcian testanvändare

Om du vill aktivera Azure AD-användare att logga in på dmarcian, måste de etableras i dmarcian. I dmarcian är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på dmarcian som en administratör.

2. Klicka på **profil** på övre högra hörnet och gå till **hantera användare**.

    ![Användaren ](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. På höger sida av **SSO användare** klickar du på **Lägg till ny användare**.

    ![Lägg till användare ](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. På den **Lägg till ny användare** popup-fönstret utför följande steg:

    ![Den nya användaren ](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. I den **nya användarens e-postadress** textrutan Ange e-postadress för användaren som **brittasimon@contoso.com**.

    b. Om du vill ge administratörsrättigheter till användaren väljer **gör användaren administratör**.

    c. Klicka på **lägga till användare**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till dmarcian.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon dmarcian, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **dmarcian**.

    ![Länken dmarcian i listan med program](./media/dmarcian-tutorial/tutorial_dmarcian_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen dmarcian i åtkomstpanelen du bör få automatiskt loggat in på ditt dmarcian program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dmarcian-tutorial/tutorial_general_01.png
[2]: ./media/dmarcian-tutorial/tutorial_general_02.png
[3]: ./media/dmarcian-tutorial/tutorial_general_03.png
[4]: ./media/dmarcian-tutorial/tutorial_general_04.png

[100]: ./media/dmarcian-tutorial/tutorial_general_100.png

[200]: ./media/dmarcian-tutorial/tutorial_general_200.png
[201]: ./media/dmarcian-tutorial/tutorial_general_201.png
[202]: ./media/dmarcian-tutorial/tutorial_general_202.png
[203]: ./media/dmarcian-tutorial/tutorial_general_203.png

