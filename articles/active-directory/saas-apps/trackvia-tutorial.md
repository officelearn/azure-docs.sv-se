---
title: 'Självstudier: Azure Active Directory-integrering med TrackVia | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TrackVia.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e7010023-bdda-4a19-a335-19904e75b813
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeedes
ms.openlocfilehash: a1f1346e363318c73e2cba6a6a356cc376a39eac
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36217967"
---
# <a name="tutorial-azure-active-directory-integration-with-trackvia"></a>Självstudier: Azure Active Directory-integrering med TrackVia

I kursen får lära du att integrera TrackVia med Azure Active Directory (AD Azure).

Integrera TrackVia med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TrackVia.
- Du kan aktivera användarna att automatiskt hämta loggat in på TrackVia (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med TrackVia, behöver du följande:

- En Azure AD-prenumeration
- En TrackVia enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till TrackVia från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-trackvia-from-the-gallery"></a>Att lägga till TrackVia från galleriet
Du måste lägga till TrackVia från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av TrackVia i Azure AD.

**Utför följande steg för att lägga till TrackVia från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **TrackVia**väljer **TrackVia** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![TrackVia i resultatlistan](./media/trackvia-tutorial/tutorial_trackvia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med TrackVia baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i TrackVia motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i TrackVia upprättas.

I TrackVia, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med TrackVia, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare TrackVia](#create-a-trackvia-test-user)**  – du har en motsvarighet för Britta Simon i TrackVia som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt TrackVia program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med TrackVia:**

1. I Azure-portalen på den **TrackVia** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/trackvia-tutorial/tutorial_trackvia_samlbase.png)

3. På den **TrackVia domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och TrackVia domän med enkel inloggning information](./media/trackvia-tutorial/tutorial_trackvia_url.png)

    I den **identifierare** textruta Skriv värdet: `TrackVia`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och TrackVia domän med enkel inloggning information](./media/trackvia-tutorial/tutorial_trackvia_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://companyname.trackvia.com`
     
    > [!NOTE] 
    > Inloggning URL-värdet är inte verkliga. Uppdatera det här värdet med det faktiska inloggnings-URL. Kontakta [TrackVia klienten supportteamet](mailto:support@trackvia.com) att hämta det här värdet.

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/trackvia-tutorial/tutorial_trackvia_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/trackvia-tutorial/tutorial_general_400.png)

7. På den **TrackVia Configuration** klickar du på **konfigurera TrackVia** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID** från den **Snabbreferens avsnitt.**

    ![TrackVia konfiguration](./media/trackvia-tutorial/tutorial_trackvia_configure.png)
    
8. I olika webbläsarfönster, inloggning till webbplatsen TrackVia företag som administratör.

9. Klicka på Trackvia **mitt konto** inställningar och väljer sedan **enkel inloggning** fliken, utför följande steg:

    ![TrackVia konfiguration](./media/trackvia-tutorial/configure1.png)

    a. I den **identitet providern enhets-ID** textruta klistra in **SAML enhets-ID** -värde som du har kopierat från Azure-portalen.

    b. Välj den **Välj fil** att överföra metadatafilen som du hämtade från Azure-portalen.

    c. Klicka på **Spara**

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/trackvia-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/trackvia-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/trackvia-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/trackvia-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-trackvia-test-user"></a>Skapa en testanvändare TrackVia

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i TrackVia. TrackVia stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt TrackVia om den inte finns.
>[!Note]
>Om du behöver skapa en användare manuellt Kontakta [TrackVia supportteamet](mailto:support@trackvia.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TrackVia.

![Tilldela rollen][200] 

**Om du vill tilldela TrackVia Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **TrackVia**.

    ![Länken TrackVia i listan med program](./media/trackvia-tutorial/tutorial_trackvia_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen TrackVia på åtkomstpanelen du bör få automatiskt loggat in på ditt TrackVia program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/trackvia-tutorial/tutorial_general_01.png
[2]: ./media/trackvia-tutorial/tutorial_general_02.png
[3]: ./media/trackvia-tutorial/tutorial_general_03.png
[4]: ./media/trackvia-tutorial/tutorial_general_04.png

[100]: ./media/trackvia-tutorial/tutorial_general_100.png

[200]: ./media/trackvia-tutorial/tutorial_general_200.png
[201]: ./media/trackvia-tutorial/tutorial_general_201.png
[202]: ./media/trackvia-tutorial/tutorial_general_202.png
[203]: ./media/trackvia-tutorial/tutorial_general_203.png
