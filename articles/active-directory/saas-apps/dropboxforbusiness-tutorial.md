---
title: 'Självstudier: Azure Active Directory-integration med Dropbox för företag | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: jeedes
ms.openlocfilehash: eadf6724891d348c2ea3654bcf19ef0d74078049
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42055915"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Självstudier: Azure Active Directory-integration med Dropbox for Business

I den här självstudien får du lära dig hur du integrerar Dropbox för företag med Azure Active Directory (AD Azure).

Integrera Dropbox för företag med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Dropbox för företag.
- Du kan aktivera användarna att automatiskt få loggat in på Dropbox for Business (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Dropbox for Business, behöver du följande objekt:

- En Azure AD-prenumeration
- En Dropbox for Business enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Dropbox för företag från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Att lägga till Dropbox för företag från galleriet

Om du vill konfigurera integreringen av Dropbox för företag i Azure AD, som du behöver lägga till Dropbox för företag från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Dropbox för företag från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Dropbox for Business**väljer **Dropbox for Business** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Dropbox for Business i listan med resultat](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Dropbox for Business baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Dropbox för företag till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Dropbox för företag upprättas.

I Dropbox för företag, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Dropbox för företag, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en Dropbox for Business testanvändare](#create-a-dropbox-for-business-test-user)**  – du har en motsvarighet för Britta Simon i Dropbox för företag som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din Dropbox för affärsprogram.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Dropbox för företag:**

1. I Azure-portalen på den **Dropbox for Business** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_samlbase.png)

3. På den **Dropbox företagsdomänen och URL: er** avsnittet, utför följande steg:

    ![Dropbox företagsdomänen och URL: er enkel inloggning för information](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_url1.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://www.dropbox.com/sso/<id>`

    b. I den **identifierare** textrutan, ange ett värde: `Dropbox`

    > [!NOTE]
    > Föregående inloggnings-URL-värdet är inte verkliga värdet. Du ska uppdatera värdet med faktiska inloggnings-URL: en, som beskrivs senare i självstudien.

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/dropboxforbusiness-tutorial/tutorial_general_400.png)

6. På den **Dropbox för företag som** klickar du på **konfigurera Dropbox for Business** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Dropbox för företag](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_configure.png) 

7. Att konfigurera enkel inloggning på **Dropbox for Business** sida finns på din Dropbox för företag-klient och logga på din Dropbox för företag-klient.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/ic769509.png "Konfigurera enkel inloggning")

8. Klicka på den **Användarikon** och välj **inställningar** fliken.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure1.png "Konfigurera enkel inloggning")

9. I navigeringsfönstret till vänster klickar du på **administratörskonsolen**.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure2.png "Konfigurera enkel inloggning")

10. På den **administratörskonsolen**, klickar du på **inställningar** i det vänstra navigeringsfönstret.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure3.png "Konfigurera enkel inloggning")

11. Välj **enkel inloggning** alternativ den **autentisering** avsnittet.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure4.png "Konfigurera enkel inloggning")

12. I den **enkel inloggning** avsnittet, utför följande steg:  

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure5.png "Konfigurera enkel inloggning")

    a. Välj **krävs** som ett alternativ i listrutan för den **enkel inloggning**.

    b. Klicka på **Lägg till inloggning URL** och i den **inloggning URL för identitetsprovider** textrutan klistra in den **SAML enkel inloggning för tjänst-URL** värde som du har kopierat från Azure portal Välj sedan **klar**.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure6.png "Konfigurera enkel inloggning")

    c. Klicka på **överför certifikat**, och bläddra till din **Base64-kodad certifikatfilen** som du har hämtat från Azure-portalen.

    d. Klicka på **Kopiera länk** och klistra in det kopierade värdet till den **inloggnings-URL** textrutan av **Dropbox företagsdomänen och URL: er** avsnittet på Azure-portalen.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/dropboxforbusiness-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/dropboxforbusiness-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/dropboxforbusiness-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/dropboxforbusiness-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-dropbox-for-business-test-user"></a>Skapa en Dropbox for Business testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i Dropbox för företag. Dropbox for Business har stöd för just-in-time-etablering, som är aktiverat som standard.

Det finns inga uppgift åt dig i det här avsnittet. Om en användare inte redan finns i Dropbox för företag, skapas en ny när du försöker få åtkomst till Dropbox för företag.

>[!Note]
>Om du vill skapa en användare manuellt, kontakta [Dropbox för Business Client-supportteamet](https://www.dropbox.com/business/contact) 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Dropbox för företag.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Dropbox for Business, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Dropbox for Business**.

    ![Dropbox for Business-länk i listan med program](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Dropbox för företag-panel i åtkomstpanelen, bör du få inloggningssidan i din Dropbox för affärsprogram.
 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dropboxforbusiness-tutorial/tutorial_general_01.png
[2]: ./media/dropboxforbusiness-tutorial/tutorial_general_02.png
[3]: ./media/dropboxforbusiness-tutorial/tutorial_general_03.png
[4]: ./media/dropboxforbusiness-tutorial/tutorial_general_04.png

[100]: ./media/dropboxforbusiness-tutorial/tutorial_general_100.png

[200]: ./media/dropboxforbusiness-tutorial/tutorial_general_200.png
[201]: ./media/dropboxforbusiness-tutorial/tutorial_general_201.png
[202]: ./media/dropboxforbusiness-tutorial/tutorial_general_202.png
[203]: ./media/dropboxforbusiness-tutorial/tutorial_general_203.png

