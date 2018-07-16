---
title: 'Självstudier: Azure Active Directory-integration med Zoho | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zoho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: c44eef96bc40e2ccfac9e5dceb106ed2e809dea2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055907"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Självstudier: Azure Active Directory-integration med Zoho

I den här självstudien får du lära dig hur du integrerar Zoho med Azure Active Directory (AD Azure).

Integrera Zoho med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Zoho.
- Du kan aktivera användarna att automatiskt få loggat in på Zoho (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Zoho, behöver du följande objekt:

- En Azure AD-prenumeration
- En Zoho enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Zoho från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-zoho-from-the-gallery"></a>Att lägga till Zoho från galleriet
För att konfigurera integrering av Zoho i Azure AD, som du behöver lägga till Zoho från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Zoho från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Zoho**väljer **Zoho** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Zoho i resultatlistan](./media/zoho-mail-tutorial/tutorial_zoho_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Zoho baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Zoho är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Zoho upprättas.

I Zoho, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Zoho, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Zoho](#create-a-zoho-test-user)**  – du har en motsvarighet för Britta Simon i Zoho som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Zoho program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Zoho:**

1. I Azure-portalen på den **Zoho** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/zoho-mail-tutorial/tutorial_zoho_samlbase.png)

3. På den **Zoho domän och URL: er** avsnittet, utför följande steg:

    ![Zoho domän och URL: er med enkel inloggning för information](./media/zoho-mail-tutorial/tutorial_zoho_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<company name>.zohomail.com`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Zoho klienten supportteamet](https://www.zoho.com/mail/contact.html) att hämta det här värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/zoho-mail-tutorial/tutorial_zoho_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/zoho-mail-tutorial/tutorial_general_400.png)

6. På den **Zoho Configuration** klickar du på **konfigurera Zoho** att öppna **konfigurera inloggning** fönster. Kopiera den **utloggning URL, ändra lösenord URL och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Zoho konfiguration](./media/zoho-mail-tutorial/tutorial_zoho_configure.png) 

7. Logga in på webbplatsen för företagets Zoho e-post som en administratör i ett annat webbläsarfönster.

8. Gå till den **Kontrollpanelen**.
   
    ![Kontrollpanelen](./media/zoho-mail-tutorial/ic789607.png "Kontrollpanelen")

9. Klicka på den **SAML-autentisering** fliken.
   
    ![SAML-autentisering](./media/zoho-mail-tutorial/ic789608.png "SAML-autentisering")

10. I den **SAML autentisering-information om** avsnittet, utför följande steg:
   
    ![Information om autentisering av SAML](./media/zoho-mail-tutorial/ic789609.png "SAML-autentisering-information")
   
    a. I den **inloggnings-URL** textrutan klistra in **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.
   
    b. I den **URL för utloggning** textrutan klistra in **URL: en för utloggning** som du har kopierat från Azure-portalen.
   
    c. I den **ändra lösenord URL** textrutan klistra in **ändra lösenord URL** som du har kopierat från Azure-portalen.
       
    d. Öppna din Base64-kodat certifikat som hämtats från Azure-portalen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **PublicKey** textrutan.
   
    e. Som **algoritmen**väljer **RSA**.
   
    f. Klicka på **OK**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/zoho-mail-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/zoho-mail-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/zoho-mail-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/zoho-mail-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-zoho-test-user"></a>Skapa en Zoho testanvändare

För att aktivera Azure AD-användare att logga in på Zoho e-post, måste de etableras i Zoho e-post. När det gäller Zoho e-post är etablering en manuell aktivitet.

> [!NOTE]
> Du kan använda alla andra Zoho e användare konto verktyg för att skapa eller API: er som tillhandahålls av Zoho e-post att etablera AAD-användarkonton.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Utför följande steg för att etablera ett användarkonto:

1. Logga in på din **Zoho e** företagets plats som administratör.

2. Gå till **Kontrollpanelen \> e-post och dokument**.

3. Gå till **användarinformation \> lägga till användare**.
   
    ![Lägg till användare](./media/zoho-mail-tutorial/ic789611.png "lägga till användare")

4. På den **lägga till användare** dialogrutan utför följande steg:
   
    ![Lägg till användare](./media/zoho-mail-tutorial/ic789612.png "lägga till användare")
   
    a. I den **Förnamn** textrutan Ange först namnet på användaren som **Britta**.

    b. I den **efternamn** textrutan typ efternamn för användaren som **Simon**.

    c. I den **e-post-ID** textrutan typ e-post-id för användaren som **brittasimon@contoso.com**.

    d. I den **lösenord** textrutan anger du lösenordet för användaren.
   
    e. Klicka på **OK**.  
      
    > [!NOTE]
    > Azure Active Directory-kontoinnehavare får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktiv.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Zoho.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Zoho, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Zoho**.

    ![Länken Zoho i listan med program](./media/zoho-mail-tutorial/tutorial_zoho_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Zoho i åtkomstpanelen du bör få automatiskt loggat in på ditt Zoho program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zoho-mail-tutorial/tutorial_general_01.png
[2]: ./media/zoho-mail-tutorial/tutorial_general_02.png
[3]: ./media/zoho-mail-tutorial/tutorial_general_03.png
[4]: ./media/zoho-mail-tutorial/tutorial_general_04.png

[100]: ./media/zoho-mail-tutorial/tutorial_general_100.png

[200]: ./media/zoho-mail-tutorial/tutorial_general_200.png
[201]: ./media/zoho-mail-tutorial/tutorial_general_201.png
[202]: ./media/zoho-mail-tutorial/tutorial_general_202.png
[203]: ./media/zoho-mail-tutorial/tutorial_general_203.png

