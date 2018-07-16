---
title: 'Självstudier: Azure Active Directory-integration med Vidyard | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jeedes
ms.openlocfilehash: d796ebf6e30476d766a0d9b6c78ba4b5cf577b47
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056233"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Självstudier: Azure Active Directory-integration med Vidyard

I den här självstudien får du lära dig hur du integrerar Vidyard med Azure Active Directory (AD Azure).

Integrera Vidyard med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Vidyard.
- Du kan aktivera användarna att automatiskt få loggat in på Vidyard (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Vidyard, behöver du följande objekt:

- En Azure AD-prenumeration
- En Vidyard enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Vidyard från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-vidyard-from-the-gallery"></a>Att lägga till Vidyard från galleriet
För att konfigurera integrering av Vidyard i Azure AD, som du behöver lägga till Vidyard från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Vidyard från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Vidyard**väljer **Vidyard** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Vidyard i resultatlistan](./media/vidyard-tutorial/tutorial_vidyard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Vidyard baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Vidyard är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Vidyard upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Vidyard, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Vidyard](#create-a-vidyard-test-user)**  – du har en motsvarighet för Britta Simon i Vidyard som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Vidyard program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Vidyard:**

1. I Azure-portalen på den **Vidyard** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/vidyard-tutorial/tutorial_vidyard_samlbase.png)

3. På den **Vidyard domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Vidyard domän och URL: er med enkel inloggning för information](./media/vidyard-tutorial/tutorial_vidyard_url2.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Vidyard domän och URL: er med enkel inloggning för information](./media/vidyard-tutorial/tutorial_vidyard_url1.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Dessa värden uppdateras med de faktiska identifierare, svars-URL och inloggnings-URL, vilket beskrivs senare i självstudien

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/vidyard-tutorial/tutorial_vidyard_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/vidyard-tutorial/tutorial_general_400.png)

7. På den **Vidyard Configuration** klickar du på **konfigurera Vidyard** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Vidyard konfiguration](./media/vidyard-tutorial/tutorial_vidyard_configure.png)

8. I ett annat webbläsarfönster logga du in på webbplatsen för företagets Vidyard programvara som en administratör.

9. Från instrumentpanelen Vidyard väljer **grupp** > **säkerhet**

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure1.png)

1. Klicka på **ny profil** fliken.

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure2.png)

11. I den **SAML-konfiguration** avsnittet, utför följande steg:

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure3.png)

    a. Ange allmänna profilnamn i den **profilnamn** textrutan.

    b. Kopiera **SSO-inloggning användarsidan** värde och klistra in den i **inloggnings-URL** -textrutan i **Vidyard domän och URL: er avsnittet** på Azure-portalen.

    c. Kopiera **ACS URL** värde och klistra in den i **svars-URL** -textrutan i **Vidyard domän och URL: er avsnittet** på Azure-portalen.

    d. Kopiera **utfärdare/Metadata-URL** värde och klistra in den i **identifierare** -textrutan i **Vidyard domän och URL: er avsnittet** på Azure-portalen.

    e. Öppna filen nedladdade certifikatet från Azure-portalen i anteckningar och klistra in den i den **X.509-certifikat** textrutan.

    f. I den **slutpunkts-URL för SAML** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL** kopieras från Azure-portalen.

    g. Klicka på **Bekräfta**.

12. Välj fliken Single Sign On **tilldela** bredvid en befintlig profil

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > När du har skapat en profil för enkel inloggning kan du tilldela den till alla grupper som användarna måste ha åtkomst via Azure. Om användaren inte finns i den grupp som de har tilldelats, Vidyard automatiskt skapa ett användarkonto och tilldela deras roll i realtid.

13. Välj Organisationsgrupp för din, som visas i den **grupper som är tillgängliga att distribuera**.

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure5.png)

14. Du kan se de tilldelade grupperna under den **för närvarande tilldelade grupper**. Välj en roll för gruppen enligt din organisation och klickar på **Bekräfta**.

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Mer information finns [det här dokumentet](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/vidyard-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/vidyard-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/vidyard-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/vidyard-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-vidyard-test-user"></a>Skapa en Vidyard testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Vidyard. Vidyard stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Vidyard om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Vidyard supportteamet](mailto:support@vidyard.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Vidyard.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Vidyard, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Vidyard**.

    ![Länken Vidyard i listan med program](./media/vidyard-tutorial/tutorial_vidyard_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Vidyard i åtkomstpanelen du bör få automatiskt loggat in på ditt Vidyard program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vidyard-tutorial/tutorial_general_01.png
[2]: ./media/vidyard-tutorial/tutorial_general_02.png
[3]: ./media/vidyard-tutorial/tutorial_general_03.png
[4]: ./media/vidyard-tutorial/tutorial_general_04.png

[100]: ./media/vidyard-tutorial/tutorial_general_100.png

[200]: ./media/vidyard-tutorial/tutorial_general_200.png
[201]: ./media/vidyard-tutorial/tutorial_general_201.png
[202]: ./media/vidyard-tutorial/tutorial_general_202.png
[203]: ./media/vidyard-tutorial/tutorial_general_203.png

