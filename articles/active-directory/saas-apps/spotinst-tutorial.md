---
title: 'Självstudier: Azure Active Directory-integrering med Spotinst | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 679284783ac93662c1c96e813f9f028fffb434f0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173665"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>Självstudier: Azure Active Directory-integrering med Spotinst

I den här självstudien får du lära dig hur du integrerar Spotinst med Azure Active Directory (AD Azure).

Integrera Spotinst med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Spotinst.
- Du kan aktivera användarna att automatiskt få loggat in på Spotinst (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Spotinst, behöver du följande objekt:

- En Azure AD-prenumeration
- En Spotinst enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Spotinst från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-spotinst-from-the-gallery"></a>Att lägga till Spotinst från galleriet
För att konfigurera integrering av Spotinst i Azure AD, som du behöver lägga till Spotinst från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Spotinst från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Spotinst**väljer **Spotinst** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Spotinst i resultatlistan](./media/spotinst-tutorial/tutorial_spotinst_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Spotinst baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Spotinst är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Spotinst upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Spotinst, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare Spotinst](#create-a-spotinst-test-user)**  – du har en motsvarighet för Britta Simon i Spotinst som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Spotinst program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Spotinst:**

1. I Azure-portalen på den **Spotinst** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/spotinst-tutorial/tutorial_spotinst_samlbase.png)

3. På den **Spotinst domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i IDP-initierad läge:

    ![Spotinst domän och URL: er med enkel inloggning för information](./media/spotinst-tutorial/tutorial_spotinst_url1.png)

    a. Kontrollera **visa avancerade URL-inställningar**.

    b. I den **Vidarebefordransstatus** textrutan, ange ett värde: `<ID>`

    c. Om du vill konfigurera programmet i **SP** har initierat läge, den **inloggnings-URL** textrutan anger du URL: `https://console.spotinst.com`

    > [!NOTE]
    > Vidarebefordransstatus-värdet är inte verkliga. Vidarebefordransstatus värdet uppdateras med det faktiska värdet Vidarebefordransstatus beskrivs senare i självstudien.

4. Spotinst program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. Följande skärmbild visar ett exempel för den.

    ![Konfigurera enkel inloggning](./media/spotinst-tutorial/tutorial_Spotinst_attribute.png)

5. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:

    | Attributnamn | Attributvärde |
    | ---------------| --------------- |
    | E-post | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/spotinst-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/spotinst-tutorial/tutorial_attribute_05.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna **Namnrymd** tom.

    e. Klicka på **Ok**

6. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/spotinst-tutorial/tutorial_spotinst_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/spotinst-tutorial/tutorial_general_400.png)

8. I ett annat webbläsarfönster, logga in på Spotinst som en administratör.

9. Klicka på den **Användarikon** på upp till höger på skärmen och klicka på **inställningar**.

    ![Spotinst inställningar](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

10. Klicka på den **SECURITY** fliken längst upp och välj sedan **Identitetsprovidrar** och utför följande steg:

    ![Spotinst säkerhet](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Kopiera den **Vidarebefordransstatus** för din instans och klistra in den i **Vidarebefordransstatus** -textrutan i **Spotinst domän och URL: er** avsnittet på Azure-portalen.

    b. Klicka på **Bläddra** att ladda upp den xml-fil som du har hämtat från Azure-portalen

    c. Klicka på **SPARA**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/spotinst-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/spotinst-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/spotinst-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/spotinst-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-spotinst-test-user"></a>Skapa en Spotinst testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Spotinst.

1. Om du har konfigurerat programmet i den **SP** intiated läge, utför följande steg:

   a. I ett annat webbläsarfönster, logga in på Spotinst som en administratör.

   b. Klicka på den **Användarikon** på upp till höger på skärmen och klicka på **inställningar**.

    ![Spotinst inställningar](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Klicka på **användare** och välj **Lägg till användare**.

    ![Spotinst inställningar](./media/spotinst-tutorial/adduser1.png)

    d. Utför följande steg på avsnittet Lägg till användare:

    ![Spotinst inställningar](./media/spotinst-tutorial/adduser2.png)

    * I den **fullständigt namn** textrutan anger du det fullständiga namnet på användaren som **BrittaSimon**.

    * I den **e-post** textrutan anger du e-postadressen för användaren som **brittasimon@contoso.com**.

    * Välj din specifika information för den **konton, Kontorollen och organisation rollen**.

2. Om du har konfigurerat programmet i den **IDP** intiated läge, där är ingen uppgift åt dig i det här avsnittet. Spotinst stöder just-in-time-etablering, vilket är som standard aktiverat. En ny användare har skapats under ett försök att komma åt Spotinst om det inte finns ännu.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Spotinst.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Spotinst, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Spotinst**.

    ![Länken Spotinst i listan med program](./media/spotinst-tutorial/tutorial_spotinst_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Spotinst i åtkomstpanelen du bör få automatiskt loggat in på ditt Spotinst program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/spotinst-tutorial/tutorial_general_01.png
[2]: ./media/spotinst-tutorial/tutorial_general_02.png
[3]: ./media/spotinst-tutorial/tutorial_general_03.png
[4]: ./media/spotinst-tutorial/tutorial_general_04.png

[100]: ./media/spotinst-tutorial/tutorial_general_100.png

[200]: ./media/spotinst-tutorial/tutorial_general_200.png
[201]: ./media/spotinst-tutorial/tutorial_general_201.png
[202]: ./media/spotinst-tutorial/tutorial_general_202.png
[203]: ./media/spotinst-tutorial/tutorial_general_203.png

