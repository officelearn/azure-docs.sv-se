---
title: 'Självstudier: Azure Active Directory-integrering med skärmbild-O-till | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och skärmbild-O-till.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 941a02ef9ab3d623d34c8dc6996040f6d9e44859
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096851"
---
# <a name="tutorial-azure-active-directory-integration-with-screencast-o-matic"></a>Självstudier: Azure Active Directory-integrering med skärmbild-O – till

I den här självstudien får du lära dig hur du integrerar skärmbild-O-till med Azure Active Directory (AD Azure).

Integrera skärmbild-O-till med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till skärmbild-O-till.
- Du kan aktivera användarna att automatiskt få loggat in på skärmbild-O-till (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med skärmbild-O-till, behöver du följande objekt:

- En Azure AD-prenumeration
- En skärmbild-O-till enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till skärmbild-O-till från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Att lägga till skärmbild-O-till från galleriet
För att konfigurera integrering av skärmbild – O-till i Azure AD, som du behöver lägga till skärmbild-O-till från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till skärmbild-O-till från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **skärmbild-O-till**väljer **skärmbild-O-till** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Skärmbild-O-till i listan med resultat](./media/screencast-tutorial/tutorial_screencast_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med skärmbild-O-till baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i skärmbild-O-till är att en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i skärmbild-O-till upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med skärmbild-O-till, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en skärmbild-O-till testanvändare](#create-a-screencast-o-matic-test-user)**  – du har en motsvarighet för Britta Simon i skärmbild-O-till som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i skärmbild-O-till programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med skärmbild-O-till:**

1. I Azure-portalen på den **skärmbild-O-till** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/screencast-tutorial/tutorial_screencast_samlbase.png)

3. På den **skärmbild-O-till-domän och URL: er** avsnittet, utför följande steg:

    ![Skärmbild-O-till-domän och URL: er med enkel inloggning för information](./media/screencast-tutorial/tutorial_screencast_url.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE] 
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [skärmbild-O-till klienten supportteamet](mailto:support@screencast-o-matic.com) att hämta värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/screencast-tutorial/tutorial_screencast_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/screencast-tutorial/tutorial_general_400.png)

6. I ett annat webbläsarfönster, logga in på skärmbild-O-till som administratör.

7. Klicka på **prenumeration**.

    ![Prenumerationen](./media/screencast-tutorial/tutorial_screencast_sub.png)

8. Under **åtkomstsidan** avsnittet, klickar du på **installationsprogrammet**.

    ![Åtkomst](./media/screencast-tutorial/tutorial_screencast_setup.png)

9. På den **åtkomst installationssidan**, utför följande steg:

   * Under **URL: en för** Skriv din instansnamn i den angivna textrutan.

     ![Åtkomst](./media/screencast-tutorial/tutorial_screencast_access.png)

   * Välj **kräver domänanvändare** under **begränsning för SAML-användare (valfritt)** avsnittet.

   * Under **överför IDP XML-fil**, klickar du på **Välj fil** att ladda upp de metadata som du har hämtat från Azure-portalen.

   * Klicka på **OK**. 

     ![Åtkomst](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/screencast-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/screencast-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/screencast-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/screencast-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-screencast-o-matic-test-user"></a>Skapa en skärmbild-O-till testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i skärmbild-O-till. Skärmbild-O-till stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt skärmbild-O-till om det inte finns ännu.

>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [skärmbild-O-till klienten supportteamet](mailto:support@screencast-o-matic.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till skärmbild-O-till.

![Tilldela rollen][200] 

**Om du vill tilldela skärmbild-O-till Britta Simon, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **skärmbild-O-till**.

    ![Skärmbild-O-till länken i listan med program](./media/screencast-tutorial/tutorial_screencast_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen skärmbild-O-till åtkomstpanelen du bör få automatiskt loggat in på skärmbild-O-till programmet.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/screencast-tutorial/tutorial_general_01.png
[2]: ./media/screencast-tutorial/tutorial_general_02.png
[3]: ./media/screencast-tutorial/tutorial_general_03.png
[4]: ./media/screencast-tutorial/tutorial_general_04.png

[100]: ./media/screencast-tutorial/tutorial_general_100.png

[200]: ./media/screencast-tutorial/tutorial_general_200.png
[201]: ./media/screencast-tutorial/tutorial_general_201.png
[202]: ./media/screencast-tutorial/tutorial_general_202.png
[203]: ./media/screencast-tutorial/tutorial_general_203.png

