---
title: 'Självstudier: Azure Active Directory-integration med Bomgar fjärrsupport | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bomgar fjärrsupport.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 193b163f-bdee-4974-b16d-777c51b991df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: jeedes
ms.openlocfilehash: c59f4291726b24b7c96bb60d0497c1578a3e4b0f
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048189"
---
# <a name="tutorial-azure-active-directory-integration-with-bomgar-remote-support"></a>Självstudier: Azure Active Directory-integration med Bomgar fjärrsupport

I den här självstudien får du lära dig hur du integrerar Bomgar Fjärrsupport med Azure Active Directory (AD Azure).

Integrera Bomgar Fjärrsupport med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Bomgar fjärrsupport.
- Du kan aktivera användarna att automatiskt få loggat in på Bomgar fjärrsupport (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Bomgar fjärrsupport, behöver du följande objekt:

- En Azure AD-prenumeration
- En Bomgar fjärrsupport enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Bomgar fjärrsupport från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-bomgar-remote-support-from-the-gallery"></a>Att lägga till Bomgar fjärrsupport från galleriet
Om du vill konfigurera integreringen av Bomgar fjärrsupport till Azure AD, som du behöver lägga till Bomgar fjärrsupport från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Bomgar fjärrsupport från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Bomgar fjärrsupport**väljer **Bomgar fjärrsupport** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Bomgar fjärrsupport i resultatlistan](./media/bomgarremotesupport-tutorial/tutorial_bomgarremotesupport_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Bomgar fjärrsupport baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Bomgar fjärrsupport till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Bomgar fjärrsupport upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Bomgar fjärrsupport, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Bomgar fjärrsupport](#create-a-bomgar-remote-support-test-user)**  – du har en motsvarighet för Britta Simon i Bomgar fjärrsupport som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Bomgar fjärrsupport program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Bomgar fjärrsupport:**

1. I Azure-portalen på den **Bomgar fjärrsupport** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/bomgarremotesupport-tutorial/tutorial_bomgarremotesupport_samlbase.png)

3. På den **Bomgar Remote Support domän och URL: er** avsnittet, utför följande steg:

    ![Bomgar Remote Support domän och URL: er med enkel inloggning för information](./media/bomgarremotesupport-tutorial/tutorial_bomgarremotesupport_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<SUBDOMAIN>.trafficmanager.net/saml`

    b. I den **identifierare (entitets-ID)** textrutan anger du ett URL med hjälp av följande mönster: `https://<SUBDOMAIN>.trafficmanager.net`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare (entitets-ID). Kontakta [Bomgar stöd för fjärrklient supportteamet](https://www.bomgar.com/docs/index.htm#support) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/bomgarremotesupport-tutorial/tutorial_bomgarremotesupport_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/bomgarremotesupport-tutorial/tutorial_general_400.png)

6. Att konfigurera enkel inloggning på **Bomgar Remote stöder** sida, som du behöver skicka de hämtade **XML-Metadata för** till [Bomgar Remote stöder supportteamet](https://www.bomgar.com/docs/index.htm#support). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/bomgarremotesupport-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/bomgarremotesupport-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/bomgarremotesupport-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/bomgarremotesupport-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-bomgar-remote-support-test-user"></a>Skapa en Bomgar fjärrsupport testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Bomgar fjärrsupport. Fjärrsupport för Bomgar stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Bomgar fjärrsupport om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Bomgar Remote stöder supportteamet](https://www.bomgar.com/docs/index.htm#support).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Bomgar fjärrsupport.

![Tilldela rollen][200] 

**Om du vill tilldela Bomgar fjärrsupport Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Bomgar fjärrsupport**.

    ![Länken Bomgar fjärrsupport i listan med program](./media/bomgarremotesupport-tutorial/tutorial_bomgarremotesupport_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Bomgar fjärrsupport i åtkomstpanelen du bör få automatiskt loggat in på ditt Bomgar fjärrsupport-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bomgarremotesupport-tutorial/tutorial_general_01.png
[2]: ./media/bomgarremotesupport-tutorial/tutorial_general_02.png
[3]: ./media/bomgarremotesupport-tutorial/tutorial_general_03.png
[4]: ./media/bomgarremotesupport-tutorial/tutorial_general_04.png

[100]: ./media/bomgarremotesupport-tutorial/tutorial_general_100.png

[200]: ./media/bomgarremotesupport-tutorial/tutorial_general_200.png
[201]: ./media/bomgarremotesupport-tutorial/tutorial_general_201.png
[202]: ./media/bomgarremotesupport-tutorial/tutorial_general_202.png
[203]: ./media/bomgarremotesupport-tutorial/tutorial_general_203.png

